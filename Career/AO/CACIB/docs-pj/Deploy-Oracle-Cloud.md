Si ton objectif est de **déployer ton projet comme en production**, je te conseille de ne **pas copier les fichiers à la main**. Le plus propre est de passer par GitHub (même en privé) puis de faire un `git clone` sur ton VPS Oracle.

Voici la procédure complète.

---

# Architecture finale

```
PC Windows
│
├── payment-messages/
│      ├── backend/
│      ├── frontend/
│      ├── docker-compose.yml
│      └── ...
│
│ git push
▼
GitHub (repo privé)
│
│ git clone
▼
Oracle Cloud VPS
│
├── payment-messages/
│
└── docker compose up -d
```

C'est exactement ce que ferait une entreprise.

---

# Etape 1 - Vérifier que ton projet fonctionne en local

Depuis la racine :

```bash
docker compose up --build
```

Tout doit fonctionner.

Par exemple :

```
Frontend
Backend
Postgres
IBM MQ
PgAdmin
```

---

# Etape 2 - Pousser ton projet sur GitHub

Si ce n'est pas déjà fait :

```bash
git init

git add .

git commit -m "Initial version"

git branch -M main

git remote add origin https://github.com/toncompte/payment-messages.git

git push -u origin main
```

Le dépôt peut être privé.

---

# Etape 3 - Se connecter sur Oracle

Depuis ton PC

```bash
ssh opc@IP_DE_TON_SERVEUR
```

Exemple

```bash
ssh opc@152.xxx.xxx.xxx
```

---

# Etape 4 - Vérifier Docker

```bash
docker --version
```

Puis

```bash
docker compose version
```

Si tu obtiens

```
Docker version ...
Docker Compose version ...
```

tout est bon.

---

# Etape 5 - Installer Git

Souvent Git est déjà installé.

Sinon

Oracle Linux

```bash
sudo dnf install git -y
```

Ubuntu

```bash
sudo apt update
sudo apt install git -y
```

Vérifier

```bash
git --version
```

---

# Etape 6 - Cloner ton projet

Par exemple

```bash
cd ~

git clone https://github.com/toncompte/payment-messages.git
```

Tu obtiens

```
payment-messages
```

Puis

```bash
cd payment-messages
```

---

# Etape 7 - Vérifier les fichiers

```
payment-messages

backend/

frontend/

docker-compose.yml
```

---

# Etape 8 - Construire les images

Si ton compose contient

```yaml
build:
  context: ./backend
```

et

```yaml
build:
  context: ./frontend
```

il suffit de faire

```bash
docker compose build
```

ou directement

```bash
docker compose up -d --build
```

---

# Etape 9 - Vérifier les conteneurs

```bash
docker ps
```

Tu dois voir

```
backend

frontend

postgres

mq

pgadmin
```

---

# Etape 10 - Vérifier les logs

Backend

```bash
docker logs payment-backend
```

ou

```bash
docker compose logs backend
```

Frontend

```bash
docker compose logs frontend
```

MQ

```bash
docker compose logs ibm-mq
```

---

# Etape 11 - Ouvrir les ports Oracle Cloud

C'est une étape que beaucoup oublient.

Dans Oracle Cloud

```
Networking

↓

Virtual Cloud Network

↓

Subnet

↓

Security Lists
```

Autoriser par exemple :

```
80
443
4200
8080
9443
1414
```

selon les ports exposés par ton `docker-compose.yml`.

---

# Etape 12 - Vérifier le firewall Linux

Si le firewall est actif :

```bash
sudo firewall-cmd --list-ports
```

Ajouter un port :

```bash
sudo firewall-cmd --permanent --add-port=8080/tcp

sudo firewall-cmd --reload
```

---

# Etape 13 - Tester depuis ton navigateur

Si ton frontend écoute sur le port 4200

```
http://IP_DU_SERVEUR:4200
```

Backend

```
http://IP_DU_SERVEUR:8080/api/messages
```

IBM MQ Console

```
https://IP:9443
```

PgAdmin

```
http://IP:5050
```

---

# Etape 14 - Mettre à jour l'application

Une fois connecté :

```bash
cd payment-messages

git pull

docker compose down

docker compose up -d --build
```

La nouvelle version est en ligne.

---

# Bonus : éviter de reconstruire sur le serveur (plus professionnel)

Pour un vrai pipeline CI/CD, tu peux :

1. Construire les images Docker avec GitHub Actions.
2. Les publier sur Docker Hub ou GitHub Container Registry (GHCR).
3. Sur le VPS, ne faire qu'un `docker compose pull` puis `docker compose up -d`.

Les avantages sont nombreux :

* déploiements plus rapides ;
* serveur plus léger (pas de compilation Java/Angular) ;
* images versionnées et reproductibles ;
* approche proche des pratiques DevOps en entreprise.

## Déploiement recommandé

```
PC
    │
git push
    │
    ▼
GitHub
    │
GitHub Actions
    │
    ├── Build Backend
    ├── Build Frontend
    ├── Push vers GHCR
    ▼
Oracle Cloud VPS
    │
docker compose pull
docker compose up -d
```

Pour ton projet **Payment Messages**, cette seconde approche est celle que je recommanderais si tu souhaites t'entraîner à un déploiement de niveau professionnel et enrichir ton portfolio.
