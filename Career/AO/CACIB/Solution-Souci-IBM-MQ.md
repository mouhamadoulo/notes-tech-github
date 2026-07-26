Pour déployer vos conteneurs (notamment IBM MQ qui nécessite une architecture compatible x86) sur une machine virtuelle gratuite d'Oracle Cloud (OCI), voici les étapes pas à pas, de la création du serveur jusqu'au lancement de votre projet.

---

### Étape 1 : Créer le compte Oracle Cloud

1. Rendez-vous sur la [page d'inscription d'Oracle Cloud Free Tier](https://www.oracle.com/fr/cloud/free/).
2. Remplissez vos informations. Une **carte bancaire** est obligatoire pour valider votre identité (Oracle effectue une vérification temporaire d'un montant minime qui est annulée ensuite, vous ne serez pas débité tant que vous restez dans les quotas gratuits).

### Étape 2 : Créer l'instance de calcul (Serveur Virtuel)

Une fois connecté à la console Oracle Cloud :

1. Dans le menu de gauche (le menu hamburger ☰), allez sur **Compute** (Compute) puis **Instances**.
2. Cliquez sur **Create instance** (Créer une instance).
3. **Nom de l'instance :** Donnez un nom (ex: `payment-server`).
4. **Image and Shape (Image et forme) :** Cliquez sur *Edit* :
* **Image :** Choisissez **Ubuntu** (ex: Ubuntu 24.04 ou version récente recommandée).
* **Shape (Forme) :** C'est l'étape critique pour éviter le problème d'IBM MQ. Ne prenez pas ARM, choisissez une forme **AMD** de type `VM.Standard.E2.1.Micro` (marquée comme *Always Free-eligible*). *Note : Cette instance micro possède 1 Go de RAM, ce qui est un peu juste si vous faites tourner Postgres + PgAdmin + IBM MQ en même temps. Si vous avez besoin de plus de puissance et que vous créez une instance ARM (Ampere), gardez à l'esprit qu'IBM MQ risquera de reposer sur de l'émulation, mais sur une instance AMD x86 classique, le processeur nativement compatible x86 exécutera IBM MQ sans broncher.*


5. **Networking (Réseau) :** Laissez par défaut la création d'un nouveau VCN (Virtual Cloud Network) et d'un sous-réseau. Assurez-vous que l'option **Assign a public IPv4 address** (Attribuer une adresse IPv4 publique) est bien cochée.
6. **Add SSH keys (Ajouter des clés SSH) :**
* Sélectionnez *Save private key* pour télécharger une paire de clés SSH générée par Oracle (ex: `oci-key.key`), ou insérez votre propre clé publique si vous en avez une. *Conservez bien cette clé, elle vous permettra de vous connecter au serveur.*


7. Cliquez sur **Create** en bas de page. Attendez 1 à 2 minutes que l'état de l'instance passe à *Running* (En cours d'exécution).

### Étape 3 : Configurer le pare-feu réseau (Ports Docker)

Par défaut, Oracle bloque les ports entrants. Pour accéder à vos services (comme PgAdmin sur le port 5050 ou IBM MQ sur 1414/9443), il faut ouvrir les ports dans le pare-feu cloud :

1. Sur la page de votre instance, cliquez sur votre **Subnet** (Sous-réseau) dans la section Réseau.
2. Cliquez sur la **Default Security List** (Liste de sécurité par défaut).
3. Cliquez sur **Add Ingress Rules** (Ajouter des règles de traçabilité entrante) :
* **CIDR source :** `0.0.0.0/0`
* **Protocole IP :** `TCP`
* **Plage de ports de destination :** `1414, 5050, 9443, 5432` (ou ajoutez les ports dont vous avez besoin).


4. Cliquez sur *Add Ingress Rules*.

### Étape 4 : Se connecter au serveur distant en SSH

Récupérez l'**Adresse IP publique** de votre instance depuis le tableau de bord Oracle.
Depuis votre terminal local (sur votre PC Windows ARM), connectez-vous en SSH (adaptez le chemin de votre clé et l'adresse IP) :

```bash
ssh -i "chemin/vers/votre/cle.key" ubuntu@<adresse-ip-publique>

```

*(Si l'accès est refusé, ajustez les permissions de la clé sur Linux/Git Bash avec `chmod 400 chemin/vers/votre/cle.key`).*

### Étape 5 : Installer Docker et Docker Compose sur le serveur Oracle

Une fois connecté en SSH sur votre machine Ubuntu distante, installez Docker proprement :

```bash
# Mettre à jour les paquets
sudo apt update && sudo apt upgrade -y

# Installer Docker
sudo apt install docker.io -y

# Démarrer et activer Docker
sudo systemctl start docker
sudo systemctl enable docker

# Ajouter votre utilisateur au groupe docker pour éviter de taper 'sudo' à chaque fois
sudo usermod -aG docker $USER

# Installer Docker Compose
sudo apt install docker-compose-v2 -y

```

*(Déconnectez-vous puis reconnectez-vous en SSH (`exit` puis relancez la commande `ssh`) pour que les droits du groupe docker soient pris en compte).*

### Étape 6 : Déployer vos conteneurs

1. Sur le serveur distant, créez un dossier pour votre projet :
```bash
mkdir payment-app && cd payment-app

```


2. Créez votre fichier `docker-compose.yaml` :
```bash
nano docker-compose.yaml

```


3. Collez votre configuration (avec l'image officielle d'IBM MQ qui fonctionnera enfin puisque vous êtes sur une architecture x86_64) :
```yaml
services:
  postgres:
    image: postgres:18
    container_name: payment-postgres
    restart: unless-stopped
    environment:
      POSTGRES_DB: payment_messages
      POSTGRES_USER: payment
      POSTGRES_PASSWORD: payment
    ports:
      - "5432:5432"
    volumes:
      - postgres_data:/var/lib/postgresql

  pgadmin:
    image: dpage/pgadmin4:latest
    container_name: payment-pgadmin
    restart: unless-stopped
    environment:
      PGADMIN_DEFAULT_EMAIL: admin@payment.com
      PGADMIN_DEFAULT_PASSWORD: admin
    ports:
      - "5050:80"

  ibm-mq:
    image: icr.io/ibm-messaging/mq:latest
    container_name: payment-mq
    restart: unless-stopped
    environment:
      LICENSE: accept
      MQ_QMGR_NAME: QM1
      MQ_APP_PASSWORD: passw0rd
    ports:
      - "1414:1414"
      - "9443:9443"
    volumes:
      - mq_data:/mnt/mqm

volumes:
  postgres_data:
  mq_data:

```


*(Sauvegardez avec `Ctrl+O`, puis `Entrée`, et quittez avec `Ctrl+X`).*
4. Lancez vos conteneurs en arrière-plan :
```bash
docker compose up -d

```



Vous pouvez désormais suivre les logs avec `docker compose logs -f payment-mq` : l'initialisation d'IBM MQ va s'effectuer sans aucune erreur d'architecture, et vos services seront accessibles via l'adresse IP publique de votre instance Oracle Cloud.
