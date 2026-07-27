Voici la procédure **officielle et propre pour installer Docker sur Ubuntu** (Ubuntu 22.04 / 24.04 recommandé). Elle installe Docker Engine + Docker Compose v2.

## 1. Mettre à jour Ubuntu

```bash
sudo apt update
sudo apt upgrade -y
```

Installer les dépendances :

```bash
sudo apt install -y ca-certificates curl gnupg lsb-release
```

---

## 2. Ajouter la clé GPG officielle Docker

Créer le dossier des clés :

```bash
sudo install -m 0755 -d /etc/apt/keyrings
```

Télécharger la clé Docker :

```bash
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | \
sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
```

Donner les permissions :

```bash
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

---

## 3. Ajouter le dépôt Docker officiel

Détecter la version Ubuntu :

```bash
source /etc/os-release
```

Ajouter le dépôt :

```bash
echo \
"deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] \
https://download.docker.com/linux/ubuntu \
$VERSION_CODENAME stable" | \
sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
```

Exemple :

* Ubuntu 22.04 → `jammy`
* Ubuntu 24.04 → `noble`

---

## 4. Installer Docker Engine

Mettre à jour les dépôts :

```bash
sudo apt update
```

Installer Docker :

```bash
sudo apt install -y \
docker-ce \
docker-ce-cli \
containerd.io \
docker-buildx-plugin \
docker-compose-plugin
```

---

## 5. Vérifier l'installation

Voir la version :

```bash
docker --version
```

Exemple :

```
Docker version 28.x.x
```

Tester Docker :

```bash
sudo docker run hello-world
```

Résultat attendu :

```
Hello from Docker!
This message shows that your installation appears to be working correctly.
```

---

# 6. Autoriser Docker sans sudo (recommandé)

Par défaut :

```bash
sudo docker ps
```

fonctionne, mais pas :

```bash
docker ps
```

Ajouter votre utilisateur au groupe docker :

```bash
sudo usermod -aG docker $USER
```

Appliquer :

```bash
newgrp docker
```

Tester :

```bash
docker ps
```

---

# 7. Activer Docker au démarrage

Vérifier le service :

```bash
sudo systemctl status docker
```

Activer au boot :

```bash
sudo systemctl enable docker
```

Démarrer si nécessaire :

```bash
sudo systemctl start docker
```

---

# 8. Vérifier Docker Compose

Avec Docker récent, Compose est intégré :

```bash
docker compose version
```

Exemple :

```
Docker Compose version v2.27.0
```

Tester :

```bash
docker compose ps
```

---

# 9. Commandes utiles après installation

### Voir les conteneurs actifs

```bash
docker ps
```

Tous les conteneurs :

```bash
docker ps -a
```

---

### Voir les images

```bash
docker images
```

---

### Nettoyer Docker

Supprimer les conteneurs arrêtés :

```bash
docker container prune
```

Supprimer les images inutilisées :

```bash
docker image prune
```

Nettoyage complet :

```bash
docker system prune -a
```

---

# 10. Installation rapide (alternative)

Sur une machine de test uniquement :

```bash
curl -fsSL https://get.docker.com | sudo sh
```

Puis :

```bash
sudo usermod -aG docker $USER
newgrp docker
```

---

## Pour ton cas (Oracle Cloud Free Tier + ton projet `payment-messages`)

Après installation, je te conseille cette séquence :

```bash
git clone https://github.com/ton-user/payment-messages.git

cd payment-messages

docker compose up -d --build
```

Puis vérifier :

```bash
docker ps
```

et les logs :

```bash
docker compose logs -f
```

⚠️ Comme tu utilises **Oracle Cloud Free Tier ARM64**, il faudra vérifier la compatibilité des images Docker (tu avais déjà eu des erreurs `no matching manifest for linux/arm64/v8` avec IBM MQ et PostgreSQL). Pour ce VPS précis, il faudra probablement adapter certains services avec des images multi-architecture.
