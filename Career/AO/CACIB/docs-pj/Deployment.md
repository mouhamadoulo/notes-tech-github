# Déploiement

Procédure de mise en production de `payment-messages` : sur un VPS cloud (Oracle Cloud pris
comme exemple), puis alternatives hors cloud.

Le dépôt contient déjà tout ce qu'il faut pour construire les images (`backend/Dockerfile`,
`frontend/Dockerfile`) et orchestrer la pile (`docker-compose.yaml`). Ce document part de là et
décrit ce qu'il faut **ajouter ou changer** pour un environnement exposé.

---

## 1. Ce qu'on déploie

Cinq services, dont un seul doit être joignable depuis Internet.

| Service | Image | Rôle | Exposition publique |
|---|---|---|---|
| `frontend` | build local → nginx 1.27-alpine | SPA Angular + relais `/api/` vers le backend | **oui** (80/443) |
| `backend` | build local → eclipse-temurin:21-jre | API REST + consommateur JMS | non |
| `postgres` | postgres:18 | Persistance | non |
| `ibm-mq` | icr.io/ibm-messaging/mq | Broker de messages | non |
| `pgadmin` | dpage/pgadmin4 | Confort de dev | **à supprimer** |

Le conteneur `frontend` relaie `/api/` vers `backend:8080` par le DNS interne Docker : le
navigateur ne voit qu'une seule origine. C'est ce qui rend possible la CSP stricte
(`connect-src 'self'`) de `frontend/security-headers.conf`.

Deux volumes portent l'état : `postgres_data` et `mq_data`. Tout le reste est jetable.

---

## 2. Deux contraintes à régler avant de choisir la machine

### 2.1 L'image IBM MQ n'existe pas en ARM64

Vérifié sur le registre :

```bash
# Architectures publiées pour icr.io/ibm-messaging/mq
TOKEN=$(curl -s "https://icr.io/oauth/token?service=registry&scope=repository:ibm-messaging/mq:pull" \
        | sed -E 's/.*"token":"([^"]+)".*/\1/')
curl -s -H "Authorization: Bearer $TOKEN" \
     -H "Accept: application/vnd.docker.distribution.manifest.list.v2+json" \
     https://icr.io/v2/ibm-messaging/mq/manifests/latest | grep -o '"architecture":"[^"]*"'
```

Résultat : **`amd64`, `s390x`, `ppc64le`**. Aucun tag `arm64` dans le dépôt, toutes versions
confondues.

Conséquence directe et non négociable : **la pile ne tourne pas sur une machine ARM**. Cela
exclut l'offre Always Free la plus généreuse d'Oracle Cloud (`VM.Standard.A1.Flex`, Ampere
Altra, 4 OCPU / 24 Go), les Raspberry Pi et les instances Graviton d'AWS.

Les échappatoires existent mais aucune n'est raisonnable en production : émulation `qemu`
(lente et non supportée par IBM), ou remplacement du broker — l'application est écrite contre
le client IBM MQ, ce n'est pas un changement de configuration.

### 2.2 L'image MQ du dépôt est une image « for Developers »

