# Vue d’ensemble : où se place chaque technologie ?

Imagine que tu veux déployer une application web composée :

* d'un frontend React
* d'une API Spring Boot
* d'une base PostgreSQL

Voici comment les technologies s'enchaînent :

```mermaid
flowchart LR
    A[Code source] --> B[Dockerfile]
    B --> C[Image Docker]

    C --> D[Docker Compose]
    C --> E[Docker Swarm]
    C --> F[Kubernetes]

    G[Terraform] --> H[Infrastructure Cloud]
    H --> F

    I[Ansible] --> J[Configuration des serveurs]
    J --> E
    J --> F
```

En résumé :

| Technologie    | Rôle                                                    |
| -------------- | ------------------------------------------------------- |
| Docker         | Exécuter une application dans un conteneur              |
| Dockerfile     | Construire une image Docker                             |
| Docker Compose | Lancer plusieurs conteneurs ensemble                    |
| Docker Swarm   | Orchestrer des conteneurs Docker sur plusieurs machines |
| Kubernetes     | Orchestration avancée de conteneurs                     |
| Ansible        | Configurer des serveurs                                 |
| Terraform      | Créer l'infrastructure                                  |

---

# 1. Docker

## Le problème

Avant Docker :

```text
Dev :
→ Ça marche sur ma machine

Prod :
→ Ça ne marche pas
```

Car :

* version Java différente
* dépendances différentes
* système différent

Docker résout cela.

## Concept

Docker encapsule :

```text
Application
+ librairies
+ dépendances
+ configuration
```

dans un conteneur.

```mermaid
flowchart TD
    A[Serveur] --> B[Docker Engine]

    B --> C[Conteneur Frontend]
    B --> D[Conteneur API]
    B --> E[Conteneur PostgreSQL]
```

---

## Exemple

Application Java :

```java
public class Main {
    public static void main(String[] args){
        System.out.println("Hello Docker");
    }
}
```

Docker l'exécute dans un environnement isolé.

---

## Commandes principales

Télécharger une image :

```bash
docker pull nginx
```

Lancer un conteneur :

```bash
docker run nginx
```

Voir les conteneurs :

```bash
docker ps
```

Arrêter :

```bash
docker stop container_id
```

---

# 2. Dockerfile

## Le problème

Comment créer sa propre image Docker ?

Réponse : Dockerfile.

C'est la recette de fabrication.

---

## Exemple complet

Application Spring Boot :

```dockerfile
FROM eclipse-temurin:21

WORKDIR /app

COPY target/app.jar app.jar

EXPOSE 8080

CMD ["java","-jar","app.jar"]
```

---

## Explication

### FROM

Image de base.

```dockerfile
FROM eclipse-temurin:21
```

équivaut à :

> "Je pars d'un Linux contenant Java 21"

---

### WORKDIR

```dockerfile
WORKDIR /app
```

équivaut à :

```bash
cd /app
```

---

### COPY

```dockerfile
COPY target/app.jar app.jar
```

copie le fichier dans l'image.

---

### EXPOSE

```dockerfile
EXPOSE 8080
```

Documentation du port utilisé.

---

### CMD

Commande de démarrage :

```dockerfile
CMD ["java","-jar","app.jar"]
```

---

## Construction

Créer l'image :

```bash
docker build -t mon-api .
```

Lancer :

```bash
docker run mon-api
```

---

## Schéma

```mermaid
flowchart LR
    A[Code source]
    B[Dockerfile]

    A --> C[Build Docker]
    B --> C

    C --> D[Image Docker]
    D --> E[Conteneur]
```

---

# 3. Docker Compose

## Le problème

Une application réelle contient souvent plusieurs services.

Exemple :

```text
Frontend
API
PostgreSQL
Redis
```

Lancer chaque conteneur à la main devient pénible.

---

## Solution

Docker Compose.

Un seul fichier :

```yaml
services:
  frontend:
  api:
  postgres:
  redis:
```

