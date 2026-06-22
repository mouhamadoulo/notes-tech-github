Voici des **schémas d’architecture simplifiés** des applications les plus utilisées au monde. Ce sont des **architectures typiques (pas les schémas internes exacts propriétaires)**, mais elles reflètent très bien comment ces systèmes sont généralement conçus à grande échelle.

---

# 1) Google Search (moteur de recherche)

```mermaid
flowchart LR
U[Utilisateur] --> LB[Load Balancer]
LB --> FE[Front-end Search API]

FE --> QP[Query Processor]
QP --> IR[Index Shards]

IR --> RS[Ranking Service]
RS --> CA[Cache]

CA --> FE
FE --> U

subgraph Data Layer
IR
CA
end
```

### Technologies typiques

* **C++ / Java** : backend haute performance
* **Bigtable** : stockage distribué des index
* **Colossus (successeur de GFS)** : stockage distribué
* **TensorFlow / ML models** : ranking des résultats
* **Redis-like cache** : accélération requêtes

### Utilité

* Indexation massive du web
* Recherche ultra-rapide
* Classement intelligent via IA

---

# 2) YouTube (streaming vidéo)

```mermaid
flowchart LR
U[Utilisateur] --> CDN[CDN Edge Server]

CDN --> API[YouTube API]
API --> MS[Metadata Service]

MS --> DB[(Metadata DB)]
API --> VS[Video Service]

VS --> ST[(Google Cloud Storage)]
VS --> EN[Transcoding Pipeline]

EN --> CDN
```

### Technologies typiques

* **Google Cloud Storage** : stockage vidéos
* **CDN global (YouTube CDN)** : diffusion rapide
* **FFmpeg / encoders internes** : transcodage
* **Kubernetes** : orchestration services
* **Bigtable / Spanner** : métadonnées

### Utilité

* Streaming mondial sans latence
* Adaptation qualité vidéo (360p → 4K)
* Recommandation de contenu

---

# 3) Netflix (streaming vidéo + recommandation)

```mermaid
flowchart LR
U[Utilisateur] --> APP[Netflix App]
APP --> EDGE[Netflix Edge CDN]

EDGE --> API[API Gateway]

API --> AUTH[Auth Service]
API --> REC[Recommendation Engine]
API --> STREAM[Streaming Service]

STREAM --> S3[(Storage - AWS S3)]
REC --> DATA[(User Behavior Data)]

DATA --> ML[ML Models]
ML --> REC
```

### Technologies typiques

* **AWS (S3, EC2, CloudFront)** : infrastructure principale
* **Apache Cassandra** : base distribuée
* **Kafka** : événements utilisateurs
* **Spark / Machine Learning** : recommandations
* **Node.js / Java** : backend API

### Utilité

* Streaming haute disponibilité
* Recommandations personnalisées
* Scalabilité mondiale

---

# 4) WhatsApp (messagerie instantanée)

```mermaid
flowchart LR
U1[Utilisateur A] --> S[WhatsApp Servers]
U2[Utilisateur B] --> S

S --> MQ[Message Queue]
MQ --> RT[Real-time Delivery Service]

RT --> U1
RT --> U2

S --> DB[(Cassandra DB)]
```

### Technologies typiques

* **Erlang** : gestion millions de connexions simultanées
* **Cassandra** : stockage messages
* **MQ (queue distribuée)** : fiabilité livraison
* **WebSockets / TCP custom protocol** : temps réel

### Utilité

* Messagerie instantanée mondiale
* Faible latence
* Tolérance aux pannes

---

# 5) Amazon (e-commerce)

```mermaid
flowchart LR
U[Client] --> FE[Frontend]
FE --> API[API Gateway]

API --> CAT[Catalog Service]
API --> CART[Cart Service]
API --> ORD[Order Service]

ORD --> PAY[Payment Service]
ORD --> FUL[Fulfillment Service]

FUL --> WARE[Warehouse System]
CAT --> DB[(Product DB)]
```

### Technologies typiques

* **Java / microservices**
* **DynamoDB** : stockage produit
* **SQS / Kafka** : communication entre services
* **AWS Lambda** : fonctions serverless
* **Elasticsearch** : recherche produits

### Utilité

* Gestion catalogue géant
* Commandes distribuées
* Logistique automatisée

---

