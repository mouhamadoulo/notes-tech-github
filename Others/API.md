# 🚀 API (Application Programming Interface)

## 🧩 Kesako ?

<div align="center">
	<img src="../images/api.png" height="350" width="350" />
</div>

> Une API (*Application Programming Interface*) est une interface qui permet à deux applications de communiquer entre elles.

Une API sert de **pont standardisé** entre systèmes :

* échange de données
* appel de fonctionnalités
* intégration de services externes

👉 Elle permet de **réutiliser des systèmes existants sans tout reconstruire**.

---

### 🔁 Schéma global d’une API

```mermaid
flowchart LR
A[Client / Frontend] -->|Requête HTTP| B[API]
B --> C[Application / Backend]
C --> B
B --> A
```

---

## 🧠 Modèles d’API

```mermaid
flowchart TD
API[API] --> SOAP
API --> REST
API --> GraphQL
API --> gRPC
API --> WebSocket
API --> Webhooks
```

---

## 🧼 SOAP (ancien mais robuste)

<div align="center">
	<img src="../images/soap.jpeg" height="400" width="400" />
</div>

* Protocole standardisé (W3C)
* Format XML strict
* Contrat via WSDL

### ✔️ Caractéristiques

* très structuré
* sécurisé (WS-Security)
* compatible multi-protocoles (HTTP, SMTP…)

### 📌 Usage

* banques
* systèmes critiques
* environnements legacy

---

## 🌐 REST (le standard moderne)

<div align="center">
	<img src="../images/rest.png" height="400" width="550" />
</div>

### 📡 Principe

REST repose sur des ressources accessibles via HTTP :

```mermaid
flowchart LR
Client -->|GET/POST/PUT| REST_API
REST_API --> Resource[(Ressources JSON)]
```

### 🔑 Concepts clés

* stateless
* ressources identifiées par URL
* verbes HTTP (GET, POST, PUT, DELETE)
* format JSON majoritaire

### ⚙️ HATEOAS

Le client découvre les actions possibles via les réponses API.

---

## 📊 GraphQL (flexibilité côté client)

<div align="center">
	<img src="../images/graphql.jpg" height="400" width="550" />
</div>

### 💡 Principe

Le client demande **exactement ce dont il a besoin** :

```mermaid
flowchart LR
Client --> GraphQL_Server
GraphQL_Server --> DB[(Multiple Sources)]
DB --> GraphQL_Server --> Client
```

### ✔️ Avantages

* moins de sur-fetching
* une seule endpoint
* flexible pour frontend complexe

### ⚠️ Limites

* complexité serveur
* caching plus difficile

---

## 🔀 API vs WebService vs Microservice

| Concept      | Définition                 | Rôle         |
| ------------ | -------------------------- | ------------ |
| API          | Interface de communication | Contrat      |
| WebService   | API via réseau (HTTP)      | Transport    |
| Microservice | Architecture applicative   | Organisation |

---

## 🧭 API Management

```mermaid
flowchart LR
A[Design] --> B[Build]
B --> C[Test]
C --> D[Deploy]
D --> E[Monitor]
E --> A
```

### 🎯 Objectifs

* gérer le cycle de vie des API
* sécuriser les accès
* analyser les usages

### 🧰 Fonctions clés

* design & documentation
* sécurité (OAuth, API keys)
* monitoring
* throttling / rate limiting

---

## 📊 KPIs API

* disponibilité (uptime)
* consommation (trafic)
* scalabilité (charge supportée)

---

## 🧪 Postman

### Cas d’usage principaux

* tests API
* automatisation
* environnements (dev/staging/prod)
* documentation
* mocks
* CI/CD

---

# 📡 Codes de retour HTTP

## 🧭 Vue globale

```mermaid
flowchart TD
HTTP --> 1xx
HTTP --> 2xx
HTTP --> 3xx
HTTP --> 4xx
HTTP --> 5xx
```

---

## 📌 Résumé

| Code | Signification  |
| ---- | -------------- |
| 2xx  | Succès         |
| 3xx  | Redirection    |
| 4xx  | Erreur client  |
| 5xx  | Erreur serveur |

---

# 🌐 Verbes HTTP

```mermaid
flowchart LR
GET --> READ
POST --> CREATE
PUT --> REPLACE
PATCH --> UPDATE
DELETE --> REMOVE
```

---

# 🔐 Quelques astuces pour sécuriser ses API

## 🔒 Astuce 1 — Utiliser HTTPS

Toutes les communications doivent être chiffrées via TLS.

👉 Empêche :

* interception de données
* attaques MITM

---

## 🔑 Astuce 2 — OAuth2

