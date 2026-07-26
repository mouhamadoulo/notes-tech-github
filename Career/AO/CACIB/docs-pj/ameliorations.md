# Axes d'amélioration — performance, résilience et bonnes pratiques

> Audit du code présent sur la branche `features/frontend-version-1` (juillet 2026).
> Hypothèse de travail : montée en charge de l'ingestion IBM MQ (de quelques
> centaines à plusieurs milliers de messages/minute, table `payment_messages`
> dépassant le million de lignes).
>
> Chaque point est référencé par un identifiant (`B1`, `D3`, `F2`…) repris dans le
> plan d'action priorisé en fin de document. Les références de fichiers pointent
> vers l'état du code au moment de l'audit.
>
> **Réserve — `A1` :** le sujet place explicitement « la gestion de l'authentification et des
> autorisations » *hors périmètre*. La chaîne Spring Security mise en place au titre de `A1`
> a donc été **entièrement retirée** (starters, `SecurityConfig`, jetons JWT, comptes, rôles,
> écran de connexion). Seuls subsistent le durcissement de l'actuator (`env` non exposé) et
> la politique CORS, déplacée dans `config/CorsConfig`. Les paragraphes `A1` ci-dessous sont
> conservés en l'état comme trace de l'audit, mais ne décrivent plus le code.

---
✅ (effectuée)

## 1. Backend — Spring Boot 4.1 / Java 21

### 1.1 Résilience de l'ingestion MQ ✅

#### B1 — Toute erreur de traitement détruit le message (criticité : haute) ✅

`mq/PaymentMessageListener.java:38-62` enveloppe l'intégralité du traitement dans
un `try { … } catch (Exception e) { log.error(…) }` et une violation de contrainte
de validation provoque un simple `return` (ligne 51). Le listener ne relance
jamais : la session JMS est donc systématiquement committée et **le message est
définitivement perdu** dès que PostgreSQL est indisponible, que le payload est
mal formé ou que la validation échoue. Aucun rejeu n'est possible puisque rien
n'est persisté.

Correction recommandée :

1. Distinguer deux familles d'erreurs.
   - **Erreurs définitives** (JSON illisible, validation métier en échec) : le
     rejeu ne servira à rien. Persister une ligne en `FAILED` avec le payload brut
     et `errorMessage`, ou republier immédiatement vers la DLQ applicative, puis
     acquitter.
   - **Erreurs transitoires** (base indisponible, timeout, deadlock) : laisser
     l'exception remonter pour que la session transactée effectue un rollback et
     que le message soit redélivré.
2. Encadrer la redélivrance côté queue manager pour éviter la boucle de message
   empoisonné : `BOTHRESH` (seuil de backout) et `BOQNAME` sur
   `PAYMENT.REQUEST.QUEUE`, à déclarer dans `infra/mq/payment-queues.mqsc`.
   Sans ce garde-fou, un message toxique boucle indéfiniment et sature les
   consommateurs.
3. Brancher un `ErrorHandler` sur la factory de listeners pour tracer les rollbacks
   et alimenter un compteur Micrometer.

#### B2 — La propriété d'acquittement configurée n'existe plus en Spring Boot 4 ✅

`resources/application.yaml:18-20` et `application-dev.example.yaml:15-17`
déclarent :

```yaml
spring:
  jms:
    listener:
      acknowledge-mode: auto
```

Vérification faite sur `spring-boot-jms-4.1.0.jar` : la classe
`JmsProperties$Listener` n'expose plus de `setAcknowledgeMode(…)`; la clé ne
subsiste que dans les métadonnées de dépréciation (dépréciée depuis 3.2,
remplacée par `spring.jms.listener.session.acknowledge-mode`). **La ligne
actuelle n'est donc pas liée** : elle est ignorée silencieusement.

Conséquence : c'est le défaut de `DefaultJmsListenerContainerFactoryConfigurer`
qui s'applique, à savoir `sessionTransacted = true` en l'absence de gestionnaire
de transactions JTA. Le comportement réel n'est pas « auto ack » comme le laisse
croire la configuration (et la documentation projet) mais une **session
transactée**. C'est en réalité le bon mode pour la correction `B1` — il faut
simplement l'assumer explicitement :

```yaml
spring:
  jms:
    listener:
      session:
        transacted: true          # explicite : rollback = redélivrance
      min-concurrency: ${MQ_MIN_CONCURRENCY:5}
      max-concurrency: ${MQ_MAX_CONCURRENCY:10}
      receive-timeout: 5s
```

À contrôler également : `spring.jms.listener.concurrency` est dépréciée de la
même façon (remplacée par `min-concurrency`).

#### B3 — Parallélisme figé dans le code et désaccordé du pool JDBC ✅

`PaymentMessageListener.java:33` fixe `concurrency = "5-10"` en dur. Deux
problèmes :

- La valeur n'est pas pilotable par environnement (le dimensionnement d'un poste
  de dev et d'une prod n'ont rien à voir) — l'externaliser en
  `${ibm.mq.concurrency}` ou via `spring.jms.listener.min/max-concurrency`.
- Le pool HikariCP reste sur son défaut de **10 connexions**. À 10 consommateurs
  JMS actifs, l'ingestion consomme tout le pool et les requêtes REST du dashboard
  attendent une connexion. Règle de dimensionnement : `maximumPoolSize ≥
  max-concurrency + threads HTTP susceptibles de toucher la base en parallèle`,
  borné par `max_connections` PostgreSQL.

```yaml
spring:
  datasource:
    hikari:
      maximum-pool-size: 20
      minimum-idle: 5
      connection-timeout: 3000
      leak-detection-threshold: 20000
```

#### B4 — Pas d'idempotence explicite ✅

`entity/PaymentMessage.java:25-26` pose `messageId` en `unique = true`, ce qui est
une bonne base, mais aucun code ne traite le cas du doublon. En redélivrance
(rollback JMS, reprise après incident), `saveMessage` lève une
`DataIntegrityViolationException` interceptée par le `catch` générique de `B1` :
le doublon est signalé comme une erreur de traitement, ce qui pollue les logs et,
une fois `B1` corrigé, provoquerait un rollback puis une boucle infinie.