Les architectures exactes d'Airbnb et d'Uber ne sont pas publiques dans leur intégralité. En revanche, les deux entreprises publient régulièrement leurs choix techniques via leurs blogs d'ingénierie. On peut donc reconstituer une architecture de référence très proche de la réalité, en distinguant :

* Frontend
* API Gateway
* Microservices
* Messaging/Event Streaming
* Bases de données
* Cache
* Search
* Data Platform
* ML/IA
* Observabilité
* Infrastructure Cloud

---

# 6) Architecture complète d'Airbnb

## Vue globale

```mermaid
flowchart TB

    U[Web React / Mobile iOS Android]

    U --> CDN[CDN Cloudflare]
    CDN --> GW[API Gateway]

    GW --> USER[User Service]
    GW --> LISTING[Listing Service]
    GW --> SEARCH[Search Service]
    GW --> BOOKING[Booking Service]
    GW --> PAYMENT[Payment Service]
    GW --> REVIEW[Review Service]
    GW --> MSG[Messaging Service]

    USER --> MYSQL1[(MySQL)]
    LISTING --> MYSQL2[(MySQL)]
    BOOKING --> MYSQL3[(MySQL)]
    REVIEW --> MYSQL4[(MySQL)]

    SEARCH --> ES[(Elasticsearch)]

    USER --> REDIS[(Redis)]
    LISTING --> REDIS
    SEARCH --> REDIS

    BOOKING --> KAFKA[Kafka]
    PAYMENT --> KAFKA
    REVIEW --> KAFKA

    KAFKA --> DATALAKE[Data Lake]
    KAFKA --> SPARK[Apache Spark]

    SPARK --> ML[ML Platform]

    USER --> OBS[Datadog]
    BOOKING --> OBS
    PAYMENT --> OBS
```

---

# Technologies Airbnb

| Brique              | Technologie      |
| ------------------- | ---------------- |
| Front Web           | React            |
| Mobile iOS          | Swift            |
| Mobile Android      | Kotlin           |
| Backend             | Java, Kotlin     |
| API Gateway         | Envoy            |
| Service Discovery   | Kubernetes       |
| Orchestration       | Kubernetes       |
| Database principale | MySQL            |
| Cache               | Redis            |
| Search              | Elasticsearch    |
| Messaging           | Kafka            |
| Batch Processing    | Apache Spark     |
| Data Warehouse      | Hive, Presto     |
| ML Platform         | Bighead, Airflow |
| Monitoring          | Datadog          |
| Tracing             | Zipkin           |
| CDN                 | Cloudflare       |
| Cloud               | AWS              |

---

# Airbnb - Recherche de logements

```mermaid
sequenceDiagram

    User->>Search API: Search Paris
    Search API->>Redis: Cache Query
    Redis-->>Search API: Miss

    Search API->>Elasticsearch: Full Text Search
    Elasticsearch-->>Search API: Listings

    Search API->>Ranking Engine: Ranking
    Ranking Engine-->>Search API: Ranked Results

    Search API-->>User: Search Results
```

---

# Airbnb - Réservation

```mermaid
sequenceDiagram

    Guest->>Booking Service: Book Listing

    Booking Service->>Availability Service: Check Availability

    Availability Service-->>Booking Service: Available

    Booking Service->>Payment Service: Charge Card

    Payment Service-->>Booking Service: Success

    Booking Service->>Kafka: Booking Event

    Booking Service-->>Guest: Confirmation
```

---

# Architecture interne Airbnb par domaines

```mermaid
flowchart LR

    UserDomain[User Domain]
    HostDomain[Host Domain]
    ListingDomain[Listing Domain]
    BookingDomain[Booking Domain]
    PaymentDomain[Payment Domain]
    MessagingDomain[Messaging Domain]
    ReviewDomain[Review Domain]

    UserDomain --> BookingDomain
    ListingDomain --> BookingDomain
    BookingDomain --> PaymentDomain
    BookingDomain --> MessagingDomain
    BookingDomain --> ReviewDomain
```

---

# 7) Architecture complète d'Uber

Uber est beaucoup plus complexe car il doit gérer :

* géolocalisation temps réel
* dispatch de chauffeurs
* pricing dynamique
* tracking GPS
* ETA prediction
* machine learning en temps réel

---

## Architecture globale Uber

