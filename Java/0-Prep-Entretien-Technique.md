Pour un entretien technique de développeur Java / Spring Boot senior avec 8 ans d’expérience, je te conseille de réviser en priorité les fondamentaux Java, la concurrence, Spring Boot, les API REST, les bases de données et les tests. L’objectif n’est pas seulement de connaître les définitions : tu dois pouvoir expliquer les concepts, justifier tes choix et donner des exemples concrets.

Voici une checklist pour réviser efficacement avant ton entretien.

# 1. Java — fondamentaux indispensables

## Checklist de révision Java

0/25

### POO et conception

Les 4 piliers de la POO : encapsulation, héritage, abstraction, polymorphisme

Interface vs classe abstraite

Surcharge (overloading) vs redéfinition (overriding)

Composition vs héritage

SOLID : les 5 principes

Immutabilité, records, classes finales

### Objets et langage

equals() vs == vs hashCode()

String, StringBuilder, StringBuffer

Passage des arguments : passage par valeur en Java

Types primitifs vs wrappers, autoboxing

Optional : bonnes pratiques et pièges

Gestion des exceptions : checked vs unchecked, finally, try-with-resources

### Collections et génériques

List vs Set vs Map

ArrayList vs LinkedList

HashMap : fonctionnement interne, collisions, hashCode()

HashMap vs ConcurrentHashMap

Comparable vs Comparator

Streams : map, filter, flatMap, reduce, collect

Generics, wildcards, PECS (? extends / ? super)

### Java moderne

Lambda expressions et interfaces fonctionnelles

Stream API et collectors

Records, sealed classes, pattern matching

Date/Time API : LocalDate, Instant, ZonedDateTime

Switch expressions

Virtual threads (Java 21), Structured Concurrency et limites selon la version Java

Réinitialiser la checklist

# 2. JVM et concurrence

Ce sont des sujets qui font souvent la différence entre un profil intermédiaire et un profil senior.

- JVM : heap, stack, Metaspace, class loading.
- Garbage Collector : fonctionnement, générations, pauses et fuites mémoire.
- Threads : `Thread`, `Runnable`, `Callable`, `Future`.
- Synchronisation : `synchronized`, `volatile`, `Lock`, atomic classes.
- ExecutorService : pools de threads, files d'attente, arrêt propre.
- CompletableFuture : composition de traitements asynchrones, gestion des erreurs.
- Deadlock, race condition, starvation : reconnaître et prévenir ces problèmes.
- Virtual threads Java 21 : intérêt, différences avec les threads classiques, limites avec les traitements CPU-bound.
- Java Memory Model : visibilité, atomicité et relation happens-before.

Questions types :

- Pourquoi `volatile` ne rend-il pas `i++` thread-safe ?
- Comment éviter un deadlock ?
- Quelle différence entre parallélisme et asynchronisme ?
- Pourquoi ne pas créer un thread par requête avec les threads classiques ?

# 3. Spring Boot et Spring Framework

Conteneur IoC et injection de dépendances

IoC, DI, injection par constructeur, beans et scopes.

Cycle de vie des beans

Constructeur, injection, BeanPostProcessor, @PostConstruct, @PreDestroy.

Configuration

application.yml, profils, @ConfigurationProperties, variables d’environnement.

Spring MVC et REST

@RestController, @RequestMapping, DTO, validation, gestion globale des erreurs.

Transactions

@Transactional, propagation, isolation, rollback, self-invocation et proxies.

Sécurité

Spring Security, chaîne de filtres, OAuth2, JWT, rôles et autorisations.

Persistance

Spring Data JPA, Hibernate, entités, lazy/eager loading, N+1, pagination.

Démarrage et observabilité

Auto-configuration, starters, Actuator, logs, métriques et health checks.

Questions incontournables :

