# Gestion des Secrets dans une Architecture Spring Boot + Angular

## Introduction

La gestion des secrets est un sujet critique dans le développement moderne.

Un **secret** est toute information sensible permettant d'accéder à une ressource :

- Mots de passe de base de données
- Clés API
- Jetons OAuth
- Secrets JWT
- Certificats
- Clés de chiffrement
- Credentials cloud (AWS, Azure, GCP)

Une mauvaise gestion des secrets est l'une des causes les plus fréquentes de compromission d'applications.

---

# 1. Les erreurs à ne jamais commettre

## ❌ Hardcoder les secrets

Mauvais exemple :

```java
public class DatabaseConfig {

    private static final String PASSWORD = "MySuperPassword123";

}
```

---

## ❌ Commiter des secrets dans Git

Mauvais :

```yaml
spring:
  datasource:
    username: admin
    password: MySuperPassword123
```

---

## ❌ Stocker les secrets dans Angular

Mauvais :

```typescript
export const environment = {
  production: true,
  stripeSecretKey: "sk_live_xxxxx"
};
```

Tout ce qui est livré au navigateur est public.

Même après minification.

Même après obfuscation.

Même dans une variable d'environnement Angular.

---

## ❌ Réutiliser les mêmes secrets partout

Mauvais :

```text
JWT_SECRET=super-secret
DB_PASSWORD=super-secret
API_KEY=super-secret
```

Chaque secret doit être unique.

---

# 2. Principe fondamental : Frontend vs Backend

## Angular

Le frontend est considéré comme :

```text
100% public
```

Ne jamais stocker :

- mot de passe DB
- secret JWT
- secret OAuth
- clé Stripe privée
- clé AWS

---

## Ce qui peut être exposé côté Angular

Exemples :

```typescript
export const environment = {
  production: true,
  apiUrl: "https://api.monapp.com"
};
```

ou

```typescript
export const environment = {
  sentryDsn: "https://xxxx.ingest.sentry.io/123"
};
```

Ces informations sont publiques.

---

# 3. Les différents niveaux de configuration Spring Boot

Spring Boot permet plusieurs sources de configuration.

Ordre simplifié :

1. Variables d'environnement
2. Paramètres JVM
3. application.yml
4. application.properties

---

## Exemple classique

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/app
    username: ${DB_USER}
    password: ${DB_PASSWORD}
```

Puis :

Linux :

```bash
export DB_USER=admin
export DB_PASSWORD=super-secret
```

Windows :

```powershell
$env:DB_USER="admin"
$env:DB_PASSWORD="super-secret"
```

Spring injectera automatiquement les valeurs.

---

# 4. Bonne pratique minimale

Ne jamais stocker de secret dans le repository.

Repository :

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/app
    username: ${DB_USER}
    password: ${DB_PASSWORD}
```

Machine locale :

```bash
DB_USER=admin
DB_PASSWORD=xxxx
```

---

# 5. Gestion locale avec fichier .env

Très pratique pour les développeurs.

## Exemple

`.env`

```env
DB_USER=admin
DB_PASSWORD=super-secret
JWT_SECRET=xxxxxxxxxxxx
```

---

## Important

Ajouter dans :

`.gitignore`

```gitignore
.env
.env.*
```

---

# 6. Profils Spring

## application.yml

```yaml
spring:
  profiles:
    active: dev
```

---

## application-dev.yml

```yaml
app:
  feature-x: true
```

---

## application-prod.yml

```yaml
app:
  feature-x: false
```

---

L'objectif :

- configuration versionnée
- secrets externalisés

---

# 7. Docker

## Mauvaise pratique

```dockerfile
ENV DB_PASSWORD=super-secret
```

Le secret finit dans l'image.

---

## Bonne pratique

```bash
docker run \
-e DB_USER=admin \
-e DB_PASSWORD=xxxx \
mon-app
```

---

## Docker Compose

```yaml
services:
  backend:
    image: mon-app
    environment:
      DB_USER: ${DB_USER}
      DB_PASSWORD: ${DB_PASSWORD}
```

---

Puis :

```env
DB_USER=admin
DB_PASSWORD=xxxx
```

---

# 8. Docker Secrets

Pour la production.

Création :

```bash
echo "super-secret" > db_password.txt
```

---

Déclaration :

```yaml
services:
  backend:
    secrets:
      - db_password

secrets:
  db_password:
    file: ./db_password.txt
```

---

Lecture :

```java
String password =
    Files.readString(
        Path.of("/run/secrets/db_password")
    );
```

---

# 9. Kubernetes Secrets

Très utilisé en entreprise.

## Création

```bash
kubectl create secret generic db-secret \
--from-literal=username=admin \
--from-literal=password=super-secret
```

---

## Injection

```yaml
env:
  - name: DB_USER
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: username

  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: db-secret
        key: password
```

---

Spring :

```yaml
spring:
  datasource:
    username: ${DB_USER}
    password: ${DB_PASSWORD}
```

---

## Attention

Les Kubernetes Secrets ne sont PAS chiffrés par défaut.

Ils sont seulement encodés en Base64.

Toujours activer :

- etcd encryption
- RBAC strict

---

# 10. Vault : la référence

Pour les environnements professionnels.

## Pourquoi Vault ?

Il permet :

- stockage sécurisé
- rotation automatique
- audit
- contrôle d'accès
- secrets dynamiques

---

Outil :

:contentReference[oaicite:0]{index=0}

---

# 11. Spring Boot + Vault

Dépendance :

