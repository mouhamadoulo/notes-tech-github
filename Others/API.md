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

# 🎯 Conclusion

Une API moderne repose sur 3 piliers :

```mermaid
flowchart TD
A[Sécurité] --> API
B[Scalabilité] --> API
C[Observabilité] --> API
```

---

Si tu veux, je peux aussi :

* transformer ça en **cours PDF prêt à imprimer**
* ajouter une section **JWT / OAuth2 en profondeur**
* ou faire une version **ultra simplifiée pour révision rapide**