---

## Exemple réel

```yaml
version: "3"

services:

  db:
    image: postgres:16
    environment:
      POSTGRES_DB: app

  api:
    build: .
    ports:
      - "8080:8080"
    depends_on:
      - db

  frontend:
    image: nginx
    ports:
      - "80:80"
```

---

## Démarrage

```bash
docker compose up
```

Arrêt :

```bash
docker compose down
```

---

## Architecture

```mermaid
flowchart LR
    U[Utilisateur]

    U --> F[Frontend]
    F --> A[API]
    A --> D[PostgreSQL]
```

Tous les services sont démarrés ensemble.

---

# 4. Docker Swarm

## Le problème

Docker Compose fonctionne bien sur UNE machine.

Mais si :

* 10 serveurs
* haute disponibilité
* plusieurs réplicas

alors il faut un orchestrateur.

---

## Solution

Docker Swarm.

Cluster de machines Docker.

---

## Architecture

```mermaid
flowchart TD

    M[Manager]

    M --> W1[Worker 1]
    M --> W2[Worker 2]
    M --> W3[Worker 3]

    W1 --> C1[Container]
    W2 --> C2[Container]
    W3 --> C3[Container]
```

---

## Exemple

Créer le cluster :

```bash
docker swarm init
```

Ajouter un worker :

```bash
docker swarm join ...
```

---

## Déployer

```bash
docker service create \
--replicas 3 \
nginx
```

Résultat :

```mermaid
flowchart LR
    LB[Load Balancer]

    LB --> N1[Nginx]
    LB --> N2[Nginx]
    LB --> N3[Nginx]
```

---

## Avantages

* réplication
* failover
* load balancing

---

## Limites

Aujourd'hui Kubernetes domine largement.

Swarm est devenu marginal.

---

# 5. Kubernetes (K8s)

## Le problème

Quand tu gères :

* 50 applications
* 200 conteneurs
* 20 serveurs

Docker seul ne suffit plus.

---

## Kubernetes = orchestrateur de conteneurs

Il décide :

* où lancer les conteneurs
* combien lancer
* comment les remplacer
* comment les mettre à jour

---

## Architecture simplifiée

```mermaid
flowchart TD

    M[Control Plane]

    M --> N1[Node 1]
    M --> N2[Node 2]
    M --> N3[Node 3]

    N1 --> P1[Pod]
    N2 --> P2[Pod]
    N3 --> P3[Pod]
```

---

## Pod

Un Pod est la plus petite unité Kubernetes.

```mermaid
flowchart TD
    P[Pod]

    P --> C1[Container API]
```

Souvent :

```text
1 Pod = 1 conteneur
```

---

## Deployment

Déclare :

```text
Je veux 3 copies de mon API
```

Exemple :

```yaml
apiVersion: apps/v1
kind: Deployment

spec:
  replicas: 3
```

---

## Service

Expose les Pods.

```mermaid
flowchart LR

    Client --> Service

    Service --> Pod1
    Service --> Pod2
    Service --> Pod3
```

---

## Auto-healing

Pod supprimé ?

```mermaid
flowchart LR

    Pod1[Pod mort]
    K8S[Kubernetes]

    Pod1 --> K8S
    K8S --> NewPod[Nouveau Pod]
```

Kubernetes recrée automatiquement le Pod.

---

## Rolling Update

Déploiement sans interruption.

```mermaid
flowchart LR

    V1[API v1]
    V2[API v2]

    V1 --> V2
```

Kubernetes remplace progressivement les instances.

---

## Exemple complet

```mermaid
flowchart TD

    User

    User --> Ingress

    Ingress --> Service

    Service --> Pod1
    Service --> Pod2
    Service --> Pod3

    Pod1 --> DB[(PostgreSQL)]
    Pod2 --> DB
    Pod3 --> DB
```

---

# 6. Ansible

## Le problème

Tu as 100 serveurs Linux.

