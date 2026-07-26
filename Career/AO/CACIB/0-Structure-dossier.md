Pour ce type de projet, je partirais sur une architecture **professionnelle**, proche de ce qu'on retrouve dans les banques (BNP, Société Générale, BRED, CACIB, etc.), en privilégiant une architecture hexagonale (Ports & Adapters). Elle est plus facile à tester, évolutive et adaptée aux applications qui dialoguent avec des systèmes externes comme IBM MQ.

## Structure globale du repository

```text
payment-messages/
│
├── backend/                 # Spring Boot
├── frontend/                # Angular
├── docker/
│   ├── postgres/
│   ├── ibmmq/
│   └── monitoring/
│
├── docs/
│   ├── architecture/
│   ├── api/
│   ├── mq/
│   ├── screenshots/
│   └── sequence-diagrams/
│
├── scripts/
│
├── .github/
│   └── workflows/
│       ├── backend.yml
│       ├── frontend.yml
│       └── docker.yml
│
├── docker-compose.yml
├── README.md
├── .gitignore
└── LICENSE
```

---

# Backend

```text
backend/
│
├── src/
│   ├── main/
│   │
│   ├── java/
│   │   └── com/
│   │       └── bank/
│   │           └── paymentmessages/
│   │
│   │               ├── config/
│   │               │
│   │               ├── domain/
│   │               │   ├── model/
│   │               │   ├── repository/
│   │               │   ├── service/
│   │               │   └── exception/
│   │               │
│   │               ├── application/
│   │               │   ├── usecase/
│   │               │   ├── dto/
│   │               │   ├── mapper/
│   │               │   └── port/
│   │               │       ├── in/
│   │               │       └── out/
│   │               │
│   │               ├── infrastructure/
│   │               │
│   │               │   ├── persistence/
│   │               │   │   ├── entity/
│   │               │   │   ├── repository/
│   │               │   │   └── mapper/
│   │               │   │
│   │               │   ├── mq/
│   │               │   │   ├── listener/
│   │               │   │   ├── config/
│   │               │   │   └── mapper/
│   │               │   │
│   │               │   └── rest/
│   │               │       ├── controller/
│   │               │       ├── advice/
│   │               │       └── mapper/
│   │               │
│   │               └── PaymentMessagesApplication.java
│   │
│   └── resources/
│       ├── application.yml
│       ├── application-dev.yml
│       ├── application-docker.yml
│       └── db/
│           └── migration/
│
├── src/test/
│
├── pom.xml
└── Dockerfile
```

---

# Frontend Angular

```text
frontend/
│
├── src/
│
│   ├── app/
│   │
│   │   ├── core/
│   │   │   ├── services/
│   │   │   ├── interceptors/
│   │   │   ├── guards/
│   │   │   └── models/
│   │   │
│   │   ├── shared/
│   │   │   ├── components/
│   │   │   ├── pipes/
│   │   │   ├── directives/
│   │   │   └── utils/
│   │   │
│   │   ├── features/
│   │   │
│   │   │   └── messages/
│   │   │       ├── pages/
│   │   │       ├── components/
│   │   │       ├── services/
│   │   │       ├── models/
│   │   │       └── store/
│   │   │
│   │   ├── layout/
│   │   │
│   │   ├── app.routes.ts
│   │   └── app.config.ts
│   │
│   ├── assets/
│   └── environments/
│
├── angular.json
├── package.json
└── Dockerfile
```

---

# Docker

```text
docker/

├── postgres/
│   └── init.sql
│
├── ibmmq/
│   └── mq-config.mqsc
│
└── monitoring/
    ├── prometheus.yml
    └── grafana/
```

---

# Documentation

```text
docs/

architecture/

    architecture.md
    hexagonal.md

api/

    openapi.yaml
    postman_collection.json

mq/

    queues.md
    ibm-mq.md

sequence-diagrams/

    receive-message.drawio
    search-message.drawio

screenshots/
```

---

# Scripts

```text
scripts/

start.sh

stop.sh

clean.sh

populate-db.sh
```

---

# Github

```text
.github/

workflows/

backend.yml

frontend.yml

docker.yml
```

---

# Ce que verra l'utilisateur

L'application Angular peut être simple mais professionnelle :

```text
Dashboard
│
├── Liste des messages
│
├── Recherche multicritères
│      ├── Message Id
│      ├── Correlation Id
│      ├── Queue
│      ├── Date
│      ├── Statut
│
├── Détail d'un message
│
├── Statistiques
│      ├── Nb messages reçus
│      ├── Nb erreurs
│      ├── Temps moyen
│      ├── Débit
│
└── Santé de l'application
```

---

# Base de données

Une table principale suffit pour commencer.

```text
MESSAGE

id
message_id
correlation_id
queue_name
payload
status
received_at
processed_at
routing_destination
retry_count
error_message
created_at
updated_at
```

---

# Architecture finale

```text
Angular
    │
REST API
    │
Controllers
    │
Use Cases
    │
Domain
    │
Repositories (Ports)
    │
JPA Adapter
    │
PostgreSQL

IBM MQ
    │
MQ Listener
    │
Use Cases
    │
Domain
```

## Pourquoi cette structure ?

Elle sépare clairement les responsabilités :

* **Domain** : les règles métier (indépendantes de Spring et de la base de données).
* **Application** : les cas d'utilisation (consulter un message, enregistrer un message, rechercher des messages).
* **Infrastructure** : les détails techniques (IBM MQ, JPA, REST).
* **Frontend** : organisé par fonctionnalités (`features`) pour rester maintenable.
* **Docker** : environnement de développement reproductible avec PostgreSQL et IBM MQ.
* **Docs** : architecture, API OpenAPI, diagrammes et documentation de test.
* **CI/CD GitHub Actions** : compilation, tests et construction des images Docker.

Cette organisation est suffisamment robuste pour un projet de démonstration tout en restant proche des pratiques utilisées sur des projets bancaires traitant de gros volumes de messages.