`icr.io/ibm-messaging/mq` avec `LICENSE: accept` est l'image **MQ Advanced for Developers** :
sa licence couvre le développement et les tests, pas l'exploitation. Pour une vraie mise en
production il faut une entitlement IBM MQ et l'image correspondante
(`cp.icr.io/cp/ibm-mqadvanced-server`, avec une clé d'accès au registre IBM Entitled).

Le reste de ce document reste valable : seuls le nom d'image et les identifiants changent.
Pour une démo, une recette ou un environnement interne, l'image développeur suffit.

---

## 3. Dimensionnement

| Poste | Empreinte observée |
|---|---|
| IBM MQ (image + QM) | ~1 Go d'image, 700 Mo – 1,5 Go de RAM |
| PostgreSQL | 200–400 Mo de RAM |
| Backend (JVM, `MaxRAMPercentage=75`) | 512 Mo – 1,5 Go selon la limite fixée |
| nginx + SPA | < 50 Mo |
| Build Angular (`npm run build`) | pics à ~2 Go de RAM |
| Build Maven | ~1 Go de RAM, dépendances téléchargées |

**Cible raisonnable : 4 vCPU / 8 Go de RAM / 50 Go de disque** si l'on construit les images sur
le serveur. **2 vCPU / 4 Go / 40 Go** suffisent si les images sont construites ailleurs et
tirées d'un registre (§ 4.9).

En dessous de 4 Go, le build Angular se fait tuer par l'OOM killer avant même que la pile
démarre.

---

## 4. Déploiement sur Oracle Cloud Infrastructure

### 4.1 Créer l'instance

Console OCI → *Compute* → *Instances* → *Create instance*.

- **Image** : Ubuntu 24.04 LTS (ou Oracle Linux 9 — adapter les commandes `apt` en `dnf`).
- **Shape** : famille **AMD/Intel**, pas Ampere (§ 2.1). Par exemple `VM.Standard.E5.Flex`,
  2 OCPU / 16 Go. L'Always Free AMD (`VM.Standard.E2.1.Micro`, 1 OCPU / 1 Go) est hors sujet :
  IBM MQ seul n'y tient pas.
- **Boot volume** : 50 Go minimum, 100 Go si la rétention des messages est longue.
- **SSH keys** : déposer sa clé publique. Aucun mot de passe.
- Noter l'**IP publique** attribuée ; la réserver (*Reserved public IP*) si un DNS doit pointer
  dessus.

### 4.2 Ouvrir le réseau — les deux pare-feux

C'est le piège classique d'OCI : il y a **deux** filtrages, et n'en ouvrir qu'un donne un port
qui « ne répond pas » sans message d'erreur.

**a) Security List / NSG du VCN** — console OCI → *Networking* → *Virtual Cloud Networks* → le
subnet → *Security Lists* → *Add Ingress Rules* :

| Source | Port | Usage |
|---|---|---|
| `0.0.0.0/0` | 80 | HTTP (redirection vers HTTPS + challenge ACME) |
| `0.0.0.0/0` | 443 | HTTPS |
| *votre IP* `/32` | 22 | SSH |

**Rien d'autre.** Ni 5432, ni 1414, ni 9443, ni 8080, ni 5050.

**b) Pare-feu de l'instance** — les images OCI arrivent avec des règles `iptables`
restrictives, indépendantes de la Security List :

```bash
# Ubuntu (netfilter-persistent)
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 80  -j ACCEPT
sudo iptables -I INPUT 6 -m state --state NEW -p tcp --dport 443 -j ACCEPT
sudo netfilter-persistent save

# Oracle Linux (firewalld)
sudo firewall-cmd --permanent --add-service=http --add-service=https
sudo firewall-cmd --reload
```

### 4.3 Préparer la machine

```bash
ssh ubuntu@<IP_PUBLIQUE>

sudo apt update && sudo apt upgrade -y

# Docker Engine + plugin compose (dépôt officiel)
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg \
  | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
echo "deb [arch=amd64 signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable" \
  | sudo tee /etc/apt/sources.list.d/docker.list
sudo apt update
sudo apt install -y docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin

sudo usermod -aG docker $USER && newgrp docker
docker compose version
```

Swap — utile si l'on construit sur place avec 4 Go :

```bash
sudo fallocate -l 4G /swapfile && sudo chmod 600 /swapfile
sudo mkswap /swapfile && sudo swapon /swapfile
echo '/swapfile none swap sw 0 0' | sudo tee -a /etc/fstab
```

### 4.4 Récupérer le code

```bash
sudo mkdir -p /opt/payment-messages && sudo chown $USER: /opt/payment-messages
git clone <URL_DU_DEPOT> /opt/payment-messages
cd /opt/payment-messages
```

`backend/src/main/resources/application-dev.yaml` **n'est pas nécessaire** : le profil `docker`
ne porte aucun fichier de propriétés, toute la configuration vient des variables d'environnement
lues par `application.yaml`.

### 4.5 Externaliser les secrets

Le `docker-compose.yaml` du dépôt contient des mots de passe en clair (`payment`, `passw0rd`,
`admin`) : c'est acceptable en local, pas sur une machine exposée. Créer un fichier `.env` à la
racine — **jamais versionné** :