Tu dois installer :

* Docker
* Java
* Nginx

sur chacun.

À la main ?

Impossible.

---

## Solution

Ansible.

Ansible configure les serveurs automatiquement.

---

## Fonctionnement

```mermaid
flowchart LR

    A[Machine Ansible]

    A --> S1[Serveur 1]
    A --> S2[Serveur 2]
    A --> S3[Serveur 3]
```

Communication via SSH.

---

## Exemple

Installer Docker :

```yaml
- hosts: servers

  tasks:

  - name: Install Docker
    apt:
      name: docker.io
      state: present
```

---

## Exécution

```bash
ansible-playbook docker.yml
```

---

## Cas réel

```mermaid
flowchart TD

    A[Ansible]

    A --> B[Installer Docker]
    A --> C[Créer utilisateurs]
    A --> D[Configurer Firewall]
    A --> E[Déployer application]
```

---

# 7. Terraform

## Le problème

Créer à la main :

* VM
* Réseaux
* Bases de données
* Clusters Kubernetes

sur AWS, Azure ou GCP est long et source d'erreurs.

---

## Solution

Terraform

Infrastructure as Code (IaC).

Tu décris l'infrastructure dans un fichier.

Terraform la crée.

---

## Exemple AWS

```hcl
resource "aws_instance" "web" {
  ami           = "ami-123456"
  instance_type = "t3.micro"
}
```

---

## Workflow

```mermaid
flowchart LR

    A[terraform code]

    A --> B[terraform plan]

    B --> C[terraform apply]

    C --> D[AWS Azure GCP]
```

---

## Exemple réel

Terraform crée :

```text
3 VM
1 Load Balancer
1 Base PostgreSQL
1 Cluster Kubernetes
```

---

## Architecture

```mermaid
flowchart TD

    TF[Terraform]

    TF --> VPC[VPC]
    TF --> VM1[VM 1]
    TF --> VM2[VM 2]
    TF --> VM3[VM 3]
    TF --> DB[Database]
```

---

# Comment tout cela fonctionne ensemble ?

Exemple DevOps moderne :

```mermaid
flowchart TD

    DEV[Développeur]

    DEV --> GIT[Git]

    GIT --> CI[GitLab CI / GitHub Actions]

    CI --> DF[Dockerfile]

    DF --> IMG[Image Docker]

    IMG --> REG[Registry]

    TF[Terraform] --> K8S[Kubernetes Cluster]

    REG --> K8S

    ANS[Ansible] --> K8S

    K8S --> PROD[Production]
```

## Étape par étape

### 1

Le développeur écrit le code.

### 2

Le Dockerfile construit l'image.

```text
Code → Image Docker
```

### 3

L'image est stockée dans un registre.

Exemples :

* Docker Hub
* GitHub Container Registry
* Amazon Elastic Container Registry

### 4

Terraform crée :

* VM
* Réseau
* Cluster Kubernetes

### 5

Ansible configure les machines.

### 6

Kubernetes déploie les conteneurs.

### 7

Les utilisateurs accèdent à l'application.

---

# Résumé ultra-court

```mermaid
flowchart LR

    A[Dockerfile]
    B[Docker Image]
    C[Docker Compose]
    D[Docker Swarm]
    E[Kubernetes]
    F[Ansible]
    G[Terraform]

    A --> B
    B --> C
    B --> D
    B --> E

    G --> E
    F --> E
```

* **Dockerfile** → recette de construction
* **Docker** → exécute les conteneurs
* **Docker Compose** → plusieurs conteneurs sur une machine
* **Docker Swarm** → plusieurs machines Docker
* **Kubernetes** → orchestration industrielle de conteneurs
* **Ansible** → configuration des serveurs
* **Terraform** → création de l'infrastructure

Une manière simple de retenir :

> **Terraform construit les serveurs → Ansible les configure → Docker exécute les applications → Kubernetes les orchestre à grande échelle.**
