# Guide Ready-To-Go Entretien Full Stack Java / Spring Boot / Angular en Banque

# 1. Présentation de mon profil

## Exemple de présentation (2 minutes)

Je suis développeur Full Stack spécialisé en Java Spring Boot et Angular.

J'interviens sur la conception, le développement et la maintenance d'applications métier. J'ai travaillé sur des architectures REST, des bases de données relationnelles, des interfaces Angular et des pipelines CI/CD.

Mes points forts sont :

- Développement backend Java/Spring Boot
- Conception et exposition d'API REST
- Développement frontend Angular
- Qualité logicielle et tests
- Analyse des besoins métier
- Travail en méthodologie Agile

Aujourd'hui, je souhaite évoluer sur des projets bancaires où les exigences de sécurité, de performance et de fiabilité sont particulièrement élevées.

---

# 2. Java - Questions incontournables

## Différence entre HashMap et ConcurrentHashMap

### HashMap

- Non thread-safe
- Utilisable dans un environnement mono-thread

### ConcurrentHashMap

- Thread-safe
- Conçu pour les environnements multi-thread
- Meilleures performances que Hashtable

---

## Différence entre ArrayList et LinkedList

### ArrayList

- Basé sur un tableau dynamique
- Accès rapide via index O(1)
- Insertion/suppression coûteuse au milieu

### LinkedList

- Liste chaînée
- Insertion/suppression rapide
- Accès plus lent O(n)

---

## Optional

### Pourquoi utiliser Optional ?

Éviter les NullPointerException.

Exemple :

```java
Optional<User> user = repository.findById(id);

user.ifPresent(System.out::println);
```

---

## equals() vs ==

### ==

Compare les références mémoire.

### equals()

Compare le contenu des objets.

---

## String vs StringBuilder

### String

Immutable.

### StringBuilder

Mutable.

Plus performant pour les concaténations répétées.

---

# 3. Programmation Orientée Objet

## Les 4 piliers

### Encapsulation

Masquer les détails internes.

### Héritage

Réutiliser le comportement d'une classe.

### Polymorphisme

Utiliser plusieurs implémentations via une même interface.

### Abstraction

Masquer la complexité inutile.

---

## Exemple de polymorphisme

```java
interface Payment {
    void pay();
}

class CardPayment implements Payment {
    public void pay() {}
}

class TransferPayment implements Payment {
    public void pay() {}
}
```

---

# 4. Spring Framework

## Qu'est-ce que l'Inversion de Contrôle (IoC) ?

Spring gère le cycle de vie des objets.

Les dépendances sont injectées automatiquement.

---

## Injection de dépendances

### Constructor Injection (recommandée)

