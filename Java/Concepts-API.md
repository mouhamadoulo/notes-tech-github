Dans un contexte d'application Java Spring Boot + Angular, il y a plusieurs concepts qu'il est vraiment important de maîtriser. On peut les regrouper en plusieurs catégories.

# 1. Architecture d'une API REST

Comprendre comment le frontend Angular et le backend Spring Boot communiquent :

```text
Angular (Client)
       ↓ HTTP
API REST Spring Boot
       ↓
Services Métier
       ↓
Base de données
```

Concepts à maîtriser :

* Ressource (`User`, `Product`, `Order`, etc.)
* Endpoints (`/users`, `/orders`)
* Représentation JSON
* Stateless (chaque requête est indépendante)
* Séparation frontend/backend

Exemple :

```http
GET /api/users/1
```

Réponse :

```json
{
  "id": 1,
  "name": "Jean",
  "email": "jean@mail.com"
}
```

---

# 2. Les verbes HTTP

C'est absolument fondamental.

| Verbe  | Action                 | Exemple         |
| ------ | ---------------------- | --------------- |
| GET    | Lire                   | GET /users      |
| POST   | Créer                  | POST /users     |
| PUT    | Modifier complètement  | PUT /users/1    |
| PATCH  | Modifier partiellement | PATCH /users/1  |
| DELETE | Supprimer              | DELETE /users/1 |

Exemple Spring :

```java
@GetMapping("/{id}")
public UserDto getUser(@PathVariable Long id) {
    return service.findById(id);
}
```

---

# 3. Codes de réponse HTTP

Une bonne API communique correctement grâce aux statuts HTTP.

| Code | Signification         |
| ---- | --------------------- |
| 200  | OK                    |
| 201  | Created               |
| 204  | No Content            |
| 400  | Bad Request           |
| 401  | Unauthorized          |
| 403  | Forbidden             |
| 404  | Not Found             |
| 409  | Conflict              |
| 500  | Internal Server Error |

Exemple :

```java
return ResponseEntity
        .status(HttpStatus.CREATED)
        .body(userDto);
```

---

# 4. DTO (Data Transfer Objects)

À maîtriser absolument.

Ne jamais exposer directement les entités JPA.

Mauvais :

```java
return userEntity;
```

Bon :

```java
return userDto;
```

Exemple :

```java
public class UserDto {
    private Long id;
    private String name;
    private String email;
}
```

Avantages :

* sécurité ;
* découplage ;
* contrôle des données exposées ;
* meilleure évolution de l'API.

---

# 5. Sérialisation JSON

Spring transforme automatiquement les objets Java en JSON grâce à Jackson.

Java :

```java
UserDto
```

↓

```json
{
  "id": 1,
  "name": "Jean"
}
```

Annotations utiles :

```java
@JsonIgnore
@JsonProperty
@JsonFormat
@JsonInclude
```

---

# 6. Validation des données

Très important pour sécuriser l'API.

Exemple :

```java
public class CreateUserRequest {

    @NotBlank
    private String name;

    @Email
    private String email;

    @Min(18)
    private Integer age;
}
```

Controller :

```java
@PostMapping
public UserDto create(@Valid @RequestBody CreateUserRequest request) {
    ...
}
```

---

# 7. Gestion centralisée des erreurs

À maîtriser rapidement.

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(UserNotFoundException.class)
    public ResponseEntity<ApiError> handle(...) {
        ...
    }
}
```

L'objectif est d'avoir des réponses homogènes :

```json
{
  "timestamp": "2026-07-08T10:00:00",
  "status": 404,
  "message": "User not found"
}
```

---

# 8. Couche Service et séparation des responsabilités

Architecture classique :

```text
Controller
     ↓
Service
     ↓
Repository
     ↓
Database
```

Responsabilités :

**Controller**

* reçoit les requêtes HTTP ;
* valide les entrées ;
* retourne les réponses.

**Service**

* contient le métier ;
* gère les transactions.

**Repository**

* accès aux données.

Cette séparation est essentielle pour maintenir une application de taille importante.

---

# 9. JPA et Hibernate

Concepts indispensables :

* Entités (`@Entity`)
* Relations (`@OneToMany`, `@ManyToOne`, `@ManyToMany`)
* Chargement lazy/eager
* Transactions (`@Transactional`)
* Requêtes JPQL
* Pagination

Exemple :

```java
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    @OneToMany(mappedBy = "user")
    private List<Order> orders;
}
```

---

# 10. Pagination, tri et filtrage

Une API ne doit jamais renvoyer des milliers d'enregistrements.

Exemple :

```http
GET /users?page=0&size=20&sort=name,asc
```

Spring :

```java
Page<UserDto> findAll(Pageable pageable)
```

---

# 11. Sécurité : Spring Security + JWT

C'est probablement le sujet le plus important après REST.

Concepts :

* Authentification
* Autorisation
* JWT
* Refresh Token
* Roles et Permissions
* Filtres Spring Security
* CORS

Flux typique :

```text
Angular
   ↓ login
Spring Security
   ↓
JWT
   ↓
Angular stocke le token
   ↓
Authorization: Bearer xxx
```

---

# 12. CORS

Indispensable avec Angular.

Sans configuration :

```text
localhost:4200
```

ne pourra pas appeler :

```text
localhost:8080
```

Exemple :

```java
@CrossOrigin(origins = "http://localhost:4200")
```

ou via :

```java
CorsConfigurationSource
```

---

# 13. Consommation de l'API côté Angular

Il faut bien comprendre :

```typescript
HttpClient
Observable
RxJS
Interceptors
Error Handling
```

Exemple :

```typescript
this.http.get<User[]>('/api/users');
```

Avec JWT :

```typescript
intercept(req, next) {
  const cloned = req.clone({
    setHeaders: {
      Authorization: `Bearer ${token}`
    }
  });

  return next.handle(cloned);
}
```

---

# 14. Documentation d'API

Très utile en entreprise :

* Swagger/OpenAPI
* Génération automatique de documentation
* Tests interactifs

Exemple :

```java
@Operation(summary = "Créer un utilisateur")
```

---

# 15. Versionnement des API

Exemples :

```http
/api/v1/users
/api/v2/users
```

ou

```http
Accept: application/vnd.company.v2+json
```

---

# 16. Tests

À maîtriser :

### Tests unitaires

```java
@ExtendWith(MockitoExtension.class)
class UserServiceTest
```

### Tests Web

```java
@WebMvcTest
```

### Tests d'intégration

```java
@SpringBootTest
```

---

# 17. Concepts avancés souvent demandés en entretien

* Idempotence des opérations HTTP
* Transactions et propagation (`REQUIRED`, `REQUIRES_NEW`)
* Optimistic Locking (`@Version`)
* Cache
* Asynchronisme (`@Async`)
* API Gateway
* Rate Limiting
* Gestion des performances
* Monitoring et logs
* Principes SOLID
* Injection de dépendances
* Architecture hexagonale
* Événements et messaging

---

# Ordre d'apprentissage recommandé

1. HTTP et REST
2. Controllers Spring Boot
3. DTO et JSON
4. Validation
5. Service / Repository
6. JPA et Hibernate
7. Gestion des erreurs
8. Pagination et filtrage
9. Angular `HttpClient` et RxJS
10. CORS
11. Spring Security + JWT
12. Tests
13. Swagger/OpenAPI
14. Transactions et performances
15. Architecture avancée

Si vous maîtrisez réellement ces quinze points, vous êtes déjà capable de développer des API professionnelles Spring Boot consommées par Angular dans la plupart des projets d'entreprise.