Protocole d’autorisation moderne.

✔ Permet :

* accès limité
* tokens temporaires
* délégation (Google, GitHub login)

---

## 🔐 Astuce 3 — WebAuthn

Authentification forte sans mot de passe.

✔ Basé sur :

* biométrie
* clés matérielles

---

## 🔑 Astuce 4 — API Keys avec niveaux

Ne jamais utiliser une clé unique globale.

✔ Bonnes pratiques :

* clé par utilisateur
* clé par environnement
* permissions limitées

---

## 🛂 Astuce 5 — Authorization stricte

Différencier :

* authentification (qui es-tu ?)
* autorisation (as-tu le droit ?)

---

## 🚦 Astuce 6 — Rate Limiting

Limiter le nombre de requêtes :

```mermaid
flowchart LR
User --> API
API -->|OK si < limite| Response
API -->|bloqué si abus| Error429
```

✔ Protège contre :

* brute force
* DDoS
* abus API

---

## 🔁 Astuce 7 — Versioning API

Exemple :

* `/api/v1/users`
* `/api/v2/users`

✔ Permet :

* évolution sans casser les clients

---

## 🧾 Astuce 8 — Allowlisting

Ne pas bloquer ce qui est dangereux → mais autoriser uniquement ce qui est connu.

✔ Très efficace pour :

* endpoints internes
* microservices

---

## 🛡️ Astuce 9 — OWASP API Security

### ⚠️ Principaux risques

* BOLA (Broken Object Level Authorization)
* Broken Authentication
* Excessive Data Exposure
* Rate Limit insuffisant
* SSRF

---

### 🔥 Vue structurée

```mermaid
mindmap
  root((OWASP API Risks))
    Auth
      Broken Authentication
    Authorization
      BOLA
      BFLA
    Data
      Excessive Exposure
    Infrastructure
      SSRF
      Misconfiguration
    Abuse
      Rate Limit
      Business Logic Abuse
```

---

## 🛡️ Astuce 10 — API Gateway

Point d’entrée unique :

```mermaid
flowchart LR
Client --> Gateway --> Microservices
Gateway --> Auth
Gateway --> RateLimit
Gateway --> Logs
```

✔ Centralise :

* sécurité
* routing
* monitoring

---

## ⚠️ Astuce 11 — Error Handling

Bonnes pratiques :

* ne pas exposer les stack traces
* messages génériques côté client
* logs détaillés côté serveur

---

## 🧪 Astuce 12 — Input Validation

Toujours valider :

* types
* formats
* tailles
* schémas JSON

✔ Empêche :

* injections
* corruption de données

---

# How to Stop Designing REST APIs the Wrong Way

Designing a REST API is not just about exposing data. A well-designed API should be intuitive, scalable, secure, and consistent. Here are 10 common mistakes developers make and how to avoid them.

---

# 1. Stop Using Verbs in URLs

REST APIs should focus on **resources (nouns)** rather than actions (verbs).

❌ Bad

```http
GET /getUsers
POST /createUser
DELETE /deleteUser/123
```

✅ Good

```http
GET /users
POST /users
DELETE /users/123
```

The HTTP method already describes the action:

| Method | Action   |
| ------ | -------- |
| GET    | Retrieve |
| POST   | Create   |
| PUT    | Replace  |
| PATCH  | Update   |
| DELETE | Remove   |

```mermaid
graph LR
A[GET /users] --> B[Read Users]
C[POST /users] --> D[Create User]
E[PUT /users/1] --> F[Update User]
G[DELETE /users/1] --> H[Delete User]
```

---

# 2. Stop Ignoring HTTP Status Codes

Many APIs return `200 OK` for everything, including errors.

Use appropriate status codes to help clients understand what happened.

| Code | Meaning               |
| ---- | --------------------- |
| 200  | Success               |
| 201  | Resource Created      |
| 204  | No Content            |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 409  | Conflict              |
| 422  | Validation Error      |
| 500  | Internal Server Error |

❌ Bad

```json
{
  "success": false,
  "message": "User not found"
}
```

Response:

```http
200 OK
```

✅ Good

```http
404 Not Found
```

```json
{
  "error": "USER_NOT_FOUND",
  "message": "User not found"
}
```

---

# 3. Stop Returning Inconsistent JSON

Consistency improves developer experience.

❌ Bad

Endpoint A

```json
{
  "userName": "John"
}
```

Endpoint B

```json
{
  "name": "John"
}
```

Endpoint C

```json
{
  "username": "John"
}
```

✅ Good

Use a standard response format.

```json
{
  "data": {
    "id": 1,
    "username": "John"
  }
}
```