1. Comment fonctionne l'injection de dépendances dans Spring ?
2. Quelle différence entre `@Component`, `@Service` et `@Repository` ?
3. Pourquoi `@Transactional` peut-il ne pas fonctionner ?
4. Quelle différence entre les scopes `singleton` et `prototype` ?
5. Comment fonctionne l'auto-configuration de Spring Boot ?
6. Comment sécuriser une API REST avec Spring Security et JWT ?

# 4. API REST, architecture et microservices

- Verbes HTTP : GET, POST, PUT, PATCH, DELETE.
- Codes HTTP : 200, 201, 204, 400, 401, 403, 404, 409, 500.
- Idempotence : méthodes HTTP et opérations métier.
- Pagination, tri, filtrage et validation des entrées.
- DTO vs entités JPA.
- Architecture en couches : controller, service, repository.
- Principes SOLID et séparation des responsabilités.
- Microservices vs monolithe modulaire.
- Communication synchrone REST vs asynchrone via messages.
- Résilience : timeout, retry, circuit breaker, bulkhead.
- Idempotence des consommateurs et prévention des doublons.
- Patterns : outbox, saga, CQRS, selon les besoins.

Exercice senior : comment concevoir une API qui traite des milliers de paiements tout en évitant les doublons et en garantissant la traçabilité ?

# 5. SQL, bases de données et JPA

- Jointures SQL : INNER, LEFT, RIGHT.
- Index : avantages, coût sur les écritures, choix des colonnes.
- Transactions ACID.
- Isolation : READ COMMITTED, REPEATABLE READ, SERIALIZABLE.
- Verrouillage optimiste (`@Version`) vs pessimiste.
- Relations JPA : `@OneToMany`, `@ManyToOne`, `@ManyToMany`.
- Chargement lazy vs eager.
- Problème N+1 et solutions : fetch join, entity graph, projections.
- Pagination et performances.
- Migrations de schéma avec Liquibase ou Flyway.
- Pool de connexions et transactions longues.

Question classique : pourquoi une requête SQL performante peut-elle devenir lente dans une application Spring Boot ?

# 6. Tests et qualité du code

- JUnit 5 : assertions, lifecycle, tests paramétrés.
- Mockito : `@Mock`, `@InjectMocks`, stubs et vérifications.
- Tests unitaires vs tests d'intégration.
- `@WebMvcTest`, `@DataJpaTest`, `@SpringBootTest`.
- Testcontainers pour les dépendances réelles, comme PostgreSQL.
- Tests des erreurs, des limites et des cas concurrents.
- Pyramide de tests et couverture pertinente.
- Qualité du code, SonarQube, revues de code.

# 7. DevOps et environnement de production

Pour un poste Java fullstack orienté cloud, révise également :

- Maven : phases, dépendances, scopes et gestion des versions.
- Git : branches, rebase, merge, cherry-pick.
- Docker : images, conteneurs, volumes et réseaux.
- CI/CD : build, tests, analyse de qualité, déploiement.
- Kubernetes ou OpenShift : pods, services, probes, ConfigMaps et Secrets.
- Logs structurés, correlation ID et traçabilité distribuée.
- Monitoring et métriques via Actuator et Micrometer.
- Gestion des secrets, des certificats et des configurations par environnement.

# 8. Plan de révision express avant l'entretien

## 30 min

Java Core

Collections, HashMap, equals/hashCode, Streams, exceptions et immutabilité.

## 30 min

Spring Boot

Injection, cycle de vie, transactions, sécurité et gestion des erreurs REST.

## 20 min

Concurrence et JVM

Threads, virtual threads, GC, synchronisation et CompletableFuture.

## 20 min

SQL, tests et architecture

N+1, transactions, tests d'intégration, idempotence et choix architecturaux.

## Mon conseil

Si tu n'as qu'une heure, concentre-toi sur HashMap, equals/hashCode, Streams, exceptions, immutabilité, threads et virtual threads, cycle de vie Spring, `@Transactional`, JPA N+1 et sécurité REST.

Et surtout, pour chaque notion, prépare une réponse structurée en trois points : définition, exemple concret, piège ou limite. C'est ce qui permet de démontrer un vrai niveau senior.