```bash
cat > /opt/payment-messages/.env <<'EOF'
POSTGRES_DB=payment_messages
POSTGRES_USER=payment
POSTGRES_PASSWORD=<mot de passe fort>

MQ_ADMIN_PASSWORD=<mot de passe fort>
MQ_APP_PASSWORD=<mot de passe fort>

PUBLIC_ORIGIN=https://messages.example.com
EOF
chmod 600 /opt/payment-messages/.env
```

Vérifier que `.env` est bien ignoré par git (`git check-ignore -v .env`) ; l'ajouter au
`.gitignore` sinon.

### 4.6 Fichier d'override de production

Compose superpose les fichiers : `docker-compose.yaml` reste la base, l'override ci-dessous
retire ce qui n'a rien à faire en production et referme les ports. Créer
`docker-compose.prod.yaml` :

```yaml
services:

  # pgAdmin n'a pas à tourner en production (mot de passe par défaut, surface inutile).
  pgadmin:
    profiles: ["debug"]      # ne démarre plus, sauf `--profile debug`

  postgres:
    ports: !reset []         # plus de 5432 sur l'hôte : accès uniquement par le réseau Docker
    environment:
      POSTGRES_DB: ${POSTGRES_DB}
      POSTGRES_USER: ${POSTGRES_USER}
      POSTGRES_PASSWORD: ${POSTGRES_PASSWORD}
    logging: &logging
      driver: json-file
      options: { max-size: "10m", max-file: "5" }

  ibm-mq:
    ports: !reset []         # 1414 et 9443 restent internes
    environment:
      LICENSE: accept
      MQ_QMGR_NAME: QM1
      MQ_APP_PASSWORD: ${MQ_APP_PASSWORD}
      MQ_ADMIN_PASSWORD: ${MQ_ADMIN_PASSWORD}
    logging: *logging

  backend:
    ports: !reset []         # l'API n'est jointe que par nginx, jamais directement
    environment:
      DB_URL: jdbc:postgresql://postgres:5432/${POSTGRES_DB}?reWriteBatchedInserts=true
      DB_USER: ${POSTGRES_USER}
      DB_PASSWORD: ${POSTGRES_PASSWORD}
      MQ_PASSWORD: ${MQ_APP_PASSWORD}
      CORS_ALLOWED_ORIGINS: ${PUBLIC_ORIGIN}
      # Écran de simulation : à couper si la file d'entrée porte un vrai flux.
      SIMULATION_ENABLED: "false"
      # Purge planifiée des messages PROCESSED de plus de 90 jours.
      RETENTION_ENABLED: "true"
    deploy:
      resources:
        limits: { memory: 1500m }
    logging: *logging

  frontend:
    ports: !reset
      - "127.0.0.1:8081:80"   # exposé au seul terminateur TLS de l'hôte (§ 4.7)
    logging: *logging
```

> `!reset` demande Compose ≥ 2.24. Sur une version antérieure, dupliquer les services complets
> dans l'override plutôt que de réinitialiser les listes.

**Piège** : le healthcheck PostgreSQL du fichier de base code en dur le rôle et la base
(`pg_isready -U payment -d payment_messages`). Ne changer que le **mot de passe** dans `.env`,
ou redéfinir aussi le healthcheck dans l'override — sinon le conteneur reste `unhealthy` et
`up -d --wait` ne rend jamais la main :

```yaml
  postgres:
    healthcheck:
      test: ["CMD-SHELL", "pg_isready -U $${POSTGRES_USER} -d $${POSTGRES_DB}"]
```

Les variables non redéfinies (`MQ_QMGR`, `MQ_CHANNEL`, `MQ_CONN_NAME`, `MQ_QUEUE`,
`MQ_DLQ_QUEUE`, `MQ_MAX_RETRIES`, pool JDBC, concurrence JMS…) gardent les valeurs du fichier de
base — elles ne sont pas secrètes.

### 4.7 Terminaison TLS

Le conteneur `frontend` sert en clair sur le port 80 et ne pose **pas** d'en-tête HSTS : c'est
délibéré, HSTS n'a d'effet que sur une réponse HTTPS et doit venir du terminateur. Deux options.

**Option A — Caddy sur l'hôte (le plus court).** Certificat Let's Encrypt automatique, renouvelé
sans intervention :