Implémenter une insertion idempotente explicite :

```java
@Modifying
@Query(value = """
    INSERT INTO payment_messages (message_id, reference, message_type, status,
                                  payload, retry_count, received_at, updated_at)
    VALUES (:messageId, :reference, :messageType, :status, :payload, 0, :now, :now)
    ON CONFLICT (message_id) DO NOTHING
    """, nativeQuery = true)
int insertIfAbsent(...);
```

Le retour `0` signifie « déjà traité » et permet d'acquitter sans erreur.

#### B5 — Une transaction et un aller-retour SQL par message ✅

`service/PaymentMessageService.java:36-40` fait un `repository.save()` unitaire,
sans batch Hibernate ni `reWriteBatchedInserts`. À volumétrie élevée, le coût est
dominé par les allers-retours réseau et les commits.

```yaml
spring:
  jpa:
    properties:
      hibernate:
        jdbc.batch_size: 50
        order_inserts: true
  datasource:
    url: ${DB_URL}?reWriteBatchedInserts=true
```

Pour aller plus loin : consommation par lots (`receiveTimeout` + accumulation
côté listener, ou `JmsTemplate.receiveNoWait` dans une boucle) et insertion
groupée. Le gain typique sur ce genre de flux est d'un ordre de grandeur.

#### B6 — `ObjectMapper` maison, strict et non aligné sur Spring ✅

`config/JacksonConfig.java:11-21` déclare un `@Bean ObjectMapper` construit à la
main. Ce bean **remplace celui auto-configuré par Spring Boot** et s'applique donc
aussi bien à la désérialisation MQ qu'à la sérialisation REST. Effets de bord :

- perte des réglages Boot (dates ISO plutôt que timestamps numériques, modules
  détectés automatiquement, `JacksonProperties`) ;
- `FAIL_ON_UNKNOWN_PROPERTIES` reste actif : **tout champ ajouté par un émetteur
  amont fait échouer la désérialisation**, donc perdre le message (cf. `B1`). Pour
  un contrat d'entrée bancaire amené à évoluer, c'est un couplage fort et fragile.

Préférer un customizer, qui conserve les défauts du framework :

```java
@Bean
Jackson2ObjectMapperBuilderCustomizer paymentJacksonCustomizer() {
    return builder -> builder
        .featuresToDisable(DeserializationFeature.FAIL_ON_UNKNOWN_PROPERTIES)
        .modules(new JavaTimeModule());
}
```

Si un mapper distinct est réellement souhaité pour le canal MQ, le déclarer en
bean qualifié (`@Qualifier("mqObjectMapper")`) sans écraser le bean principal.

#### B7 — Publication DLQ fragile ✅