```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

---

## Différence entre @Component, @Service et @Repository

### @Component

Bean générique.

### @Service

Couche métier.

### @Repository

Couche accès données.

Gestion automatique des exceptions SQL.

---

## Bean Scope

### Singleton

Une seule instance.

### Prototype

Nouvelle instance à chaque injection.

---

# 5. Spring Boot

## Pourquoi Spring Boot ?

- Auto-configuration
- Démarrage rapide
- Serveur embarqué
- Réduction de la configuration

---

## Starter Spring Boot

Exemple :

```xml
spring-boot-starter-web
spring-boot-starter-data-jpa
spring-boot-starter-security
```

---

## Cycle d'une requête REST

Client
↓
Controller
↓
Service
↓
Repository
↓
Base de données

---

# 6. API REST

## Verbes HTTP

### GET

Lecture.

### POST

Création.

### PUT

Mise à jour complète.

### PATCH

Mise à jour partielle.

### DELETE

Suppression.

---

## Codes HTTP à connaître

| Code | Signification |
|--------|-------------|
| 200 | OK |
| 201 | Created |
| 204 | No Content |
| 400 | Bad Request |
| 401 | Unauthorized |
| 403 | Forbidden |
| 404 | Not Found |
| 500 | Internal Server Error |

---

## Idempotence

Une opération est idempotente lorsqu'elle produit toujours le même résultat.

Exemples :

- GET ✓
- PUT ✓
- DELETE ✓
- POST ✗

---

# 7. JPA / Hibernate

## Différence entre save() et saveAndFlush()

### save()

Persistance différée.

### saveAndFlush()

Écriture immédiate en base.

---

## FetchType

### EAGER

Chargement immédiat.

### LAZY

Chargement à la demande.

Préféré dans la plupart des cas.

---

## N+1 Problem

Exemple :

1 requête pour les clients

Puis :

N requêtes pour leurs commandes

Solution :

```java
JOIN FETCH
@EntityGraph
```

---

# 8. SQL

## Ordre d'exécution SQL

```sql
FROM
JOIN
WHERE
GROUP BY
HAVING
SELECT
ORDER BY
LIMIT
```

---

## INNER JOIN

Retourne uniquement les correspondances.

---

## LEFT JOIN

Retourne toutes les lignes de gauche.

---

## Index

Permettent d'accélérer les recherches.

Attention :

- Plus rapide en lecture
- Plus coûteux en écriture

---

# 9. Spring Security

## JWT

JSON Web Token.

Structure :

```text
Header.Payload.Signature
```

---

## OAuth2

Protocole d'autorisation.

Permet à une application d'accéder à une ressource sans partager le mot de passe.

---

## Différence 401 / 403

### 401

Non authentifié.

### 403

Authentifié mais non autorisé.

---

# 10. Microservices

## Pourquoi les microservices ?

- Scalabilité
- Déploiement indépendant
- Résilience

---

## Inconvénients

- Complexité
- Réseau
- Monitoring

---

## Communication

### Synchrone

REST

### Asynchrone

Kafka
RabbitMQ

---

## Circuit Breaker

Évite qu'un service défaillant fasse tomber toute l'application.

---

# 11. Kafka

## Pourquoi Kafka ?

Messagerie distribuée haute performance.

---

## Concepts clés

### Producer

Produit des messages.

### Consumer

Consomme les messages.

### Topic

Canal de communication.

### Partition

Permet la parallélisation.

### Offset

Position du message.

---

## Avantages

- Découplage
- Scalabilité
- Résilience

---

# 12. Docker

## Qu'est-ce qu'un conteneur ?

Application + dépendances.

Même comportement partout.

---

## Commandes importantes

```bash
docker build .
docker run
docker ps
docker logs
docker exec
```

---

## Dockerfile

Permet de construire une image.

---

# 13. Kubernetes

## Concepts essentiels

### Pod

Plus petite unité déployable.

### Deployment

Gestion des versions.

### Service

Exposition réseau.

### ConfigMap

Configuration.

### Secret

Informations sensibles.

---

# 14. Angular

## Cycle de vie

```typescript
ngOnInit()
ngAfterViewInit()
ngOnDestroy()
```

---

## RxJS

Programmation réactive.

Exemple :

```typescript
this.http.get<User[]>('/users')
.subscribe(users => {});
```

---

## Observable vs Promise

### Promise

Une seule valeur.

### Observable

Flux de valeurs.

Annulable.

---

## Interceptor

Permet :

- Ajouter JWT
- Logger
- Gérer les erreurs

---

# 15. Tests

## Tests unitaires

Validation d'une unité isolée.

---

## Mockito

Permet de simuler des dépendances.

Exemple :

```java
@Mock
private UserRepository repository;
```

---

## Tests d'intégration

Validation de plusieurs couches ensemble.

---

# 16. CI/CD

## Pipeline classique

```text
Commit
↓
Build
↓
Tests
↓
Analyse Sonar
↓
Packaging
↓
Déploiement
```

---

# 17. Questions Banque

## Qu'est-ce que le KYC ?

Know Your Customer.

Identification des clients.

---

## AML

Anti-Money Laundering.

Lutte contre le blanchiment.

---

## Pourquoi la sécurité est-elle critique en banque ?

- Données sensibles
- Transactions financières
- Exigences réglementaires

---

## Pourquoi Kafka est-il utilisé en banque ?

- Transactions temps réel
- Traçabilité
- Architecture événementielle

---

# 18. Questions RH Classiques

## Pourquoi souhaitez-vous rejoindre le secteur bancaire ?

Le secteur bancaire offre des projets critiques avec de fortes exigences de sécurité, de robustesse et de performance. C'est un environnement techniquement stimulant qui permet de développer une expertise à forte valeur ajoutée.

---

## Quelle est votre plus grande qualité ?

Capacité d'analyse et résolution de problèmes complexes.

---

## Quel est votre principal axe d'amélioration ?

Je cherche constamment à approfondir mes connaissances en architecture distribuée et en sécurité afin de concevoir des systèmes toujours plus robustes.

---

# 19. Questions à poser à l'intervieweur

- Quelle est l'architecture actuelle ?
- Monolithe ou microservices ?
- Utilisez-vous Kafka ?
- Quelle est la stack cloud ?
- Comment est organisée l'équipe ?
- Quel est le niveau d'autonomie attendu ?
- Quels sont les principaux défis techniques du projet ?
- Quelle est la roadmap des 12 prochains mois ?

---

# 20. Concepts à maîtriser absolument avant un entretien bancaire

✓ Java Core

✓ Collections

✓ Stream API

✓ Optional

✓ Concurrence

✓ Spring Core

✓ Spring Boot

✓ REST

✓ JPA/Hibernate

✓ SQL avancé

✓ JWT

✓ OAuth2

✓ Spring Security

✓ Docker

✓ Kubernetes

✓ Kafka

✓ CI/CD

✓ Angular

✓ RxJS

✓ Tests

✓ Microservices

✓ KYC

✓ AML

✓ Architecture événementielle

✓ Monitoring

✓ Logs

✓ Résilience

✓ Design Patterns (Singleton, Factory, Strategy)

✓ SOLID

✓ Clean Code

✓ Clean Architecture