```bash
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' \
  | sudo gpg --dearmor -o /usr/share/keyrings/caddy-stable-archive-keyring.gpg
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' \
  | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update && sudo apt install -y caddy
```

`/etc/caddy/Caddyfile` :

```caddyfile
messages.example.com {
    encode gzip
    header {
        # HSTS : posé ici, seul endroit où la réponse est réellement en HTTPS.
        Strict-Transport-Security "max-age=31536000; includeSubDomains"
    }
    reverse_proxy 127.0.0.1:8081
}
```

```bash
sudo systemctl reload caddy
```

**Option B — nginx + certbot sur l'hôte**, si nginx est déjà en place :

```bash
sudo apt install -y nginx certbot python3-certbot-nginx
sudo certbot --nginx -d messages.example.com
```

avec un `proxy_pass http://127.0.0.1:8081;` dans le `server` généré, et
`add_header Strict-Transport-Security "max-age=31536000; includeSubDomains" always;`.

Dans les deux cas, un enregistrement DNS `A` doit pointer sur l'IP publique **avant** la demande
de certificat.

### 4.8 Démarrer

```bash
cd /opt/payment-messages
docker compose -f docker-compose.yaml -f docker-compose.prod.yaml build
docker compose -f docker-compose.yaml -f docker-compose.prod.yaml up -d --wait
```

`--wait` bloque jusqu'à ce que les healthchecks passent : `pg_isready` pour PostgreSQL,
`chkmqhealthy` pour le queue manager, `/actuator/health/readiness` pour le backend (Flyway
appliqué, pool JDBC ouvert, conteneur JMS démarré), `wget /` pour nginx.

Le premier démarrage prend plusieurs minutes : construction des deux images, création du queue
manager (`start_period: 60s`), migrations Flyway.

Pour éviter de retaper les `-f`, exporter une fois pour toutes :

```bash
echo 'COMPOSE_FILE=docker-compose.yaml:docker-compose.prod.yaml' >> /opt/payment-messages/.env
```

### 4.9 Vérifications

```bash
docker compose ps                    # tous les services "healthy"
curl -f http://127.0.0.1:8081/       # la SPA répond localement
curl -fs https://messages.example.com/api/v1/config   # { queue, dlqQueue, queueManager, channel }
curl -fs https://messages.example.com/api/v1/messages/stats

# Les ports internes ne doivent PAS répondre depuis l'extérieur
nc -zv <IP_PUBLIQUE> 5432   # doit échouer
nc -zv <IP_PUBLIQUE> 8080   # doit échouer
nc -zv <IP_PUBLIQUE> 1414   # doit échouer
```

Vérifier aussi la chaîne complète : ouvrir l'interface, aller sur *Messages*, confirmer que le
tableau se remplit. Si la liste reste vide alors que la base contient des lignes, l'appel `/api/`
ne passe pas — regarder `docker compose logs frontend`.

### 4.10 Construire ailleurs plutôt que sur le serveur

Construire sur un VPS à 4 Go est fragile (le build Angular sature la RAM) et immobilise la
machine à chaque déploiement. Variante recommandée dès qu'il y a plus d'une mise à jour par
semaine : publier les images depuis la CI vers un registre (GHCR, Docker Hub, OCI Container
Registry), et ne faire que les tirer sur le serveur.

Côté CI (GitHub Actions), après le job de build existant :

```yaml
- uses: docker/login-action@v3
  with:
    registry: ghcr.io
    username: ${{ github.actor }}
    password: ${{ secrets.GITHUB_TOKEN }}
- uses: docker/build-push-action@v6
  with:
    context: ./backend
    platforms: linux/amd64          # cf. § 2.1
    push: true
    tags: ghcr.io/<org>/payment-messages-backend:${{ github.sha }}
```

Côté serveur, remplacer les blocs `build:` par `image: ghcr.io/<org>/…:<tag>` dans l'override,
puis :

```bash
docker compose pull && docker compose up -d --wait
```

### 4.11 Exploitation

**Journaux**

```bash
docker compose logs -f backend
docker compose logs -f --since 15m
```