`mq/DeadLetterPublisher.java:35` appelle `message.getRetryCount()` sur un
`Integer` dans `setIntProperty` : `NullPointerException` si le compteur est nul
(possible pour les lignes créées avant l'ajout de la valeur par défaut). L'échec
de publication n'est que loggé (`ligne 46-51`) : le statut `DEAD_LETTER` est posé
en base alors que le message n'est jamais arrivé sur la DLQ, donc **divergence
silencieuse entre la base et le broker**.

Corriger le `null` (`Objects.requireNonNullElse(retryCount, 0)`), ajouter un
compteur Micrometer `payment.dlq.publish.failures`, et prévoir une reprise
(table d'outbox ou tâche planifiée qui republie les `DEAD_LETTER` non confirmés).

---

### 1.2 Base de données et requêtes ✅

#### D1 — Index manquants sur toutes les colonnes réellement filtrées ✅

`entity/PaymentMessage.java:9-13` ne déclare qu'un index sur `reference`. Or les
requêtes de `repository/PaymentMessageRepository.java:20-29` filtrent sur
`status`, `receivedAt`, `(status, receivedAt)` et le tri par défaut du contrôleur
est `receivedAt DESC` (`controller/PaymentMessageController.java:46`). Au-delà de
quelques centaines de milliers de lignes, chaque page déclenche un *seq scan* +
tri complet.

```java
@Table(name = "payment_messages", indexes = {
    @Index(name = "idx_pm_reference",           columnList = "reference"),
    @Index(name = "idx_pm_received_at",         columnList = "received_at DESC"),
    @Index(name = "idx_pm_status_received_at",  columnList = "status, received_at DESC")
})
```

L'index composite `(status, received_at)` couvre à lui seul le filtre par statut
+ tri, et le filtre par statut seul.

#### D2 — Pagination par offset et `COUNT(*)` sur chaque appel ✅

`Page<T>` impose un `SELECT COUNT(*)` à chaque requête de liste, et `OFFSET n`
oblige PostgreSQL à parcourir les `n` premières lignes. Sur une table volumineuse,
le coût croît linéairement avec le numéro de page.

Deux leviers, cumulables :

- retourner un `Slice<T>` quand le total n'est pas indispensable (le front n'a
  besoin du total que pour l'affichage « x–y sur N ») ;
- pagination par curseur (*keyset*) pour la navigation séquentielle :
  `WHERE (received_at, id) < (:lastReceivedAt, :lastId) ORDER BY received_at DESC, id DESC LIMIT :size`,
  coût constant quelle que soit la profondeur.

#### D3 — Le payload complet est renvoyé dans les listes ✅

`dto/api/PaymentMessageDto.java:37-38` embarque `payload`, et
`mapper/PaymentMessageMapper.java:23` le recopie systématiquement. Une page de
100 lignes transporte donc 100 payloads complets ; le dashboard en demande 200
d'un coup (`frontend .../message.service.ts:115-122`). C'est le premier poste de
consommation réseau et mémoire de l'application.

Introduire une projection de liste sans payload :

```java
public interface PaymentMessageSummary {
    Long getId();
    String getMessageId();
    String getReference();
    String getMessageType();
    PaymentMessageStatus getStatus();
    Integer getRetryCount();
    LocalDateTime getReceivedAt();
    LocalDateTime getUpdatedAt();
}
```

Le payload complet reste servi par `GET /{id}`. Prévoir en complément une colonne
`payload_size` calculée à l'ingestion, pour que le front cesse de mesurer la
taille côté client (cf. `F1`).

#### D4 — `/stats` recalcule un `GROUP BY` complet à chaque appel ✅

`PaymentMessageService.java:71-81` s'appuie sur `countByStatus()`, soit un
agrégat sur toute la table. Le front l'appelle au chargement du dashboard, de la
liste, et **après chaque action unitaire** (`retry`, `updateStatus`,
`deleteMessage` — `message.service.ts:173, 199, 213`). À forte volumétrie, c'est
la requête la plus coûteuse du système, déclenchée le plus souvent.

Options par ordre de complexité croissante :

1. cache court (`spring-boot-starter-cache` + Caffeine, TTL 10–30 s, éviction sur
   écriture) — suffisant dans la majorité des cas ;
2. index partiel/couvrant permettant un *index-only scan* par statut ;
3. table de compteurs mise à jour par trigger ou par le service, pour un coût
   constant.

#### D5 — `batchRetryFailed` charge tous les échecs en mémoire ✅

`PaymentMessageService.java:90-97` fait `findAllByStatus(FAILED)` sans limite,
itère, puis `saveAll`. Avec 100 000 messages en échec, c'est 100 000 entités
managées et autant de payloads `TEXT` en mémoire heap — risque d'`OutOfMemoryError`
et de blocage de la JVM. La méthode publie de plus vers la DLQ **dans la boucle**,
un envoi JMS synchrone par message.

Traiter par lots bornés (`Pageable` de 500) dans une boucle, ou passer par une
mise à jour massive `@Modifying` pour les cas simples, avec une réponse
asynchrone (`202 Accepted` + suivi de tâche) plutôt qu'une requête HTTP qui dure
plusieurs minutes.

#### D6 — Schéma piloté par `ddl-auto` et absence de rétention ✅

`application.yaml:14-16` délègue le schéma à Hibernate (`update` dans les profils
dev/docker). En production, cela interdit toute revue de migration, tout rollback,
et peut verrouiller la table au démarrage. Introduire **Flyway** (ou Liquibase) et
figer `ddl-auto: validate`.

Sur la volumétrie : prévoir dès maintenant une stratégie de rétention —
partitionnement natif PostgreSQL par mois sur `received_at` (`DETACH PARTITION`
pour l'archivage) ou purge planifiée des messages `PROCESSED` de plus de N jours.
Sans cela, la table et ses index croissent indéfiniment et les temps de réponse
avec.

#### D7 — Transactions et concurrence ✅

Aucune annotation `@Transactional` dans tout le code applicatif (vérifié sur
`backend/src/main`). Conséquences :

- les lectures ne bénéficient pas de `readOnly = true` (pas de *flush* inutile,
  routage possible vers un réplica de lecture) ;
- `updateStatus` (`PaymentMessageService.java:110-122`) fait un *read-modify-write*
  hors transaction : deux appels concurrents sur le même message se écrasent
  mutuellement (*lost update*), et la publication DLQ (ligne 119) a lieu hors du
  périmètre transactionnel — elle peut réussir alors que le commit échoue.

Ajouter `@Transactional(readOnly = true)` au niveau classe, `@Transactional` sur
les écritures, un champ `@Version` pour le verrouillage optimiste, et publier vers
la DLQ **après commit** (`TransactionSynchronizationManager` /
`@TransactionalEventListener(phase = AFTER_COMMIT)`).

#### D8 — `LocalDateTime` sur un flux bancaire ✅

L'entité et les DTO utilisent `LocalDateTime` (donc `timestamp without time zone`).
Sur un flux de paiement multi-fuseaux, et pour tout conteneur dont le `TZ` diffère
du poste de développement, cela produit des décalages silencieux et des tris
incohérents. Migrer vers `Instant`/`OffsetDateTime` et `timestamptz`.

---

### 1.3 API, sécurité et exploitabilité ✅

> Traitée. Écarts assumés par rapport aux recommandations initiales :
> - **`A1`** — **partiellement annulé depuis** : le sujet place l'authentification et les
>   autorisations *hors périmètre*, la chaîne Spring Security a donc été retirée (voir la note
>   en tête de document). Seul le durcissement de l'actuator subsiste : `env` reste retiré de
>   l'exposition, et `MANAGEMENT_PORT` permet d'isoler les endpoints d'exploitation sur un
>   port réservé au réseau interne.
> - **`A3`** — refus en **422** (et non 409, réservé au verrou optimiste) ; un statut identique
>   à l'actuel est accepté sans effet, pour que la commande reste idempotente.
> - **`A6`** — MDC et bridge Micrometer Tracing en place, **sans exportateur** : l'émission
>   vers un collecteur ne demande plus que `micrometer-tracing-exporter-otlp`.
> - **`A5`** — les jauges s'appuient sur l'agrégat déjà mis en cache (`D4`), sans quoi chaque
>   scrutation Prometheus relancerait un `GROUP BY` sur toute la table.
> - Le contrat de `PUT /{id}/status` change (`A4`) : le frontend a été adapté (DTO,
>   transitions proposées).

| Id | Constat | Recommandation |
|----|---------|----------------|
| `A1` ⚠️ | *Volet authentification annulé — hors périmètre du sujet.* Aucune authentification : `/api/v1/**`, `/swagger-ui.html` et l'actuator sont ouverts. `application.yaml:38-45` expose `env`, qui **révèle toute la configuration** (URL de base, identifiants MQ si présents dans l'environnement). | Ajouter `spring-boot-starter-security` (JWT/OAuth2 resource server). Retirer `env` de `management.endpoints.web.exposure.include`, ne garder que `health,info,prometheus`. Séparer le port de management. |
| `A2` ✅ | `exception/GlobalExceptionHandler.java:28-33` intercepte `Exception` et renvoie `ex.getMessage()` dans une réponse 500 → fuite d'information (messages SQL, chemins internes). Le handler capture aussi les exceptions gérées nativement par Spring, ce qui dégrade les 400/404/405 en 500. | Utiliser `ProblemDetail` (RFC 9457, natif Spring Framework 6+) et étendre `ResponseEntityExceptionHandler`. Message générique + identifiant de corrélation en 500. |
| `A3` ✅ | `PUT /{id}/status` accepte n'importe quelle valeur d'enum : aucune règle de transition n'est vérifiée (`RECEIVED → DEAD_LETTER` direct est accepté). | Formaliser la machine à états (map des transitions autorisées) et rejeter en 409/422. |
| `A4` ✅ | Le corps de `PUT /{id}/status` est une chaîne JSON brute (`controller:121`), ce qui oblige le front à envoyer `"\"PROCESSED\""` (`message.service.ts:192`). | Remplacer par un DTO `{ "status": "PROCESSED", "reason": "…" }`, validé par `@Valid`. |
| `A5` ✅ | Aucune métrique métier : impossible de connaître le débit d'ingestion, la latence de traitement, le taux d'échec ou la profondeur de file. | `MeterRegistry` : `Counter` (reçus/échecs/DLQ), `Timer` (durée de traitement), `Gauge` (lignes `RECEIVED`). Exposer `/actuator/prometheus`. |
| `A6` ✅ | Pas de corrélation entre un message MQ et les logs applicatifs. | Renseigner le MDC (`messageId`, `reference`, `JMSMessageID`) dans le listener, et activer le tracing (Micrometer Tracing + OpenTelemetry) sur la chaîne MQ → base → API. |
| `A7` ✅ | Pas de compression HTTP ni de garde-fous sur la taille de page : `?size=100000` est accepté tel quel. | `server.compression.enabled: true`, `spring.data.web.pageable.max-page-size: 200`, timeouts de requête. |
| `A8` ✅ | `/stats` et les listes sont sans cache HTTP. | `ETag` (`ShallowEtagHeaderFilter`) + `Cache-Control` court sur `/stats`. |

### 1.4 Build, packaging et tests ✅

> Traitée. Écarts assumés par rapport aux recommandations initiales :
> - **GC** : **G1** et non ZGC. En dessous de 4 Go de tas — le cas ici — ZGC n'apporte pas
>   de pause plus courte que G1 mais coûte davantage en mémoire résidente. Le choix reste
>   ouvert sans reconstruire l'image : `JAVA_OPTS="-XX:+UseZGC -XX:+ZGenerational"`.
> - **Healthcheck** : le groupe `readiness` inclut la base mais **pas** le broker. Une
>   coupure MQ suspend l'ingestion ; l'API continue de servir les lectures et les rejeux,
>   retirer l'instance du trafic aggraverait la panne au lieu de la contenir.
> - **pom.xml** : les versions étaient déjà remontées en `<properties>`, et toutes les
>   dépendances listées sauf Testcontainers étaient déjà là (sections 1.1 à 1.3).
>   `spring-boot-testcontainers` n'a finalement **pas** été retenu : `@ServiceConnection`
>   lit les champs annotés par réflexion dès la découverte des tests, ce qui initialise la
>   classe de test et démarre le conteneur **avant** l'évaluation de la condition « Docker
>   joignable » — les tests échouaient au lieu d'être ignorés. La connexion passe par
>   `@DynamicPropertySource`, et seuls `testcontainers` + `testcontainers-postgresql`
>   restent au `pom.xml`.
> - `@EnabledIfDockerAvailable` (Testcontainers 2.0.5) est **inutilisable avec JUnit 6** :
>   sa condition appelle `getRequiredTestClass()` au niveau conteneur de test, ce que JUnit
>   refuse (« required test class is not present »). Remplacé par `@RequiresDocker`, une
>   annotation locale composée sur `@EnabledIf`. Elle est portée par chaque classe concrète :
>   les conditions JUnit ne sont pas `@Inherited`.
> - **Tests** : `PaymentMessageListenerTest` existait déjà (section 1.1) ; le constat
>   ci-dessous était antérieur. Le tir IBM MQ (`PaymentMessageMqIT`) est **désactivé par
>   défaut** (`-Dmq.it=true`) : image de ~1 Go et gestionnaire de files long à démarrer,
>   hors de proportion avec une boucle de build. Les tests PostgreSQL, eux, tournent en CI.
> - **Non vérifié** : les `*IT` n'ont pas pu être exécutés au moment de l'écriture, le
>   démon Docker local répondant 500 sur toutes les routes de son API. `./mvnw verify` est
>   vert avec les 95 tests unitaires et les 16 tests d'intégration *skipped*.

- **Dockerfile** ✅ (`backend/Dockerfile`) : jar mono-couche (chaque build renvoie
  ~60 Mo de couche même si seul le code applicatif change), exécution en `root`,
  aucun réglage mémoire. Passer aux *layered jars* (`java -Djarmode=tools -jar
  app.jar extract`), ajouter un `USER 1001`, et
  `-XX:MaxRAMPercentage=75 -XX:+UseZGC` (ou G1 selon le profil de latence).
- **Healthcheck** ✅ (`docker-compose.yaml:86-91`) : le test `/dev/tcp/127.0.0.1/8080`
  ne vérifie que l'ouverture du socket. Utiliser
  `/actuator/health/readiness` avec `management.endpoint.health.probes.enabled: true`,
  pour que Compose (et plus tard Kubernetes) distingue *démarré* de *prêt*.
- **pom.xml** ✅ : versions de `mq-jms-spring-boot-starter` (4.1.0) et `springdoc`
  (2.8.9) codées en dur — les remonter en `<properties>`. Dépendances absentes et
  utiles : ~~`spring-boot-starter-security`~~ (hors périmètre), `spring-boot-starter-cache`,
  `spring-boot-testcontainers`, `micrometer-registry-prometheus`, `flyway-core`.
- **Tests** ✅ : aucun test du `PaymentMessageListener`, aucun test d'intégration
  MQ + PostgreSQL. Ajouter des Testcontainers (`PostgreSQLContainer` +
  `GenericContainer` IBM MQ) pour couvrir la redélivrance, l'idempotence et la
  bascule DLQ — ce sont précisément les comportements que les tests H2 actuels
  ne peuvent pas valider.
- **Charge** ✅ : aucun scénario de tir. Prévoir un injecteur MQ (script JMS ou
  `k6` sur l'API) mesurant messages/s soutenus, p95 des endpoints de liste et
  occupation du pool JDBC.
- **Propreté** ✅ : `backend/mqjms.log.0`, `mqjms.log.0.1`, `mqjms.log.1` (260 Ko)
  traînent dans le répertoire de travail. Non suivis par git, mais à ajouter au
  `.gitignore` et à rediriger via la configuration de trace du client MQ.

---

## 2. Frontend — Angular 22

### 2.1 Volume de données et réactivité ✅

> Traitée. Écarts assumés par rapport aux recommandations initiales :
> - **`F1`** — un seul endpoint d'agrégation (`GET /messages/stats/dashboard`) au lieu des trois
>   proposés (`/stats/hourly`, `/stats/types`, `/stats/retries`). Le dashboard est le seul
>   consommateur et affichait ces trois blocs ensemble : trois appels, trois entrées de cache et
>   trois `ETag` pour une seule vue. La réponse porte en plus les **alertes** (cinq derniers
>   `FAILED`/`DEAD_LETTER`) et `lastReceivedAt`, ce qui supprime le dernier usage de
>   l'échantillon de 200 messages — le dashboard passe de 3 appels (dont un de plusieurs Mo) à 2.
> - **`F1`** — `types` et `retries` sont calculés sur **toute la table**, pas sur une fenêtre :
>   c'est ce qui les rend cohérents avec le camembert des statuts, déjà global. Seul le volume
>   horaire porte sur une fenêtre (24 h glissantes, alignées sur l'heure pleine).
> - **`F2`** — le filtre client sur `payload` avait déjà disparu avec `D3` (les listes ne
>   transportent plus le payload). Restaient l'anti-rebond, ajouté (250 ms), et la recherche
>   serveur, faite tout de suite plutôt que « à terme » : sans elle, `F3` n'était pas atteignable.
>   Elle compare `lower(reference|messageId|messageType)` à `%fragment%`, donc sans index —
>   l'index trigramme (`pg_trgm` + GIN) reste à faire, l'extension n'étant pas garantie
>   disponible et H2 ne sachant pas l'exprimer.
> - **`F4`** — `Subject` + `switchMap` plutôt que `httpResource`. Le gain visé (annulation de la
>   requête en vol) est obtenu, sans réécrire les composants : les signaux `messages`, `stats`,
>   `currentPage`, `loading`… restent l'interface du service. `httpResource` aurait imposé de
>   dériver l'état depuis la ressource dans chaque vue, pour un bénéfice de forme.
> - **`F5`** — rafraîchissement périodique (30 s, suspendu onglet masqué) et **non** SSE : un
>   `SseEmitter` par client ne diffuse que les événements de l'instance qui le porte, donc
>   supposerait un bus derrière plusieurs répliques. La pastille du bandeau pilote le
>   rafraîchissement au lieu de promettre un temps réel inexistant, et seules les vues déjà
>   chargées sont rejouées.
> - **`F6`** — rendu conditionné par un signal de point de rupture et taille mémorisée, **sans**
>   `cdk-virtual-scroll-viewport` : la stratégie à hauteur fixe du CDK impose que le conteneur
>   défilant soit le viewport, ce qui obligerait à abandonner `<table>` (donc la sémantique
>   d'en-têtes et la colonne d'action figée) pour un gain qui n'apparaît qu'au-delà de ~100
>   lignes — borne que la taille de page maximale interdit déjà d'atteindre.
> - Effets de bord : `shared/ui/histogram` (bucketisation horaire côté client) est supprimé,
>   devenu mort ; le sélecteur de type et la « dernière réception » de la barre latérale
>   viennent maintenant du serveur.

#### F1 — Le dashboard télécharge 200 messages complets à chaque visite ✅

`features/messages/pages/dashboard/dashboard.page.ts:262-266` déclenche
`loadActivitySample(200)`, et toutes les visualisations (histogramme horaire,
répartition par type, buckets de rejeu, alertes) sont dérivées **côté client** de
cet échantillon (lignes 291-348). Chaque message transporte son payload complet
(cf. `D3`). Deux problèmes :

- le volume transféré est sans rapport avec l'information affichée (quelques
  dizaines de nombres) ;
- les chiffres sont **faux par construction** dès que la table dépasse 200
  lignes : le libellé « 200 derniers messages » masque le fait que le taux
  d'erreur et les KPI sont calculés sur un échantillon non représentatif.

Créer les agrégats côté backend (`GET /api/v1/messages/stats/hourly`,
`/stats/types`, `/stats/retries`), calculés en SQL sur la fenêtre voulue. Le
volume réseau tombe de plusieurs mégaoctets à quelques centaines d'octets et les
chiffres deviennent exacts.

#### F2 — La recherche parcourt les payloads à chaque frappe ✅

`services/message.service.ts:48-57` filtre sur `m.payload?.toLowerCase()` pour
chaque message de la page à chaque caractère saisi (`message-filter.component.ts:126`,
sans anti-rebond). Sur une page de 100 messages de quelques kilo-octets, chaque
frappe alloue et parcourt plusieurs centaines de kilo-octets.

- retirer `payload` du filtre client (ou pré-calculer un champ de recherche) ;
- appliquer un anti-rebond (~250 ms) ;
- à terme, déporter la recherche côté serveur (`?q=`) — c'est le seul moyen de
  chercher ailleurs que dans la page affichée.

#### F3 — Filtres client et compteurs serveur incohérents ✅

Le texte et le type filtrent **la page courante** (`message-list.page.ts:99-103`)
alors que les pastilles de statut affichent des compteurs **globaux** issus de
`/stats` (`message-filter.component.ts:49`). L'utilisateur voit « FAILED 1 240 »
puis 3 lignes après filtrage. Unifier : tous les filtres côté serveur, tous les
compteurs cohérents avec le filtre actif.

#### F4 — Requêtes concurrentes non annulées → résultats hors séquence ✅

Chaque appel de `loadMessages` (`message.service.ts:63-90`) lance un
`subscribe` indépendant. Un clic rapide sur « page suivante » ou sur deux en-têtes
de tri déclenche N requêtes dont **la dernière réponse arrivée gagne**, pas la
dernière demandée. Le tableau peut afficher une page qui ne correspond ni au
numéro ni au tri sélectionnés.

Corriger avec un flux unique (`Subject` de requêtes + `switchMap`) ou, plus
idiomatique en Angular 22, une ressource signal :

```ts
private readonly query = signal<ListQuery>({ page: 0, size: 20, sort: DEFAULT_SORT });

readonly page = httpResource<Page<PaymentMessage>>(() => ({
  url: API_CONFIG.messages,
  params: toParams(this.query()),
}));
```

`httpResource` annule automatiquement la requête précédente, expose `isLoading()`
/ `error()` / `value()` en signaux et supprime la gestion manuelle des drapeaux
`loading` / `error` (les trois signaux `loading`, `statsLoading`, `detailLoading`
disparaissent).

#### F5 — Données figées entre deux clics sur « Actualiser » ✅

Le bandeau affiche une pastille verte animée « Données <heure> »
(`layout/header/header.component.ts:25-29`) qui suggère un flux temps réel, alors
que le rafraîchissement est exclusivement manuel. Sur un flux à fort débit,
l'écran est périmé en permanence.

Ajouter soit un rafraîchissement périodique (`interval` + `switchMap`, suspendu
quand l'onglet est masqué via `document.visibilityState`), soit un vrai canal
poussé — un `SseEmitter` Spring sur `/api/v1/messages/stream` est peu coûteux à
mettre en place et supprime le polling.

#### F6 — Le tableau et les cartes mobiles sont rendus tous les deux ✅

`components/message-table/message-table.component.ts:32-93` génère le `<table>`
(lignes 32-66) **et** la liste de cartes (lignes 69-93) ; c'est le CSS qui masque
l'un ou l'autre (`@media (max-width: 700px)`, lignes 171-174). Le DOM et les
liaisons sont donc doublés en permanence — 100 lignes × 2 rendus, avec un appel
`payloadSize()` (encodage `TextEncoder` du payload entier) par ligne et par
rendu (`ligne 227`).

- conditionner par un signal de point de rupture (`@if (isMobile()) { … } @else { … }`) ;
- mémoriser la taille de payload (ou la recevoir du backend, cf. `D3`) ;
- pour les tailles de page 50/100, utiliser `cdk-virtual-scroll-viewport` —
  `@angular/cdk` est déjà une dépendance.

### 2.2 Conformité aux pratiques Angular 22 ✅

> Traitée. Écarts assumés par rapport aux recommandations initiales :
> - **`F8`** — trois lectures migrent vers `httpResource` (`/messages/{id}`, `/messages/types`,
>   `/config`) ; la liste, les compteurs et les agrégats **restent** sur `Subject` +
>   `switchMap`, conformément à l'écart déjà acté en `F4` : l'annulation y est acquise et les
>   composants consomment les signaux du service, que `httpResource` obligerait à redériver
>   depuis la ressource. Les `subscribe` restants sont des commandes (`POST`/`PUT`/`DELETE`)
>   plus le suivi du rejeu massif — c'est l'état cible décrit par la fiche.
> - **`F10`** — `provideAnimations()` est **supprimé**, pas remplacé par
>   `provideAnimationsAsync()` : celui-ci est déprécié depuis la 20.2 avec suppression annoncée
>   en v23, et la vérification demandée par la fiche est concluante — aucun `.mjs` d'Angular
>   Material 22 n'importe `@angular/animations` ni ne déclare de propriété d'hôte synthétique
>   (dialogues, `MatSnackBar` et `mat-select` compris). La dépendance est retirée du
>   `package.json` : elle est un pair **facultatif** de `@angular/platform-browser`.
> - **`F12`** — le rejeu ne couvre que `status 0` et `5xx`. Un `4xx` renverrait la même
>   réponse, et un dépassement du délai n'est pas rejoué : trois tentatives à 15 s
>   enchaîneraient 45 s d'attente. L'intercepteur est placé **en dernier**, pour rejouer la
>   requête définitive et borner le temps réellement passé sur le réseau.
> - **`F14`** — la ligne n'est pas rendue focalisable : l'action passe par un bouton réel dans
>   la cellule « Référence » (et un bouton dans chaque en-tête triable, avec `aria-sort`), ce
>   qui donne un ordre de tabulation correct sans casser la sémantique du tableau ni forcer un
>   rôle ARIA sur un `<tr>`. Le clic sur la ligne entière est conservé à la souris.
> - **`F15`** — budgets `initial` et `allScript` uniquement. `anyScript` s'applique aussi au
>   lot initial et déclencherait sur `main` avant tout *chunk* différé ; `bundle` exige un nom
>   de lot, que le hachage rend instable. `allScript` est ce qui détecte réellement une
>   régression de poids dans un lot paresseux.
> - **`F17`** — deux blocs `@defer (on viewport)` sur le dashboard **et** le tiroir de détail
>   en `@defer (when …)` sur la liste : ce dernier est le gain mesurable (le tiroir et la
>   directive d'animation de liste sortent du lot de la page).
> - Vérifications : `npm run build` sans avertissement de budget (lot initial 575 → 553 ko,
>   tiroir et directive d'animation devenus paresseux), `npm run test -- --no-watch` vert
>   (36 tests, dont 6 nouveaux sur l'intercepteur de résilience et l'annulation des lectures
>   unitaires). `provideNoopAnimations()` disparaît de `app.spec.ts` avec la dépendance.

**Déjà en place, à conserver** : composants *standalone*, routes en `loadComponent`,
état en signaux, `input()`/`output()`, nouveau flux de contrôle `@if`/`@for` avec
`track`, imports dynamiques pour les dialogues (`message-list.page.ts:157, 167`),
`takeUntilDestroyed` dans le header. Le mode **zoneless est effectif** : `zone.js`
est absent du `package-lock.json` et le mode sans zone est le défaut depuis
Angular 21.

À corriger :

| Id | Constat | Recommandation |
|----|---------|----------------|
| `F7` ✅ | Aucun composant ne déclare `changeDetection: ChangeDetectionStrategy.OnPush`. En zoneless, la détection reste descendante : un cycle parcourt les vues non `OnPush` même si seul un signal isolé a changé. | `ChangeDetectionStrategy.OnPush` sur tous les composants (c'est gratuit ici : l'état est déjà en signaux). |
| `F8` ✅ | 19 `subscribe()` manuels avec gestion à la main des drapeaux de chargement et d'erreur. | Migrer vers `httpResource`/`rxResource` (cf. `F4`) ; ne garder `subscribe` que pour les commandes (POST/PUT/DELETE). |
| `F9` ✅ | `apexcharts` (5.16.0) et `ng-apexcharts` (2.4.0) sont déclarés en dépendances mais **jamais importés** (aucune occurrence dans `src/`). | Les supprimer : temps d'installation, surface de vulnérabilités et bruit d'audit pour zéro usage. |
| `F10` ✅ | `provideAnimations()` (`app.config.ts:14`) charge le moteur d'animations au démarrage alors que toutes les animations du projet sont en CSS pur. | `provideAnimationsAsync()` (chargement différé), voire suppression si Angular Material n'en a plus besoin — à valider avec les dialogues et le `MatSnackBar`. |
| `F11` ✅ | `provideHttpClient(withInterceptors([...]))` sans `withFetch()`. | Ajouter `withFetch()` (API `fetch`, requises pour l'hydratation/SSR et pour l'annulation propre des requêtes). |
| `F12` ✅ | Aucun intercepteur de résilience : pas de délai maximal, pas de rejeu avec temporisation exponentielle sur les erreurs réseau, pas de traitement centralisé des 401/5xx. | Ajouter un intercepteur `retry({ count: 2, delay: backoff })` limité aux `GET` idempotents + `timeout(15000)`. |
| `F13` ✅ | `message-list.page.ts:107` souscrit à `queryParamMap` sans `takeUntilDestroyed` (risque faible, la route complète le flux, mais l'usage n'est pas homogène avec le header). | `toSignal(route.queryParamMap)` ou `takeUntilDestroyed()`. |
| `F14` ✅ | Les lignes du tableau sont cliquables via `(click)` sur `<tr>` (`message-table:49`) : non focalisables, non activables au clavier, sans rôle ARIA. | Rendre la ligne focalisable (`tabindex="0"` + `(keydown.enter)`) ou déplacer l'action sur un contrôle réellement interactif. |
| `F15` ✅ | Budgets de build limités à `initial` et `anyComponentStyle` (`angular.json:38-49`) ; aucun budget sur les *chunks* différés. | Ajouter un budget `allScript`/`bundle` pour détecter les régressions de poids. |
| `F16` ✅ | Les tests Vitest ne sont pas exécutés en CI (seul `npm run build` l'est, cf. `.github/workflows/ci.yml`). | Ajouter `npm run test -- --run` au job frontend ; sinon les tests existants se dégradent sans que personne ne le voie. |
| `F17` ✅ | `@defer` n'est utilisé nulle part. | Différer les blocs lourds et sous la ligne de flottaison du dashboard (`@defer (on viewport)`) pour raccourcir le premier rendu. |

### 2.3 Livraison et conteneurisation ✅

> Traitée. Écarts et compléments par rapport aux recommandations initiales :
> - **`--legacy-peer-deps`** retiré : la justification était doublement périmée —
>   `@angular/animations` n'est plus seulement aligné en 22.x, il a **disparu** du projet
>   (section 2.2, `F10`). `npm ci` strict résout le graphe sans conflit (vérifié par
>   `npm ci --dry-run`), l'image installe donc exactement ce que valide la CI.
> - **Cache Docker** : `COPY . .` remplacé par une copie ciblée
>   (`angular.json`, `tsconfig*.json`, `public/`, `src/`) en plus du `.dockerignore`
>   complété. Le `.dockerignore` seul ne suffisait pas : `nginx.conf` et
>   `proxy.conf.json` doivent rester dans le contexte (le premier est copié à l'étape
>   runtime), mais ne doivent pas invalider l'étape `npm run build`.
> - **CSP et `inlineCritical`** : `optimization.styles.inlineCritical` est désactivé dans
>   `angular.json`. L'optimisation réécrivait la feuille de style en
>   `<link ... media="print" onload="this.media='all'">`, soit un **gestionnaire
>   d'événement en ligne** — il aurait imposé `script-src 'unsafe-inline'`, ce qui vide la
>   CSP de son intérêt. Coût : la feuille de 11 ko redevient bloquante au premier rendu
>   (même origine, précompressée, 2,4 ko transférés) ; gain : `script-src 'self'` strict.
>   `index.html` passe au passage de 24,5 à 15,4 ko (le CSS critique n'y est plus dupliqué).
>   `style-src` conserve `'unsafe-inline'` : Angular injecte les styles de composants en
>   `<style>` au runtime, un nonce imposerait de regénérer `index.html` à chaque requête.
> - **En-têtes dans un fichier séparé** (`security-headers.conf`, `include` dans chaque
>   `location`) : `add_header` n'est pas cumulatif en nginx. Un `location` qui déclare son
>   propre `add_header` — celui du cache long sur les assets — **remplace** tous les
>   en-têtes hérités du bloc `server`. Les poser une fois au niveau `server` les aurait
>   silencieusement supprimés là où ils comptent le plus.
> - **Brotli non retenu** : `nginx:1.27-alpine` n'embarque pas `ngx_brotli`, il faudrait
>   compiler le module ou basculer sur une image tierce. Seul `gzip_static` est activé,
>   alimenté par une précompression au niveau 9 dans l'étape de build (js/css/html/svg/json).
>   Le `gzip` à la volée reste actif pour ce qui n'est pas précompressé, dont les réponses
>   du proxy `/api/`.
> - **Deux paliers de cache** au lieu d'un : le bloc unique existant appliquait
>   `immutable` un an à `.ico`/`.png`/`.svg`, or les fichiers de `public/` (dont
>   `favicon.ico`) **ne sont pas hachés** — leur nom est stable, une mise à jour restait
>   invisible un an. Le cache immuable est désormais réservé aux noms hachés
>   (`-[A-Za-z0-9_-]{8}\.(js|css)`), le reste passe à `max-age=86400`.
> - **`Cache-Control: no-cache` sur `index.html`** via un `location = /index.html` : le
>   `try_files $uri $uri/ /index.html` du repli SPA provoque une redirection interne qui
>   repasse par ce bloc, la règle couvre donc aussi les routes Angular profondes.
> - **Ajout hors périmètre** : `HEALTHCHECK` sur l'image frontend (wget busybox). Sans lui,
>   le `docker compose up -d --wait` du job CI n'attendait rien pour ce service.
> - Vérifications : `npm run build` vert (lot initial 552,90 ko, aucun avertissement de
>   budget), `index.html` produit sans aucun gestionnaire en ligne ni balise `<script>`
>   autre que le module `main-*.js` — la CSP stricte est donc tenable. **La construction de
>   l'image et le relevé effectif des en-têtes n'ont pas pu être exécutés** : le moteur
>   Docker de la machine ne répond pas (WSL en échec), à refaire avant de pousser.

- `frontend/Dockerfile:12-14` : le commentaire justifiant `--legacy-peer-deps` par
  un `@angular/animations` resté en 20.1.8 est **obsolète** — le lock résout
  `@angular/animations@22.0.8`, aligné sur `@angular/core@22.0.8`. Retirer le
  drapeau : il masque aujourd'hui de vrais conflits de dépendances, et l'image
  diverge de ce que valide la CI (`npm ci` strict).
- `COPY . .` avant `npm run build` invalide le cache Docker à chaque modification
  de fichier. Vérifier/compléter `.dockerignore` (`node_modules`, `dist`, `.git`).
- `nginx.conf` : ajouter les en-têtes de sécurité (`Content-Security-Policy`,
  `X-Content-Type-Options: nosniff`, `Referrer-Policy`, `Permissions-Policy`),
  compléter `gzip_types` (`text/javascript`, `application/wasm`), servir des
  fichiers précompressés (`gzip_static`/Brotli) et surtout forcer
  `Cache-Control: no-cache` sur `index.html` — sinon un client peut conserver un
  `index.html` pointant vers des *bundles* hachés supprimés.

---

## 3. Plan d'action priorisé

### P0 — Intégrité du flux (à traiter en premier)

| Id | Action | Effort |
|----|--------|--------|
| `B1` | Ne plus avaler les exceptions du listener ; séparer erreurs définitives et transitoires ; `BOTHRESH`/`BOQNAME` sur la file d'entrée | M |
| `B2` | Corriger la configuration JMS (`session.transacted`, `min/max-concurrency`) — la clé actuelle est morte | S |
| `B4` | Insertion idempotente sur `messageId` | S |
| `A1` ⚠️ | Retirer l'actuator `env` de l'exposition (fait) ; ~~authentifier l'API~~ (hors périmètre) | M |
| `D7` | `@Transactional`, `@Version` sur `updateStatus`, publication DLQ après commit | M |

### P1 — Tenue en charge

| Id | Action | Effort |
|----|--------|--------|
| `D1` | Index `(status, received_at)` et `received_at` | S |
| `D3` / `F1` ✅ | Projection de liste sans payload + endpoints d'agrégation pour le dashboard | M |
| `D4` | Cache court sur `/stats` et réduction des appels déclenchés par le front | S |
| `D5` | Rejeu massif par lots bornés, réponse asynchrone | M |
| `B3` | Dimensionner HikariCP en cohérence avec la concurrence JMS | S |
| `B5` | Batch Hibernate + `reWriteBatchedInserts` | S |
| `F4` ✅ | Annulation des requêtes concurrentes (`httpResource`/`switchMap`) | M |
| `F6` ✅ | Rendu conditionnel tableau/cartes + `payloadSize` mémorisé | S |

### P2 — Qualité, exploitabilité, dette

| Id | Action | Effort |
|----|--------|--------|
| `D6` | Flyway + `ddl-auto: validate` + politique de rétention/partitionnement | L |
| `A5` / `A6` ✅ | Métriques Micrometer, `/actuator/prometheus`, MDC et tracing | M |
| `A2` ✅ | `ProblemDetail` (RFC 9457) et arrêt des fuites de messages d'exception | S |
| `B6` | `Jackson2ObjectMapperBuilderCustomizer`, tolérance aux champs inconnus | S |
| `D2` | Pagination par curseur / `Slice` | M |
| `F7`–`F17` | `OnPush`, `withFetch`, suppression d'ApexCharts, budgets, tests en CI, `@defer` | M |
| — | Testcontainers (PostgreSQL + IBM MQ) sur redélivrance, idempotence, DLQ | L |
| — | Layered jar, utilisateur non-root, sondes `readiness`/`liveness` | S |
| `D8` | Migration `LocalDateTime` → `Instant`/`timestamptz` | M |

---

## 4. Comment valider les gains

Aucune de ces optimisations ne devrait être intégrée sans mesure avant/après.

1. **Ingestion** — injecteur JMS produisant un débit constant sur
   `PAYMENT.REQUEST.QUEUE` ; mesurer les messages/s soutenus, la profondeur de
   file (`CURDEPTH`), la durée de traitement (`Timer` Micrometer) et l'occupation
   du pool Hikari.
2. **API** — `k6`/JMeter sur `GET /messages` (pages profondes incluses) et
   `GET /messages/stats` ; suivre p50/p95/p99 avec 10⁵ puis 10⁶ lignes en base.
3. **SQL** — `EXPLAIN (ANALYZE, BUFFERS)` sur les requêtes de liste avant et après
   création des index ; vérifier la disparition des `Seq Scan` et des tris
   externes.
4. **Front** — Lighthouse et l'onglet Performance sur `/dashboard` et `/messages`
   (page de 100 lignes) ; suivre le poids transféré, le temps de rendu et le
   nombre de cycles de détection de changement.
5. **Résilience** — tests de panne : arrêt de PostgreSQL pendant l'ingestion,
   message toxique injecté, redémarrage du broker. Critère d'acceptation : **zéro
   message perdu**, et tout message non traité retrouvable en DLQ ou en base avec
   un statut d'échec.