Error responses should also follow a common structure.

```json
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "Email is required"
  }
}
```

---

# 4. Stop Ignoring API Versioning

APIs evolve over time. Without versioning, breaking changes can impact existing clients.

Common approaches:

### URI Versioning

```http
/api/v1/users
/api/v2/users
```

### Header Versioning

```http
Accept: application/vnd.company.v2+json
```

```mermaid
graph TD
A["Client v1"] --> B["/api/v1/users"]
C["Client v2"] --> D["/api/v2/users"]
```

Benefits:

* Backward compatibility
* Easier migrations
* Controlled evolution

---

# 5. Stop Returning Everything (Stop Ignoring Pagination)

Returning thousands of records in a single request hurts performance.

❌ Bad

```http
GET /users
```

Response:

```json
[
  ...100000 users...
]
```

✅ Good

```http
GET /users?page=1&limit=20
```

Response:

```json
{
  "data": [],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 5000,
    "pages": 250
  }
}
```

```mermaid
graph LR
A[Client] --> B[GET users?page=1]
B --> C[20 Users]
A --> D[GET users?page=2]
D --> E[20 Users]
```

---

# 6. Stop Mixing Authentication & Authorization

These concepts are different.

### Authentication

Who are you?

Example:

```http
Authorization: Bearer eyJhb...
```

### Authorization

What are you allowed to do?

Example:

* Admin can delete users
* Customer cannot delete users

```mermaid
flowchart TD
A[User Request] --> B[Authentication]
B -->|Valid Token| C[Authorization]
B -->|Invalid Token| D[401 Unauthorized]
C -->|Allowed| E[Process Request]
C -->|Denied| F[403 Forbidden]
```

---

# 7. Stop Ignoring Error Structure

Error responses should be predictable.

❌ Bad

```json
{
  "msg": "something wrong"
}
```

✅ Good

```json
{
  "error": {
    "code": "EMAIL_ALREADY_EXISTS",
    "message": "The email is already registered",
    "details": {
      "field": "email"
    }
  }
}
```

Benefits:

* Easier debugging
* Better frontend integration
* Better monitoring

---

# 8. Stop Ignoring Filtering & Sorting Standards

Users often need subsets of data.

❌ Bad

```http
GET /activeUsers
GET /sortedUsers
```

✅ Good

Filtering:

```http
GET /users?status=active
```

Sorting:

```http
GET /users?sort=name
GET /users?sort=-createdAt
```

Multiple filters:

```http
GET /users?status=active&country=FR
```

Advanced example:

```http
GET /products?category=laptop&price_lt=1500
```

This keeps APIs flexible and reduces endpoint proliferation.

---

# 9. Stop Ignoring Security

Security must be considered from day one.

Essential practices:

### Use HTTPS

❌

```http
http://api.company.com
```

✅

```http
https://api.company.com
```

### Validate Inputs

Never trust user input.

### Rate Limiting

Prevent abuse:

```http
100 requests/minute
```

### Use Secure Authentication

* OAuth2
* OpenID Connect
* JWT (when appropriate)

### Protect Sensitive Data

Never expose:

* Passwords
* Secret keys
* Internal IDs when avoidable

```mermaid
flowchart LR
A[Client] --> B[HTTPS]
B --> C[API Gateway]
C --> D[Authentication]
D --> E[Rate Limiting]
E --> F[Application]
```

---

# 10. Stop Designing APIs Around Your Database

A REST API is a product for consumers, not a mirror of your database schema.

❌ Database-driven design

```http
/users
/orders
/order_items
/user_addresses
```

Clients may need multiple requests to build a single screen.

❌

```text
GET /users/1
GET /orders?userId=1
GET /addresses?userId=1
```

✅ Consumer-driven design

```http
GET /users/1/profile
```

Response:

```json
{
  "id": 1,
  "name": "John",
  "address": {},
  "recentOrders": []
}
```

```mermaid
graph TD
A[Frontend Screen]
A --> B[Single API Endpoint]
B --> C[Aggregated Data]
```

Design APIs around business use cases, not database tables.

---

# Final Thoughts

A good REST API should be:

* Resource-oriented
* Consistent
* Versioned
* Secure
* Easy to consume
* Performance-conscious
* Backward compatible

Before publishing an endpoint, ask yourself:

> "Does this API make sense for the consumer, or only for the backend implementation?"

If the answer is "only for the backend", redesign it.


# 🎯 Conclusion

Une API moderne repose sur 3 piliers :

```mermaid
flowchart TD
A[Sécurité] --> API
B[Scalabilité] --> API
C[Observabilité] --> API
```