Chaque ligne du backend porte `req=` (identifiant de corrélation HTTP, aussi renvoyé en
`X-Request-Id`) et `msg=` (identifiant du message MQ en cours d'ingestion) : c'est ce qui permet
de relier une erreur remontée par un utilisateur à sa trace serveur.

**Sauvegarde PostgreSQL** — c'est la seule donnée irremplaçable :

```bash
# Sauvegarde quotidienne, rétention 14 jours
cat > /opt/payment-messages/backup.sh <<'EOF'
#!/bin/bash
set -euo pipefail
cd /opt/payment-messages
# .env n'est pas chargé dans le shell par Compose : le sourcer pour disposer des identifiants.
set -a; . ./.env; set +a
mkdir -p backups
docker compose exec -T postgres pg_dump -U "$POSTGRES_USER" "$POSTGRES_DB" \
  | gzip > "backups/pg-$(date +%F).sql.gz"
find backups -name 'pg-*.sql.gz' -mtime +14 -delete
EOF
chmod +x /opt/payment-messages/backup.sh
( crontab -l 2>/dev/null; echo "30 2 * * * /opt/payment-messages/backup.sh" ) | crontab -
```

Un dump sur le même disque ne protège de rien : le copier ailleurs (OCI Object Storage,
`rclone`, un autre hôte).

**Mise à jour applicative**

```bash
cd /opt/payment-messages
git pull
docker compose build            # ou docker compose pull si registre
docker compose up -d --wait
docker image prune -f
```

Les migrations Flyway s'appliquent au démarrage du backend. Le schéma est validé
(`ddl-auto: validate`) : une entité sans migration correspondante empêche le démarrage — c'est
voulu, et le healthcheck le rend visible immédiatement.

**Changer la configuration des files MQ** — piège important : `infra/mq/payment-queues.mqsc`
n'est joué **qu'à la création du queue manager**. Modifier le fichier et redémarrer le conteneur
ne change rien. Il faut rejouer le script à la main :

```bash
docker compose exec ibm-mq bash -c "runmqsc QM1 < /etc/mqm/payment-queues.mqsc"
```

ou supprimer le volume `mq_data` (destruction des messages en file).

**Redémarrage machine** : `restart: unless-stopped` est déjà déclaré sur tous les services, la
pile remonte seule au boot dès lors que le service Docker est activé
(`sudo systemctl enable docker`).

### 4.12 Durcissement — à faire avant d'annoncer l'URL

- [ ] Mots de passe PostgreSQL et MQ changés (§ 4.5) ; `.env` en `chmod 600`, non versionné.
- [ ] `pgadmin` désactivé.
- [ ] Aucun port hors 22/80/443 dans la Security List, et rien publié sur `0.0.0.0` par Compose.
- [ ] SSH : authentification par clé uniquement (`PasswordAuthentication no`), root désactivé,
      source restreinte à une IP si possible.
- [ ] `unattended-upgrades` activé pour les correctifs de sécurité OS.
- [ ] `SIMULATION_ENABLED=false` si la file d'entrée porte un flux réel — sinon n'importe quel
      visiteur peut y déposer des messages.
- [ ] `MANAGEMENT_PORT` déplacé sur un port distinct si l'on veut réserver `/actuator/metrics`
      et `/actuator/prometheus` au réseau interne. **À savoir** : sans authentification (choix de
      périmètre du projet), tout ce qui est joignable est public — actuator et Swagger UI
      compris. Si l'application doit être exposée à Internet, filtrer `/swagger-ui`,
      `/v3/api-docs` et `/actuator` au niveau du terminateur TLS est le minimum.
- [ ] Sauvegardes testées : un dump qu'on n'a jamais restauré n'est pas une sauvegarde.
- [ ] Accès sortant vers `fonts.gstatic.com` disponible, sinon les polices IBM Plex retombent
      sur les polices système (l'application reste fonctionnelle).

---

## 5. Alternatives de déploiement hors cloud

Toutes supposent une machine **x86-64** (§ 2.1).

### 5.1 Serveur physique ou VM interne, même pile Compose

Le scénario le plus proche de ce qui précède, et le plus simple : un serveur en salle machine ou
une VM sur l'hyperviseur d'entreprise (**Proxmox VE**, **VMware vSphere**, **Hyper-V**,
**KVM/libvirt**, **XCP-ng**).

Rien ne change à partir du § 4.3 : Docker, `git clone`, `.env`, override de production,
`docker compose up -d --wait`. Seules diffèrent les couches réseau :

- pas de Security List : c'est le pare-feu de l'entreprise ou `ufw`/`firewalld` qui filtre ;
- le certificat TLS vient souvent d'une autorité interne plutôt que de Let's Encrypt (ACME ne
  peut pas valider un domaine non joignable depuis Internet) — poser le certificat manuellement
  dans Caddy (`tls /chemin/cert.pem /chemin/key.pem`) ou nginx ;
- prévoir des instantanés (snapshots) de la VM en plus des dumps PostgreSQL.

**Pour** : maîtrise complète, pas de coût récurrent, données qui ne quittent pas le réseau — ce
qui compte pour un flux de paiement. **Contre** : tout est à votre charge, matériel compris.

### 5.2 Installation native, sans conteneurs (systemd)

Utile quand la politique interne interdit Docker, ou quand IBM MQ est **déjà** installé et
exploité par ailleurs — cas très fréquent en banque : le queue manager est mutualisé, on ne
déploie que l'application.

Composants :

1. **PostgreSQL 18** installé par paquet ; créer base, rôle et mot de passe.
2. **IBM MQ** installé sur le serveur (ou distant) ; les files
   `PAYMENT.REQUEST.QUEUE` / `PAYMENT.BACKOUT.QUEUE` / `PAYMENT.DLQ.QUEUE` créées avec
   `infra/mq/payment-queues.mqsc` (`runmqsc QM1 < payment-queues.mqsc`), et un canal + un
   utilisateur applicatif autorisés.
3. **Backend** : construire le jar une fois (`./mvnw -B clean package -DskipTests`), le déposer
   dans `/opt/payment-messages/app.jar`, et l'exécuter en service systemd sous un compte dédié
   non privilégié.

```ini
# /etc/systemd/system/payment-messages.service
[Unit]
Description=Payment Messages API
After=network-online.target postgresql.service
Wants=network-online.target

[Service]
User=payment
Group=payment
WorkingDirectory=/opt/payment-messages
# Les secrets vivent dans un fichier lisible du seul compte de service (chmod 600).
EnvironmentFile=/etc/payment-messages/env
ExecStart=/usr/bin/java -XX:MaxRAMPercentage=75 -XX:+UseG1GC \
          -XX:+ExitOnOutOfMemoryError -jar /opt/payment-messages/app.jar
SuccessExitStatus=143
Restart=on-failure
RestartSec=10
NoNewPrivileges=true
PrivateTmp=true
ProtectSystem=strict
ReadWritePaths=/opt/payment-messages/logs

[Install]
WantedBy=multi-user.target
```

```bash
# /etc/payment-messages/env  (chmod 600)
SPRING_PROFILES_ACTIVE=prod
SERVER_PORT=8080
DB_URL=jdbc:postgresql://localhost:5432/payment_messages?reWriteBatchedInserts=true
DB_USER=payment
DB_PASSWORD=…
MQ_QMGR=QM1
MQ_CHANNEL=PAYMENT.SVRCONN
MQ_CONN_NAME=mq-host(1414)
MQ_USER=payment_app
MQ_PASSWORD=…
MQ_QUEUE=PAYMENT.REQUEST.QUEUE
MQ_DLQ_QUEUE=PAYMENT.DLQ.QUEUE
MQ_MAX_RETRIES=3
CORS_ALLOWED_ORIGINS=https://messages.interne.example
SIMULATION_ENABLED=false
```

> `SPRING_PROFILES_ACTIVE` doit désigner un profil **sans** fichier de propriétés (comme
> `docker`, ou n'importe quel nom inexistant tel que `prod`) : toute la configuration est alors
> lue dans les variables d'environnement via `application.yaml`. Le laisser sur `dev` ferait
> chercher `application-dev.yaml`, absent du jar.

4. **Frontend** : `npm ci && npm run build` sur une machine de build, puis copier
   `dist/frontend/browser/` dans `/var/www/payment-messages` et servir avec le nginx du système.
   Reprendre `frontend/nginx.conf` et `frontend/security-headers.conf` en deux modifications :
   remplacer `proxy_pass $backend_upstream$request_uri` par `proxy_pass http://127.0.0.1:8080`
   et **retirer la ligne `resolver 127.0.0.11`** — c'est le DNS interne de Docker, il n'existe
   pas hors conteneur.

**Pour** : pas de couche conteneur, intégration naturelle avec un MQ d'entreprise existant,
supervision par les outils système déjà en place. **Contre** : plus de pièces à installer et à
maintenir à la main ; la parité avec la CI (qui teste en conteneurs) n'est plus garantie.

### 5.3 Kubernetes auto-hébergé (k3s)

Si l'infrastructure interne est déjà sur Kubernetes. `k3s` sur un ou trois nœuds x86-64 suffit.

Ce qu'il faut écrire, que Compose fournissait gratuitement : `Deployment` + `Service` pour le
backend et le frontend, `StatefulSet` + `PersistentVolumeClaim` pour PostgreSQL et MQ, `Secret`
pour les mots de passe, `ConfigMap` pour le MQSC, `Ingress` (Traefik est fourni avec k3s) et un
`Certificate` cert-manager pour le TLS.

Deux points déjà préparés dans le dépôt : l'image backend tourne sous l'**uid numérique 1001**
(condition de `runAsNonRoot`), et l'endpoint `/actuator/health/readiness` est fait pour une
`readinessProbe` — il exclut volontairement MQ, une coupure du broker ne devant pas retirer
l'instance du service alors que l'API répond toujours.

**Pour** : redémarrages, montée en charge et déploiements progressifs gérés par la plateforme.
**Contre** : complexité sans commune mesure avec le besoin si l'application est seule sur sa
machine. IBM MQ en `StatefulSet` demande par ailleurs une vraie réflexion sur le stockage.

### 5.4 Mini-PC ou NAS (homelab)

Un mini-PC x86 (Intel N100 et assimilés, 16 Go de RAM) ou un NAS Synology/QNAP **x86** avec
Container Manager fait tourner la pile sans difficulté : Docker y est disponible, le
`docker-compose.yaml` du dépôt s'importe tel quel.

À prévoir : IP publique dynamique (DNS dynamique + redirection de ports sur la box, ou tunnel
type Cloudflare Tunnel / WireGuard pour éviter d'exposer la box), et sauvegardes hors du NAS.

**Attention** : les NAS et SBC en **ARM** (Synology DS220j, Raspberry Pi…) sont exclus par le
§ 2.1.

### 5.5 Poste de développement

Rappel pour mémoire, c'est le mode déjà documenté dans le README : `docker compose up -d` monte
la pile complète, ou `docker compose up -d postgres ibm-mq` pour ne démarrer que
l'infrastructure et lancer backend et frontend depuis les sources. Rien à déployer.

---

## 6. Récapitulatif

| Cible | Coût | Effort initial | Exploitation | Quand la choisir |
|---|---|---|---|---|
| VPS cloud x86 + Compose (§ 4) | ~15–40 €/mois | faible | faible | Démo, recette, petite production exposée |
| VM interne + Compose (§ 5.1) | matériel existant | faible | moyen | Données qui ne doivent pas sortir du réseau |
| systemd natif (§ 5.2) | matériel existant | moyen | moyen | Docker interdit, ou MQ d'entreprise déjà en place |
| k3s / Kubernetes (§ 5.3) | variable | élevé | faible une fois en place | Plateforme Kubernetes déjà exploitée |
| Mini-PC / NAS x86 (§ 5.4) | ~150–400 € une fois | faible | faible | Homelab, démonstration permanente |

Trois points valent pour toutes les cibles :

1. **x86-64 obligatoire** — l'image IBM MQ n'existe pas en ARM64 (§ 2.1).
2. **L'image MQ du dépôt est une image développeur** — vérifier la licence avant un usage
   réellement productif (§ 2.2).
3. **Il n'y a aucune authentification dans l'application** (choix de périmètre) : tout ce qui est
   joignable est public. Le filtrage réseau et le terminateur TLS sont, de fait, la seule
   protection.