```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-vault-config</artifactId>
</dependency>
```

---

Configuration :

```yaml
spring:
  cloud:
    vault:
      uri: http://localhost:8200
      token: my-token
```

---

Lecture :

```java
@Value("${database.password}")
private String password;
```

Le mot de passe est récupéré depuis Vault.

---

# 12. AWS Secrets Manager

Très utilisé sur AWS.

Service :

:contentReference[oaicite:1]{index=1}

---

Avantages :

- rotation automatique
- IAM
- audit CloudTrail
- haute disponibilité

---

Exemple Java

```java
SecretsManagerClient client =
        SecretsManagerClient.create();

GetSecretValueResponse response =
        client.getSecretValue(
            GetSecretValueRequest.builder()
                .secretId("prod/db")
                .build()
        );

String secret = response.secretString();
```

---

# 13. Azure Key Vault

Service :

:contentReference[oaicite:2]{index=2}

Fonctionnement similaire à Vault ou AWS Secrets Manager.

---

# 14. Google Secret Manager

Service :

:contentReference[oaicite:3]{index=3}

Très intégré à GKE.

---

# 15. Rotation des secrets

Un secret ne doit jamais être permanent.

Bonnes pratiques :

| Secret | Rotation |
|----------|----------|
| API Key | 30 à 90 jours |
| JWT signing key | 3 à 12 mois |
| DB password | 30 à 90 jours |
| Cloud credentials | automatique |

---

# 16. Gestion du JWT

Ne jamais faire :

```yaml
jwt:
  secret: my-secret
```

---

Préférer :

```yaml
jwt:
  secret: ${JWT_SECRET}
```

---

Puis :

```bash
JWT_SECRET=xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
```

---

## Générer un secret robuste

Linux :

```bash
openssl rand -base64 64
```

---

Java :

```java
SecureRandom random = new SecureRandom();

byte[] key = new byte[64];

random.nextBytes(key);
```

---

# 17. Détection de secrets dans Git

Très fortement recommandé.

---

## Gitleaks

Outil :

:contentReference[oaicite:4]{index=4}

Scan :

```bash
gitleaks detect
```

---

## TruffleHog

Outil :

:contentReference[oaicite:5]{index=5}

Scan :

```bash
trufflehog git .
```

---

# 18. CI/CD

Les secrets doivent être stockés dans le système CI.

Exemples :

- GitHub Actions Secrets
- GitLab CI Variables
- Jenkins Credentials
- Azure DevOps Library

---

Jamais :

```yaml
env:
  DB_PASSWORD: super-secret
```

---

Toujours :

```yaml
env:
  DB_PASSWORD: ${{ secrets.DB_PASSWORD }}
```

---

# 19. Angular et les secrets

## Règle absolue

Si Angular connaît un secret :

```text
L'utilisateur connaît le secret.
```

---

## Architecture correcte

```text
Angular
   |
   v
Spring Boot
   |
   v
Stripe / AWS / OpenAI / DB
```

---

## Mauvaise architecture

```text
Angular
   |
   +--> AWS
   |
   +--> Stripe Secret
```

---

## Bonne architecture

```text
Angular
   |
   v
Spring Boot
   |
   v
Services externes
```

---

# 20. Exemple complet

## Angular

```typescript
this.http.post(
  "/api/payment",
  {
    amount: 100
  }
);
```

---

## Spring

```java
@PostMapping("/payment")
public void payment(
        @RequestBody PaymentRequest request) {

    stripeService.process(request.amount());
}
```

---

## Service

```java
@Service
public class StripeService {

    @Value("${STRIPE_SECRET}")
    private String secret;

}
```

---

## Variable d'environnement

```bash
STRIPE_SECRET=sk_live_xxxxx
```

Le secret n'est jamais exposé au navigateur.

---

# 21. Architecture recommandée en 2026

## Petit projet

```text
Angular
    |
Spring Boot
    |
Variables d'environnement
```

---

## Projet moyen

```text
Angular
    |
Spring Boot
    |
Docker Secrets
```

---

## Projet entreprise

```text
Angular
    |
Spring Boot
    |
Vault / AWS Secrets Manager
    |
Kubernetes
```

---

# 22. Checklist finale

## Développement

- [ ] Aucun secret dans Git
- [ ] Fichiers .env ignorés
- [ ] Variables d'environnement utilisées
- [ ] Gitleaks activé

---

## Spring Boot

- [ ] Secrets via `${VARIABLE}`
- [ ] Profils Spring séparés
- [ ] JWT robuste
- [ ] Rotation régulière

---

## Angular

- [ ] Aucun secret côté frontend
- [ ] Appels vers backend uniquement
- [ ] Pas de clé privée dans environment.ts

---

## Production

- [ ] Vault ou Secret Manager
- [ ] Rotation automatique
- [ ] Audit activé
- [ ] Permissions minimales
- [ ] Chiffrement des secrets au repos
- [ ] Journalisation des accès

---

# Recommandation de Stack Moderne

Pour une application Spring Boot + Angular déployée sur Kubernetes :

```text
Frontend:
- Angular

Backend:
- Spring Boot

Secrets:
- HashiCorp Vault
  ou AWS Secrets Manager

Déploiement:
- Kubernetes

CI/CD:
- GitHub Actions
- GitLab CI

Contrôle:
- Gitleaks
```

Cette combinaison couvre aujourd'hui la majorité des besoins en matière de sécurité, d'auditabilité, de rotation des secrets et de conformité dans les architectures cloud natives.