```mermaid
flowchart TB

    USER[Passenger App]
    DRIVER[Driver App]

    USER --> GATEWAY[API Gateway]
    DRIVER --> GATEWAY

    GATEWAY --> USER_SVC[User Service]
    GATEWAY --> TRIP_SVC[Trip Service]
    GATEWAY --> MATCH_SVC[Matching Service]
    GATEWAY --> ETA_SVC[ETA Service]
    GATEWAY --> PRICING_SVC[Pricing Service]
    GATEWAY --> PAYMENT_SVC[Payment Service]

    MATCH_SVC --> GEO[Geospatial Service]

    GEO --> H3[H3 Index]

    USER_SVC --> MYSQL[(MySQL)]

    TRIP_SVC --> CASS[(Cassandra)]

    MATCH_SVC --> REDIS[(Redis)]

    ETA_SVC --> ML[ML Models]

    PRICING_SVC --> ML

    PAYMENT_SVC --> MYSQL2[(MySQL)]

    TRIP_SVC --> KAFKA[Kafka]

    MATCH_SVC --> KAFKA

    ETA_SVC --> KAFKA

    KAFKA --> FLINK[Apache Flink]

    FLINK --> DATALAKE[Data Lake]

    FLINK --> FEATURE[Feature Store]
```

---

# Technologies Uber

| Brique               | Technologie                    |
| -------------------- | ------------------------------ |
| Front Web            | React                          |
| Mobile iOS           | Swift                          |
| Mobile Android       | Kotlin                         |
| API Gateway          | Envoy                          |
| RPC                  | gRPC                           |
| Backend              | Go, Java                       |
| Service Mesh         | Istio                          |
| Orchestration        | Kubernetes                     |
| Event Streaming      | Kafka                          |
| Real-time Processing | Apache Flink                   |
| Cache                | Redis                          |
| User DB              | MySQL                          |
| Trip DB              | Cassandra                      |
| Search               | Elasticsearch                  |
| Geospatial           | H3                             |
| ML Serving           | Michelangelo                   |
| Monitoring           | M3                             |
| Tracing              | Jaeger                         |
| Cloud                | Multi-cloud + Datacenter privé |

---

# Le système de Matching Uber

Le coeur d'Uber est le dispatching.

```mermaid
flowchart LR

    Passenger[Passenger Request]

    Passenger --> MATCH[Matching Engine]

    MATCH --> GEO[Geo Service]

    GEO --> H3[H3 Cells]

    MATCH --> ETA[ETA Engine]

    ETA --> DRIVER1[Driver A]
    ETA --> DRIVER2[Driver B]
    ETA --> DRIVER3[Driver C]

    ETA --> SCORE[Scoring Engine]

    SCORE --> BEST[Best Driver]

    BEST --> Trip
```

---

# Tracking GPS temps réel Uber

```mermaid
sequenceDiagram

    Driver App->>Gateway: GPS Position
    Gateway->>Location Service: Update

    Location Service->>Redis: Store Last Position

    Location Service->>Kafka: Location Event

    Kafka->>Flink: Stream Processing

    Flink->>ETA Service: Recompute ETA

    ETA Service-->>Passenger App: Live ETA
```

---

# Architecture ML Uber (Michelangelo)

```mermaid
flowchart TB

    Kafka --> FeatureStore

    FeatureStore --> Training

    Training --> ModelRegistry

    ModelRegistry --> Serving

    Serving --> ETA

    Serving --> Pricing

    Serving --> Fraud

    Serving --> Dispatch
```

Uber est généralement considérée comme l'une des architectures distribuées les plus complexes au monde, car elle combine microservices, streaming temps réel, géospatial, machine learning et systèmes distribués à très faible latence. Airbnb est davantage optimisée pour la recherche, le catalogue, les réservations et les paiements à très grande échelle.

---
# Résumé global (pattern commun)

Presque toutes ces apps utilisent :

* **Load Balancer** → répartit trafic
* **Microservices** → découpage fonctionnel
* **Base distribuée** → scalabilité mondiale
* **Cache (Redis-like)** → rapidité
* **CDN** → contenu proche utilisateur
* **Message queues (Kafka/SQS)** → communication asynchrone
* **Machine Learning** → personnalisation

---

Si tu veux, je peux aussi te faire :

* une **architecture “Big Tech complète unifiée” (Google + Netflix + Amazon mix)**
* ou un **schéma d’architecture moderne type startup (MVP → scale)**
* ou encore **comment construire une app comme Netflix étape par étape**
