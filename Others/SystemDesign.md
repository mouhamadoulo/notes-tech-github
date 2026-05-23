# System Design pour Développeur Fullstack Java / Angular

> Guide complet des concepts à maîtriser pour booster ton profil — du fondamental à l'avancé.

---

## Table des matières

1. [Scalabilité](#1-scalabilité)
2. [Stockage & Données](#2-stockage--données)
3. [API & Communication](#3-api--communication)
4. [Fiabilité & Résilience](#4-fiabilité--résilience)
5. [System Design côté Java / Spring Boot](#5-system-design-côté-java--spring-boot)
6. [System Design côté Angular](#6-system-design-côté-angular)
7. [Ressources recommandées](#7-ressources-recommandées)

---

## 1. Scalabilité

La scalabilité répond à la question fondamentale : **comment ton système tient-il la montée en charge ?** C'est le premier sujet abordé dans tout entretien system design senior.

### 1.1 Scaling vertical vs horizontal

**Scaling vertical** (scale-up) : on augmente les ressources d'un seul serveur — plus de CPU, plus de RAM.

- Avantage : simple, aucun changement de code.
- Limite : plafond matériel atteint rapidement, coût exponentiel, point de défaillance unique.

**Scaling horizontal** (scale-out) : on ajoute des instances supplémentaires derrière un load balancer.

- Avantage : théoriquement illimité, haute disponibilité, moins coûteux à grande échelle.
- Limite : complexité applicative (état partagé, sessions, cohérence des données).

```
Vertical :   t3.medium → t3.xlarge → t3.2xlarge (limité)

Horizontal : 1 instance
             + Load Balancer
             ├── Instance A
             ├── Instance B
             └── Instance C   (extensible à l'infini)
```

En pratique, les architectures cloud-native misent sur le scale horizontal — c'est la base des conteneurs Docker et de Kubernetes.

---

### 1.2 Load Balancing

Un load balancer distribue le trafic entrant entre plusieurs instances backend. Il agit comme un point d'entrée unique pour les clients.

**Algorithmes de distribution courants :**

| Algorithme | Fonctionnement | Cas d'usage |
|---|---|---|
| Round-robin | Rotation séquentielle | Instances homogènes |
| Least connections | Redirige vers l'instance la moins chargée | Requêtes de durée variable |
| IP hash | La même IP va toujours sur la même instance | Sessions sans état partagé (sticky sessions) |
| Weighted | Pondération selon la capacité de l'instance | Instances hétérogènes |

**Types de load balancers :**

- **L4 (Transport)** : route sur IP/port — très rapide, pas de lecture du contenu HTTP.
- **L7 (Application)** : route sur l'URL, les headers, les cookies — plus intelligent (ex. : Nginx, AWS ALB).

En Java, Spring Cloud LoadBalancer (ou Ribbon anciennement) gère le load balancing côté client dans une architecture microservices.

---

### 1.3 Caching

Le cache stocke le résultat de calculs ou de requêtes coûteux en mémoire pour éviter de les recalculer à chaque fois.

**Stratégies principales :**

- **Cache-aside (lazy loading)** : l'application vérifie le cache, si absent (cache miss) elle interroge la DB et remplit le cache. C'est la stratégie par défaut avec Spring Cache.
- **Write-through** : l'écriture se fait simultanément en cache ET en base. Cohérence forte, mais latence d'écriture plus élevée.
- **Write-behind** : l'écriture se fait d'abord en cache, la DB est mise à jour de façon asynchrone. Très performant en écriture, risque de perte de données.
- **Read-through** : le cache interroge la DB lui-même si absent. Logique cachée côté cache.

**TTL (Time To Live)** : durée de vie d'une entrée en cache avant expiration. Crucial pour éviter les données obsolètes.

**Redis** est le standard pour Spring Boot :

```java
// application.yml
spring:
  cache:
    type: redis
  redis:
    host: localhost
    port: 6379

// Service
@Cacheable(value = "products", key = "#id")
public Product findById(Long id) {
    return productRepository.findById(id).orElseThrow();
}

@CacheEvict(value = "products", key = "#product.id")
public Product update(Product product) {
    return productRepository.save(product);
}
```

**CDN (Content Delivery Network)** : cache les assets statiques (images, JS, CSS) au plus proche de l'utilisateur géographiquement. Essentiel pour les apps Angular en production (Cloudflare, AWS CloudFront).

---

### 1.4 Sharding & Partitioning

Quand une seule base de données ne suffit plus — au-delà de dizaines de millions de lignes — on distribue les données sur plusieurs instances.

**Partitioning horizontal (sharding)** : chaque shard contient un sous-ensemble de lignes. La **shard key** détermine sur quel shard une donnée est stockée.

```
Shard key : user_id % 4

user_id 1 → Shard 0
user_id 2 → Shard 1
user_id 3 → Shard 2
user_id 4 → Shard 3
```

**Défis du sharding :**
- Les requêtes cross-shard (jointures entre shards) sont très coûteuses.
- Le rééquilibrage des shards est complexe (resharding).
- Le choix de la shard key est critique — une mauvaise clé crée des "hot spots".

**Partitioning vertical** : on sépare les colonnes sur différentes tables ou bases (ex. : séparer les données de profil utilisateur des données d'activité).

---

## 2. Stockage & Données

Le choix de la base de données est souvent la décision architecturale la plus impactante d'un système.

### 2.1 SQL vs NoSQL

**Bases SQL (relationnelles)** — PostgreSQL, MySQL, Oracle :

- Schéma fixe, relations entre tables, transactions ACID.
- Idéal pour les données structurées avec des relations complexes.
- Garanties ACID : Atomicity, Consistency, Isolation, Durability.
- Forte cohérence, mais scaling horizontal difficile.

**Bases NoSQL** :

| Type | Exemples | Cas d'usage |
|---|---|---|
| Document | MongoDB, CouchDB | Données semi-structurées, flexibilité de schéma |
| Clé-valeur | Redis, DynamoDB | Cache, sessions, données simples à accès rapide |
| Colonne large | Cassandra, HBase | Séries temporelles, logs, haute écriture distribuée |
| Graphe | Neo4j, Amazon Neptune | Réseaux sociaux, recommandations, relations complexes |

**Critères de choix :**

- Relations complexes et cohérence forte → SQL
- Volume massif de données, schema flexible → NoSQL document
- Hautes performances en lecture (cache) → Redis
- Haute disponibilité et écriture distribuée → Cassandra

---

### 2.2 Théorème CAP

Dans un système distribué, il est **impossible** de garantir simultanément les trois propriétés suivantes. Il faut en choisir deux :

- **C — Consistency** : tous les nœuds voient les mêmes données au même moment.
- **A — Availability** : chaque requête reçoit une réponse (pas nécessairement la plus récente).
- **P — Partition Tolerance** : le système continue de fonctionner malgré des pannes réseau entre nœuds.

En pratique, la tolérance aux partitions (P) est non négociable dans un système distribué réel. Le vrai choix est entre C et A :

```
CP (Consistency + Partition Tolerance) :
    PostgreSQL, MongoDB, Redis en mode cluster
    → En cas de partition, refuse les requêtes pour préserver la cohérence

AP (Availability + Partition Tolerance) :
    Cassandra, DynamoDB, CouchDB
    → En cas de partition, continue de répondre (données potentiellement obsolètes)
```

**Exemple concret** : un panier e-commerce peut tolérer de l'AP (éventuelle cohérence) — si le stock affiché est légèrement décalé, c'est acceptable. Un système bancaire doit être CP — on ne peut pas débiter deux fois.

---

### 2.3 Indexing & Optimisation des requêtes

Un index est une structure de données (généralement un B-Tree) qui accélère les recherches au prix d'un espace disque supplémentaire et d'un surcoût en écriture.

**Types d'index SQL :**

- **Simple** : `CREATE INDEX idx_email ON users(email);`
- **Composite** : index sur plusieurs colonnes — l'ordre des colonnes est crucial.
- **Covering index** : inclut toutes les colonnes nécessaires à la requête, évite de lire la table principale.
- **Partial index** : index filtré sur un sous-ensemble de lignes.

**Diagnostiquer une requête lente :**

```sql
EXPLAIN ANALYZE
SELECT * FROM orders o
JOIN users u ON o.user_id = u.id
WHERE o.status = 'PENDING'
  AND o.created_at > NOW() - INTERVAL '7 days';
```

**Le problème N+1 avec JPA/Hibernate** — le piège classique en Java :

```java
// ❌ Mauvais : 1 requête pour les commandes + N requêtes pour chaque utilisateur
List<Order> orders = orderRepository.findAll();
orders.forEach(o -> System.out.println(o.getUser().getName())); // N requêtes !

// ✅ Bon : une seule requête avec JOIN FETCH
@Query("SELECT o FROM Order o JOIN FETCH o.user WHERE o.status = :status")
List<Order> findByStatusWithUser(@Param("status") String status);

// ✅ Ou avec @EntityGraph
@EntityGraph(attributePaths = {"user"})
List<Order> findByStatus(String status);
```

---

### 2.4 CQRS & Event Sourcing

**CQRS (Command Query Responsibility Segregation)** : séparer les modèles d'écriture (commandes) et de lecture (queries).

```
Écriture → Command Handler → Base de données write (normalisée)
                            ↓ (synchronisation)
Lecture  → Query Handler  → Base de données read (dénormalisée, optimisée)
```

Avantage : on peut optimiser chaque côté indépendamment — la base de lecture peut être un index Elasticsearch pour la recherche full-text, pendant que la base d'écriture reste un PostgreSQL strict.

**Event Sourcing** : plutôt que de stocker l'état final d'une entité, on stocke la séquence de tous les événements qui ont conduit à cet état.

```
État classique :
    order { status: "SHIPPED", total: 150.00 }

Event Sourcing :
    OrderCreated   { total: 150.00, at: ... }
    PaymentReceived{ amount: 150.00, at: ... }
    OrderShipped   { trackingId: "XYZ", at: ... }
```

Avantages : audit trail complet, possibilité de rejouer l'historique, debugging facilité. Inconvénient : complexité accrue.

---

## 3. API & Communication

### 3.1 REST vs GraphQL vs gRPC

**REST** — le standard universel :

- Ressources identifiées par des URLs, verbes HTTP (GET, POST, PUT, DELETE).
- Simple, universel, bien supporté par Angular via HttpClient.
- Inconvénient : over-fetching (trop de données) ou under-fetching (plusieurs appels nécessaires).

**GraphQL** :

- Le client spécifie exactement les champs dont il a besoin.
- Idéal pour Angular : un seul endpoint, les composants décrivent leur besoin en données.
- Réduit le nombre de requêtes réseau (pas de multiple REST calls).
- Courbe d'apprentissage plus élevée côté back (Spring for GraphQL depuis Spring Boot 2.7).

```graphql
# Le client Angular demande exactement ce dont il a besoin
query {
  user(id: "123") {
    name
    orders(last: 5) {
      id
      total
      status
    }
  }
}
```

**gRPC** :

- Protocole binaire (Protocol Buffers) — très performant.
- Idéal pour la communication inter-microservices (pas pour le front Angular directement).
- Contrats stricts avec les fichiers `.proto`.
- Moins adapté pour les API publiques consommées par un navigateur.

**Règle de décision :**

- API publique consommée par Angular → REST ou GraphQL
- Communication inter-services → gRPC ou Kafka (async)
- Besoin de flexibilité côté front → GraphQL

---

### 3.2 Message Queues — Kafka & RabbitMQ

La communication asynchrone via des files de messages **découple** les services : le producteur n'attend pas que le consommateur ait traité le message.

**RabbitMQ** — message broker classique :

- Garantit la livraison de chaque message à un consommateur.
- Idéal pour les tâches en arrière-plan : envoi d'emails, génération de PDF, notifications push.
- Modèle push — RabbitMQ pousse les messages aux consommateurs.

**Apache Kafka** — event streaming platform :

- Les événements sont persistés dans des **topics** et peuvent être relus.
- Conçu pour un débit massif (millions d'événements/seconde).
- Idéal pour les logs, l'audit trail, la synchronisation entre services.
- Modèle pull — les consommateurs lisent à leur propre rythme.

```java
// Producteur Spring Boot + Kafka
@Service
public class OrderService {
    private final KafkaTemplate<String, OrderEvent> kafkaTemplate;

    public void placeOrder(Order order) {
        orderRepository.save(order);
        kafkaTemplate.send("orders-topic", new OrderCreatedEvent(order.getId()));
    }
}

// Consommateur (peut être un autre microservice)
@KafkaListener(topics = "orders-topic", groupId = "notification-service")
public void handleOrderCreated(OrderCreatedEvent event) {
    emailService.sendConfirmation(event.getOrderId());
}
```

---

### 3.3 WebSockets & Server-Sent Events

**WebSockets** : connexion bidirectionnelle persistante entre client et serveur. Idéal pour les chats, les tableaux de bord temps réel, les jeux.

```java
// Spring Boot
@Configuration
@EnableWebSocketMessageBroker
public class WebSocketConfig implements WebSocketMessageBrokerConfigurer {
    @Override
    public void configureMessageBroker(MessageBrokerRegistry config) {
        config.enableSimpleBroker("/topic");
        config.setApplicationDestinationPrefixes("/app");
    }
}
```

```typescript
// Angular
import { webSocket } from 'rxjs/webSocket';

const socket$ = webSocket('ws://localhost:8080/ws');
socket$.subscribe(message => console.log(message));
socket$.next({ type: 'PING' });
```

**Server-Sent Events (SSE)** : flux unidirectionnel du serveur vers le client. Plus simple que WebSocket pour les notifications, les live feeds, le suivi de statut.

```java
// Spring Boot — SSE avec Flux (WebFlux)
@GetMapping(value = "/notifications", produces = MediaType.TEXT_EVENT_STREAM_VALUE)
public Flux<ServerSentEvent<String>> streamNotifications() {
    return Flux.interval(Duration.ofSeconds(1))
        .map(seq -> ServerSentEvent.<String>builder()
            .data("Notification " + seq)
            .build());
}
```

---

### 3.4 API Gateway

L'API Gateway est le **point d'entrée unique** de toutes les requêtes clientes vers les microservices. Elle centralise des préoccupations transversales :

- **Authentification & Autorisation** : vérification du JWT avant de router.
- **Rate Limiting** : limite le nombre de requêtes par client/IP.
- **Routing** : redirige vers le bon microservice selon l'URL.
- **Load Balancing** : distribue entre les instances d'un service.
- **Logging & Monitoring** : trace toutes les requêtes entrantes.
- **Circuit Breaking** : coupe les appels vers un service défaillant.

```yaml
# Spring Cloud Gateway — application.yml
spring:
  cloud:
    gateway:
      routes:
        - id: user-service
          uri: lb://USER-SERVICE
          predicates:
            - Path=/api/users/**
          filters:
            - AuthenticationFilter
            - name: RequestRateLimiter
              args:
                redis-rate-limiter.replenishRate: 10
                redis-rate-limiter.burstCapacity: 20
```

---

## 4. Fiabilité & Résilience

### 4.1 Circuit Breaker

Sans circuit breaker, si le service A appelle le service B qui est lent ou en erreur, le service A va accumuler des threads bloqués jusqu'à tomber lui aussi — c'est la **cascade de pannes**.

Le Circuit Breaker coupe le circuit (arrête d'appeler le service défaillant) et renvoie rapidement une réponse de fallback.

**Trois états :**

```
CLOSED (normal) → trop d'erreurs → OPEN (coupe le circuit)
OPEN → après un délai → HALF-OPEN (teste quelques requêtes)
HALF-OPEN → succès → CLOSED / échec → OPEN
```

**Resilience4j avec Spring Boot** (le standard actuel) :

```java
// application.yml
resilience4j:
  circuitbreaker:
    instances:
      payment-service:
        failureRateThreshold: 50          # Ouvre si 50% d'erreurs
        waitDurationInOpenState: 30s      # Reste ouvert 30 secondes
        slidingWindowSize: 10             # Sur les 10 derniers appels

// Service
@CircuitBreaker(name = "payment-service", fallbackMethod = "paymentFallback")
public PaymentResult processPayment(Order order) {
    return paymentClient.charge(order);
}

public PaymentResult paymentFallback(Order order, Exception e) {
    // Réponse de secours : mettre en file d'attente, notifier l'utilisateur...
    return PaymentResult.pending("Payment queued for retry");
}
```

Resilience4j fournit aussi **Retry**, **Rate Limiter**, **Bulkhead** (limite la concurrence) et **TimeLimiter** (timeout) — souvent utilisés ensemble.

---

### 4.2 Eventual Consistency

Dans un système distribué, maintenir une cohérence forte (chaque lecture retourne la valeur la plus récente) a un coût en latence et disponibilité. L'**éventuelle cohérence** accepte que les nœuds soient temporairement désynchronisés, mais garantissent de converger vers le même état.

**Pattern Saga** pour les transactions distribuées : quand une opération couvre plusieurs microservices, on ne peut pas utiliser une transaction ACID classique. La Saga orchestre une séquence de transactions locales avec des **compensations** en cas d'échec.

```
Commande passée :
    1. OrderService.createOrder()    ✅
    2. PaymentService.charge()       ✅
    3. InventoryService.reserve()    ❌ (rupture de stock)

Compensation (rollback distribué) :
    → PaymentService.refund()
    → OrderService.cancelOrder()
```

Deux implémentations : **Choreography** (chaque service écoute et réagit aux événements) ou **Orchestration** (un orchestrateur central coordonne les étapes — plus simple à déboguer).

---

### 4.3 Observabilité — Les 3 piliers

Sans observabilité, un système distribué devient une boîte noire impossible à déboguer en production.

**1. Logs structurés** :

Préférer les logs JSON (structurés) aux logs texte classiques — ils sont indexables et requêtables.

```java
// Logback + SLF4J avec contexte MDC
MDC.put("userId", userId);
MDC.put("requestId", requestId);
log.info("Order created", kv("orderId", orderId), kv("total", total));
```

Stack recommandée : **ELK** (Elasticsearch + Logstash + Kibana) ou **Loki + Grafana**.

**2. Métriques** :

```java
// Spring Boot Actuator + Micrometer + Prometheus
management:
  endpoints:
    web:
      exposure:
        include: health, metrics, prometheus
```

Grafana pour les dashboards visuels. Alerting sur les anomalies (taux d'erreur, latence p99, saturation mémoire).

**3. Distributed Tracing** :

Trace une requête à travers tous les microservices. Chaque appel reçoit un **trace ID** et un **span ID** propagés dans les headers HTTP.

```java
// Spring Boot 3 + Micrometer Tracing (remplace Sleuth)
// Configuration automatique — ajouter la dépendance suffit
implementation 'io.micrometer:micrometer-tracing-bridge-otel'
implementation 'io.opentelemetry.instrumentation:opentelemetry-spring-boot-starter'
```

Outils : **Jaeger**, **Zipkin**, **AWS X-Ray**.

---

### 4.4 Rate Limiting

Protège l'API contre les abus, les attaques DDoS et la surcharge accidentelle.

**Algorithmes :**

- **Token Bucket** : un seau se remplit à un débit fixe. Chaque requête consomme un token. Autorise les pics temporaires.
- **Sliding Window** : compte les requêtes dans une fenêtre temporelle glissante. Plus précis.
- **Fixed Window** : compte sur une fenêtre fixe (ex. 100 req/minute). Simple mais peut autoriser 200 requêtes à cheval sur deux fenêtres.

```java
// Spring Cloud Gateway + Redis Rate Limiter
@Bean
public KeyResolver userKeyResolver() {
    return exchange -> Mono.just(
        exchange.getRequest().getHeaders().getFirst("X-User-Id")
    );
}
```

Côté Angular, implémenter un **exponential backoff** pour les retry côté client :

```typescript
// RxJS — retry avec délai exponentiel
httpClient.get('/api/data').pipe(
  retryWhen(errors =>
    errors.pipe(
      delayWhen((_, attempt) => timer(Math.pow(2, attempt) * 1000)),
      take(3)
    )
  )
)
```

---

## 5. System Design côté Java / Spring Boot

### 5.1 Microservices vs Monolithe

**Monolithe modulaire** : toute l'application est déployée en un seul artefact, mais le code est organisé en modules bien isolés. C'est souvent le meilleur point de départ.

- Avantages : simple à développer, tester, déboguer et déployer.
- Adapté pour la majorité des projets jusqu'à plusieurs dizaines de développeurs.

**Microservices** : chaque service est déployé indépendamment, possède sa propre base de données, communique via API ou events.

- Avantages : déploiement indépendant, scalabilité par service, isolation des pannes.
- Coût : complexité opérationnelle très élevée (CI/CD par service, observabilité distribuée, gestion des transactions distribuées).

**Critères pour passer aux microservices :**

- Équipe > 20 développeurs et besoin de déploiements indépendants
- Parties du système avec des besoins de scaling très différents
- Domaines fonctionnels clairement séparés (Domain-Driven Design)

**Découpage par domaine (DDD Bounded Contexts)** :

```
Mauvais découpage (technique) :   UserController, ProductController, OrderController → 1 service each
Bon découpage (domaine)       :   OrderManagement, Catalog, UserProfile, Payment → 1 service each
```

---

### 5.2 Gestion des transactions

**@Transactional** en Spring : garantit qu'un ensemble d'opérations s'exécute de façon atomique.

```java
@Transactional
public OrderResult placeOrder(OrderRequest request) {
    Order order = orderRepository.save(new Order(request));
    inventoryService.reserve(request.getItems());   // Si exception ici...
    paymentService.charge(request.getPayment());     // ...tout est rollback
    return new OrderResult(order.getId());
}
```

**Niveaux d'isolation** (du moins strict au plus strict) :

| Niveau | Dirty Read | Non-repeatable Read | Phantom Read |
|---|---|---|---|
| READ_UNCOMMITTED | Possible | Possible | Possible |
| READ_COMMITTED | Protégé | Possible | Possible |
| REPEATABLE_READ | Protégé | Protégé | Possible |
| SERIALIZABLE | Protégé | Protégé | Protégé |

`READ_COMMITTED` est la valeur par défaut dans PostgreSQL — suffisant pour la plupart des cas.

**Optimistic vs Pessimistic Locking :**

```java
// Optimistic (léger, idéal si conflits rares)
@Entity
public class Product {
    @Version
    private Long version; // Spring lance OptimisticLockException si conflit
}

// Pessimistic (verrouille la ligne, idéal si conflits fréquents)
@Lock(LockModeType.PESSIMISTIC_WRITE)
Optional<Product> findById(Long id);
```

---

### 5.3 Sécurité — JWT, OAuth2, Spring Security

**Architecture recommandée :**

```
Client Angular
    → [API Gateway] (valide le JWT)
        → [Microservice A] (confiance implicite, pas de re-vérification)
        → [Microservice B]
```

**JWT (JSON Web Token)** : token auto-porteur contenant les claims (userId, roles, expiration). Stateless — pas besoin de session côté serveur.

```java
// Spring Security 6 + JWT
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    public SecurityFilterChain filterChain(HttpSecurity http) throws Exception {
        return http
            .csrf(csrf -> csrf.disable())
            .sessionManagement(s -> s.sessionCreationPolicy(STATELESS))
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/api/auth/**").permitAll()
                .requestMatchers("/api/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .oauth2ResourceServer(oauth2 -> oauth2.jwt(Customizer.withDefaults()))
            .build();
    }
}
```

**OAuth2 / OIDC** avec Keycloak ou Auth0 : délègue l'authentification à un Identity Provider. Spring Boot s'intègre nativement.

**Bonnes pratiques :**

- Access token court (15 min) + refresh token long (7 jours).
- Stocker le refresh token en HttpOnly cookie (inaccessible au JS).
- Valider la signature JWT (RS256/ES256), pas seulement le contenu.
- Ne jamais stocker de données sensibles dans le payload JWT (il est décodable en base64).

---

### 5.4 Performance JVM & Spring Boot

**HikariCP (connection pool)** : chaque connexion à la base est coûteuse à établir. Le pool maintient des connexions ouvertes et réutilisables.

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20     # Règle générale : (CPU cores * 2) + nb disques
      minimum-idle: 5
      connection-timeout: 30000
      idle-timeout: 600000
```

**Async avec CompletableFuture** :

```java
@Async
@Transactional(readOnly = true)
public CompletableFuture<List<Order>> findOrdersAsync(Long userId) {
    return CompletableFuture.completedFuture(orderRepository.findByUserId(userId));
}
```

**Reactive avec WebFlux** : pour les appels I/O massivement concurrents (ex. appel à 10 APIs externes en parallèle), WebFlux utilise un modèle non-bloquant bien plus efficace que le modèle thread-par-requête de Spring MVC.

```java
// WebClient (non-bloquant) vs RestTemplate (bloquant)
WebClient.create("https://api.example.com")
    .get()
    .uri("/users/{id}", userId)
    .retrieve()
    .bodyToMono(User.class)
    .timeout(Duration.ofSeconds(5));
```

**GC Tuning** : G1GC est le collecteur par défaut depuis Java 9. Pour les applications à faible latence, ZGC (Java 15+) offre des pauses < 1ms.

```bash
# JVM options recommandées pour Spring Boot en production
-XX:+UseG1GC
-Xms512m -Xmx2g
-XX:MaxGCPauseMillis=200
-XX:+HeapDumpOnOutOfMemoryError
```

---

## 6. System Design côté Angular

### 6.1 Architecture modulaire

**Structure recommandée :**

```
src/app/
├── core/               # Services singleton (AuthService, HttpInterceptors, Guards)
│   └── core.module.ts  # Importé une seule fois dans AppModule
├── shared/             # Composants, pipes, directives réutilisables
│   └── shared.module.ts
├── features/           # Modules fonctionnels (lazy-loaded)
│   ├── orders/
│   │   ├── orders.module.ts
│   │   ├── orders-routing.module.ts
│   │   ├── components/
│   │   └── services/
│   └── catalog/
└── app-routing.module.ts
```

**Lazy Loading** : les modules de features sont chargés à la demande, pas au démarrage.

```typescript
// app-routing.module.ts
const routes: Routes = [
  {
    path: 'orders',
    loadChildren: () => import('./features/orders/orders.module')
      .then(m => m.OrdersModule)
  }
];
```

**Standalone Components (Angular 17+)** : l'approche moderne qui remplace les NgModules — plus simple, tree-shakable, adapté à la composition fine.

```typescript
@Component({
  selector: 'app-order-card',
  standalone: true,
  imports: [CommonModule, RouterModule],
  template: `...`
})
export class OrderCardComponent {}
```

**Monorepo avec Nx** : pour les projets multi-applications (ex. backoffice + app client Angular + lib partagée), Nx gère les dépendances entre projets et optimise le build.

---

### 6.2 State Management

**Quand a-t-on besoin d'un state manager ?**

Si l'état est local à un composant ou partagé entre 2-3 composants proches → **@Input/@Output ou service simple avec BehaviorSubject**.

Si l'état est global, partagé entre des parties distantes de l'app, ou si les flux de données deviennent complexes → **NgRx ou Signals**.

**NgRx (Redux pattern)** :

```typescript
// Action
export const loadOrders = createAction('[Orders] Load Orders');
export const loadOrdersSuccess = createAction('[Orders] Load Success',
  props<{ orders: Order[] }>());

// Reducer
export const ordersReducer = createReducer(
  initialState,
  on(loadOrdersSuccess, (state, { orders }) => ({ ...state, orders, loaded: true }))
);

// Effect
loadOrders$ = createEffect(() =>
  this.actions$.pipe(
    ofType(loadOrders),
    switchMap(() => this.orderService.getAll().pipe(
      map(orders => loadOrdersSuccess({ orders }))
    ))
  )
);
```

**Signals (Angular 17+)** — l'approche moderne, plus simple :

```typescript
@Component({ ... })
export class OrderListComponent {
  orders = signal<Order[]>([]);
  total = computed(() => this.orders().reduce((sum, o) => sum + o.total, 0));

  loadOrders() {
    this.orderService.getAll().subscribe(data => this.orders.set(data));
  }
}
```

---

### 6.3 Performance Angular

**OnPush Change Detection** : par défaut, Angular vérifie tous les composants à chaque événement. OnPush ne re-rend un composant que si ses `@Input` changent par référence ou si un Observable émet.

```typescript
@Component({
  selector: 'app-order-list',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <app-order-card
      *ngFor="let order of orders; trackBy: trackByOrderId"
      [order]="order">
    </app-order-card>
  `
})
export class OrderListComponent {
  trackByOrderId(index: number, order: Order): number {
    return order.id; // Evite le re-rendu de tous les éléments quand la liste change
  }
}
```

**Virtual Scroll** (Angular CDK) : pour les listes longues, ne rend que les éléments visibles dans le viewport.

```html
<cdk-virtual-scroll-viewport itemSize="72" style="height: 400px">
  <app-order-row *cdkVirtualFor="let order of orders" [order]="order"/>
</cdk-virtual-scroll-viewport>
```

**Optimisation des images** : utiliser `NgOptimizedImage` (Angular 15+) — lazy loading natif, prévention du layout shift, preload des images critiques.

```html
<img ngSrc="product.jpg" width="400" height="300" priority />
```

**SSR avec Angular Universal** : le rendu serveur améliore le First Contentful Paint et le SEO.

---

### 6.4 Résilience côté client

Le front doit anticiper les pannes et lenteurs du back — un backend lent ne doit pas figer l'application.

**RxJS — Retry, Timeout, Fallback** :

```typescript
@Injectable({ providedIn: 'root' })
export class OrderService {
  getOrders(): Observable<Order[]> {
    return this.http.get<Order[]>('/api/orders').pipe(
      timeout(5000),                              // Abandonne après 5 secondes
      retry({ count: 2, delay: 1000 }),           // Réessaie 2 fois avec 1s de délai
      catchError(err => {
        console.error('Orders unavailable', err);
        return of([]);                            // Retourne un tableau vide en fallback
      })
    );
  }
}
```

**HTTP Interceptor** — centraliser la gestion des erreurs :

```typescript
@Injectable()
export class ErrorInterceptor implements HttpInterceptor {
  intercept(req: HttpRequest<any>, next: HttpHandler): Observable<HttpEvent<any>> {
    return next.handle(req).pipe(
      catchError((error: HttpErrorResponse) => {
        if (error.status === 401) this.authService.logout();
        if (error.status === 503) this.notificationService.showError('Service temporairement indisponible');
        return throwError(() => error);
      })
    );
  }
}
```

**Skeleton Loaders** : afficher des placeholders animés pendant le chargement — bien meilleure UX qu'un spinner seul.

**Service Workers & Offline-first** : avec `@angular/pwa`, mettre en cache les assets et réponses API critiques pour fonctionner sans connexion ou avec une connexion dégradée.

---

## 7. Ressources recommandées

### Livres

- **Designing Data-Intensive Applications** — Martin Kleppmann : la référence absolue du domaine, couvre tous les concepts de ce guide en profondeur.
- **System Design Interview (Vol. 1 & 2)** — Alex Xu : très orienté entretien, avec des cas pratiques step-by-step.
- **Building Microservices** — Sam Newman : la référence pour l'architecture microservices.
- **Clean Architecture** — Robert C. Martin : principes intemporels applicables en Java.

### Ressources en ligne

- **ByteByteGo** (YouTube & blog) : illustrations claires des concepts system design, très accessible.
- **High Scalability** (highscalability.com) : retours d'expérience des grandes architectures en production.
- **Spring Blog** (spring.io/blog) : articles officiels sur les patterns Spring Boot.
- **Martin Fowler** (martinfowler.com) : articles de référence sur les patterns architecturaux (CQRS, Event Sourcing, Microservices).

### Pratique

- **Exercice de design** : s'entraîner à concevoir des systèmes connus — URL shortener, fil d'actualité Twitter, système de réservation. Estimer les charges, choisir les composants, justifier les décisions.
- **Contribuer à un projet open source Spring** : la meilleure façon de voir ces patterns appliqués en production.
- **Architectural Decision Records (ADR)** : documenter les décisions architecturales dans ton équipe — signal fort de maturité pour un profil senior.

---

> **Feuille de route** : commence par maîtriser les fondamentaux (caching, load balancing, indexing, REST) puis monte progressivement vers les patterns avancés (CQRS, Saga, Kafka). L'objectif n'est pas de tout mémoriser, mais de savoir **pourquoi** et **quand** utiliser chaque outil.