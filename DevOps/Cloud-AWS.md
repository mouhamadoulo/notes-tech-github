Voici un **tutoriel complet et structuré sur AWS (Amazon Web Services)** avec des **exemples concrets** et des **diagrammes Mermaid** pour bien visualiser les concepts.

---

# ☁️ 1. C’est quoi AWS ?

Amazon Web Services est la plateforme cloud d’Amazon. Elle permet de louer des ressources informatiques à la demande :

* serveurs (machines virtuelles)
* bases de données
* stockage
* IA / machine learning
* réseaux
* déploiement d’applications

👉 Idée clé : tu ne possèdes plus de serveurs → tu les **utilises à la demande** et tu payes uniquement ce que tu consommes.

<div align="center">
	<img src="../images/aws.png" height="350" width="350" />
</div>

```
Amazon Web Services (AWS) est la plateforme cloud la plus complète et la plus largement adoptée au monde. 
Elle propose plus de 200 services complets issus de centres de données du monde entier. 
Des millions de clients (dont certaines des startups les plus dynamiques au monde, de très grandes entreprises 
et des agences fédérales de premier plan) utilisent AWS pour réduire leurs coûts, gagner en agilité et innover plus rapidement.
```

---

# 🧱 2. Les grands concepts du cloud AWS

## 2.1 Infrastructure globale

AWS est organisé en :

* **Regions** (ex: Paris, Irlande)
* **Availability Zones (AZ)** : centres de données dans une région
* **Edge locations** : cache (CDN)

```mermaid
flowchart TD
A[Utilisateur] --> B[Region AWS - Paris]
B --> C[AZ1 - Data Center]
B --> D[AZ2 - Data Center]
B --> E[AZ3 - Data Center]
```

👉 Objectif : haute disponibilité et tolérance aux pannes.

---

# 💻 3. Les services AWS essentiels

## 3.1 EC2 (serveurs virtuels)

Amazon EC2 = machine virtuelle dans le cloud.

### Exemple :

Tu veux héberger un site web.

👉 Tu lances une machine Linux sur AWS.

```mermaid
flowchart LR
User[Utilisateur] --> Internet
Internet --> EC2[Serveur EC2]
EC2 --> App[Site Web]
```

### Cas concret :

* Ubuntu server
* Apache/Nginx
* Node.js / Python backend

💡 Tu peux :

* augmenter la puissance (scale vertical)
* ajouter plusieurs serveurs (scale horizontal)

<div align="center">
	<img src="../images/ec2.jpeg" height="350" width="350" />
</div>

```
Capacité de calcul sécurisée et redimensionnable pour pratiquement toutes les charges de travail
EC2 offre la plateforme de calcul la plus variée et la plus complète, avec plus de 750 instances. 
Vous y avez accès au processeur, à la solution de stockage, aux réseaux, au système d’exploitation 
et au modèle d’achat les plus récents pour vous aider à répondre au mieux aux besoins de votre charge de travail
```
```
Avantages : L'engagement SLA (Service Level Agreement), Optimiser les performances et les coûts, Outils de migration AWS
Use cases : Exécuter des applications métier et natives cloud
```
---

## 3.2 S3 (stockage)

Amazon S3 = stockage de fichiers.

### Exemple :

Tu stockes :

* images
* vidéos
* backups
* fichiers utilisateurs

```mermaid
flowchart TD
User --> App
App --> S3[(Amazon S3 Bucket)]
S3 --> Files[Images / Videos / Docs]
```

💡 Avantages :

* quasi illimité
* très durable (11 9's)
* accessible via URL

---

## 3.3 RDS (bases de données)

Amazon RDS = base de données managée.

Supporte :

* MySQL
* PostgreSQL
* MariaDB
* SQL Server

### Exemple :

```mermaid
flowchart LR
App --> RDS[(Database)]
RDS --> Data[Tables SQL]
```

💡 AWS gère :

* backups
* mises à jour
* réplication

---

## 3.4 Lambda (serverless)

AWS Lambda = exécuter du code sans serveur.

### Exemple :

Upload d’une image → redimension automatique

```mermaid
flowchart LR
Upload --> S3
S3 --> Lambda
Lambda --> Resize[Image optimisée]
Resize --> S3
```

💡 Tu ne gères aucun serveur.

---

# 🌐 4. Architecture complète (exemple réel)

## Exemple : site e-commerce

* Frontend web
* Backend API
* base de données
* stockage images

```mermaid
flowchart TD
User[Client] --> CloudFront[CDN]
CloudFront --> S3[Frontend React]

CloudFront --> ALB[Load Balancer]
ALB --> EC2_1[Backend EC2]
ALB --> EC2_2[Backend EC2]

EC2_1 --> RDS[(Database)]
EC2_2 --> RDS

EC2_1 --> S3Images[(Images produits)]
```

---

# ⚖️ 5. Load Balancer (répartition de charge)

Elastic Load Balancing

👉 répartit le trafic entre plusieurs serveurs

### Exemple :

```mermaid
flowchart LR
User --> LB[Load Balancer]
LB --> Server1[EC2 #1]
LB --> Server2[EC2 #2]
LB --> Server3[EC2 #3]
```

💡 Avantages :

* pas de surcharge
* haute disponibilité
* tolérance aux pannes

---

# 📦 6. Stockage avancé : S3 détaillé

## Structure :

* Bucket = dossier principal
* Objects = fichiers
* Keys = chemins

```mermaid
flowchart TD
S3Bucket[Bucket S3]
S3Bucket --> Img1[image1.png]
S3Bucket --> Img2[image2.jpg]
S3Bucket --> Video[video.mp4]
```

## Exemple URL :

```
https://bucket-name.s3.amazonaws.com/image1.png
```

---

# 🔐 7. Sécurité AWS (très important)

AWS Identity and Access Management (IAM)

## IAM = gestion des accès

### Exemple :

* Développeur → accès EC2
* Marketing → accès S3 uniquement

```mermaid
flowchart TD
User --> IAM
IAM --> Policy1[Accès EC2]
IAM --> Policy2[Accès S3]
IAM --> Policy3[Accès RDS]
```

💡 principe clé :
👉 **Least Privilege (moindre privilège)**

---

# 🚀 8. Déploiement simple d’une application AWS

## Étapes typiques :

1. créer un serveur EC2
2. installer Node.js / Python
3. déployer le code
4. connecter base RDS
5. stocker fichiers dans S3

```mermaid
flowchart TD
Dev[Code local] --> Git
Git --> EC2
EC2 --> App[Application]
App --> RDS
App --> S3
```

---

# 📊 9. Scalabilité AWS

## Auto Scaling

AWS Auto Scaling

```mermaid
flowchart TD
TrafficHigh --> AddServer[+ EC2]
TrafficLow --> RemoveServer[- EC2]
```

💡 Le système ajuste automatiquement les serveurs.

---

# 🌍 10. Cas réel complet (startup)

### Exemple : Instagram-like

* S3 → images
* EC2 → API
* RDS → utilisateurs
* Lambda → compression images
* CloudFront → accélération

```mermaid
flowchart TD
User --> CloudFront
CloudFront --> API[EC2 API]
API --> DB[(RDS)]
API --> S3[(Images)]
S3 --> Lambda[Resize]
```

---

# 🧠 11. Résumé simple

| Service      | Rôle               |
| ------------ | ------------------ |
| EC2          | Serveurs           |
| S3           | Stockage           |
| RDS          | Base de données    |
| Lambda       | Code sans serveur  |
| IAM          | Sécurité           |
| ELB          | Répartition trafic |
| Auto Scaling | Ajuste serveurs    |


