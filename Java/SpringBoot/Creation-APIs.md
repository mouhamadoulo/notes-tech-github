# Tutoriel Complet : Développement d'APIs REST avec Java Spring Boot

> Objectif : apprendre à construire une API professionnelle avec Spring Boot en partant d'un projet simple mais réaliste.
>
> Projet fil rouge : **Gestion de livres (Book Management API)**

---

# Sommaire

1. Introduction aux APIs
2. Types d'APIs
3. Architecture du projet
4. Création du projet Spring Boot
5. Structure recommandée
6. CRUD complet
7. Validation des données
8. Gestion des erreurs
9. DTOs
10. Pagination
11. Filtrage et recherche
12. API Versioning
13. Documentation OpenAPI / Swagger
14. Sécurité JWT
15. Tests
16. Logging
17. Bonnes pratiques
18. Anti-patterns
19. Exemple d'architecture professionnelle
20. Conclusion

---

# 1. Introduction aux APIs

Une API (Application Programming Interface) permet à deux applications de communiquer.

Exemple :

```text
Frontend React
      ↓
API Spring Boot
      ↓
Base de données PostgreSQL
```

Le frontend n'accède jamais directement à la base.

---

# 2. Les différents types d'API

## REST API

Le standard actuel.

```http
GET /books
POST /books
PUT /books/1
DELETE /books/1
```

### Avantages

- Simple
- Standardisé
- Très utilisé

---

## GraphQL

Permet au client de demander exactement les données nécessaires.

Exemple :

```graphql
{
  book(id:1){
    title
    author
  }
}
```

### Avantages

- Pas d'over-fetching
- Flexible

### Inconvénients

- Plus complexe

---

## SOAP

Ancien standard basé sur XML.

Utilisé encore dans :

- banques
- assurances
- administrations

---

## WebSocket

Communication bidirectionnelle temps réel.

Exemples :

- chat
- notifications
- trading

---

# 3. Création du projet Spring Boot

Via Spring Initializr :

Dépendances :

```xml
Spring Web
Spring Data JPA
Validation
PostgreSQL Driver
Spring Security
Lombok
Springdoc OpenAPI
```

---

# 4. Structure recommandée

```text
src/main/java

├── controller
├── service
├── repository
├── entity
├── dto
├── mapper
├── exception
├── security
├── config
└── util
```

Architecture :

```text
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

---

# 5. Entité Book

```java
@Entity
@Table(name = "books")
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
public class Book {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String title;

    private String author;

    private Double price;
}
```

---

# 6. Repository

Le Repository gère l'accès aux données.

```java
@Repository
public interface BookRepository
        extends JpaRepository<Book, Long> {
}
```

Grâce à JpaRepository on obtient déjà :

```java
findAll()
findById()
save()
deleteById()
```

---

# 7. Service Layer

La logique métier doit être ici.

## Mauvaise pratique

```java
@RestController
public class BookController {

    @Autowired
    BookRepository repository;

    @PostMapping
    public Book create(Book book){
        return repository.save(book);
    }
}
```

Le contrôleur contient la logique.

---

## Bonne pratique

```java
@Service
@RequiredArgsConstructor
public class BookService {

    private final BookRepository repository;

    public Book create(Book book){
        return repository.save(book);
    }
}
```

---

# 8. Controller

```java
@RestController
@RequestMapping("/api/books")
@RequiredArgsConstructor
public class BookController {

    private final BookService service;

    @GetMapping
    public List<Book> getAll(){
        return service.findAll();
    }

    @PostMapping
    public Book create(@RequestBody Book book){
        return service.create(book);
    }
}
```

---

# 9. CRUD complet

## CREATE

```http
POST /api/books
```

```json
{
  "title":"Spring Boot",
  "author":"John Doe",
  "price":39.99
}
```

---

## READ

```http
GET /api/books
```

---

## READ BY ID

```http
GET /api/books/1
```

```java
@GetMapping("/{id}")
public Book getById(@PathVariable Long id){
    return service.findById(id);
}
```

---

## UPDATE

```http
PUT /api/books/1
```

```java
@PutMapping("/{id}")
public Book update(
        @PathVariable Long id,
        @RequestBody Book book){

    return service.update(id, book);
}
```

---

## DELETE

```http
DELETE /api/books/1
```

```java
@DeleteMapping("/{id}")
public void delete(@PathVariable Long id){
    service.delete(id);
}
```

---

# 10. Validation des données

Toujours valider les entrées utilisateur.

## DTO

```java
public class CreateBookRequest {

    @NotBlank
    private String title;

    @NotBlank
    private String author;

    @Positive
    private Double price;
}
```

---

## Controller

```java
@PostMapping
public BookResponse create(
        @Valid @RequestBody CreateBookRequest request){

    return service.create(request);
}
```

---

# 11. DTOs : indispensable

Ne jamais exposer directement les entités.

## Mauvais

```java
public Book create(Book book)
```

---

## Bon

```java
public BookResponse create(CreateBookRequest request)
```

---

## Response DTO

```java
@Builder
public record BookResponse(
        Long id,
        String title,
        String author,
        Double price
) {}
```

---

# 12. Mapping DTO ↔ Entity

## Manuel

```java
Book book = Book.builder()
        .title(request.getTitle())
        .author(request.getAuthor())
        .price(request.getPrice())
        .build();
```

---

## Avec MapStruct

```java
@Mapper(componentModel = "spring")
public interface BookMapper {

    Book toEntity(CreateBookRequest request);

    BookResponse toResponse(Book book);
}
```

---

# 13. Gestion des exceptions

## Exception métier

```java
public class BookNotFoundException
        extends RuntimeException {

    public BookNotFoundException(Long id){
        super("Book " + id + " not found");
    }
}
```

---

## Global Exception Handler

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(BookNotFoundException.class)
    public ResponseEntity<String> handle(
            BookNotFoundException ex){

        return ResponseEntity
                .status(HttpStatus.NOT_FOUND)
                .body(ex.getMessage());
    }
}
```

---

# 14. Réponses HTTP correctes

Toujours utiliser les bons codes.

```http
200 OK
201 Created
204 No Content
400 Bad Request
401 Unauthorized
403 Forbidden
404 Not Found
500 Internal Server Error
```

---

## Exemple

```java
@PostMapping
public ResponseEntity<BookResponse> create(
        @RequestBody CreateBookRequest request){

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(service.create(request));
}
```

---

# 15. Pagination

Sans pagination :

```http
GET /books
```

Si 1 million de lignes :

💥 catastrophe

---

## Avec Spring Data

```java
@GetMapping
public Page<BookResponse> getAll(
        Pageable pageable){

    return service.findAll(pageable);
}
```

Appel :

```http
GET /books?page=0&size=20
```

---

# 16. Tri

```http
GET /books?page=0&size=20&sort=title
```

---

## Tri descendant

```http
GET /books?sort=price,desc
```

---

# 17. Recherche

Repository :

```java
public interface BookRepository
        extends JpaRepository<Book, Long> {

    List<Book> findByAuthor(String author);
}
```

---

Controller :

```java
@GetMapping("/author/{author}")
public List<BookResponse> byAuthor(
        @PathVariable String author){

    return service.findByAuthor(author);
}
```

---

# 18. Queries personnalisées

```java
@Query("""
    SELECT b
    FROM Book b
    WHERE b.price > :price
""")
List<Book> findExpensiveBooks(
        @Param("price") Double price);
```

---

# 19. API Versioning

Très important en production.

## Mauvais

```http
/api/books
```

---

## Bon

```http
/api/v1/books
/api/v2/books
```

Controller :

```java
@RequestMapping("/api/v1/books")
```

---

# 20. Swagger / OpenAPI

Documentation automatique.

Dépendance :

```xml
<dependency>
   <groupId>org.springdoc</groupId>
   <artifactId>springdoc-openapi-starter-webmvc-ui</artifactId>
   <version>2.8.9</version>
</dependency>
```

---

URL :

```text
http://localhost:8080/swagger-ui.html
```

---

Documentation :

```java
@Operation(
    summary = "Get all books"
)
@GetMapping
public List<BookResponse> getAll() {
    ...
}
```

---

# 21. Sécurité JWT

Architecture :

```text
Client
 ↓
Login
 ↓
JWT Token
 ↓
API protégée
```

---

## Login

```http
POST /auth/login
```

```json
{
  "username":"admin",
  "password":"password"
}
```

---

Réponse :

```json
{
  "token":"eyJhbGc..."
}
```

---

## Header

```http
Authorization: Bearer eyJhbGc...
```

---

# 22. Security Configuration

```java
@Configuration
@EnableWebSecurity
public class SecurityConfig {

    @Bean
    SecurityFilterChain securityFilterChain(
            HttpSecurity http)
            throws Exception {

        return http
                .csrf(csrf -> csrf.disable())
                .authorizeHttpRequests(auth -> auth
                        .requestMatchers("/auth/**")
                        .permitAll()
                        .anyRequest()
                        .authenticated())
                .build();
    }
}
```

---

# 23. Logging

Ne jamais utiliser :

```java
System.out.println("Book created");
```

---

Utiliser :

```java
@Slf4j
@Service
public class BookService {

    public void create(Book book){

        log.info(
            "Creating book {}",
            book.getTitle()
        );
    }
}
```

---

# 24. Tests Unitaires

## Service

```java
@ExtendWith(MockitoExtension.class)
class BookServiceTest {

    @Mock
    private BookRepository repository;

    @InjectMocks
    private BookService service;

    @Test
    void shouldCreateBook() {

        Book book = new Book();

        when(repository.save(any()))
                .thenReturn(book);

        Book result =
                service.create(book);

        assertNotNull(result);
    }
}
```

---

# 25. Tests d'intégration

```java
@SpringBootTest
@AutoConfigureMockMvc
class BookControllerIT {

    @Autowired
    MockMvc mockMvc;

    @Test
    void shouldReturnBooks()
            throws Exception {

        mockMvc.perform(get("/api/books"))
                .andExpect(status().isOk());
    }
}
```

---

# 26. Validation avancée

Créer son propre validateur.

```java
@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
@Constraint(validatedBy = ISBNValidator.class)
public @interface ValidISBN {
}
```

---

Validator :

```java
public class ISBNValidator
        implements ConstraintValidator<
            ValidISBN,
            String> {

    @Override
    public boolean isValid(
            String value,
            ConstraintValidatorContext context) {

        return value.matches(
                "\\d{13}"
        );
    }
}
```

---

# 27. Transactions

Important pour la cohérence.

```java
@Transactional
public void createOrder() {

    saveOrder();

    savePayment();

    saveInvoice();
}
```

Si une étape échoue :

```text
Rollback automatique
```

---

# 28. Gestion de configuration

```yaml
spring:
  datasource:
    url: jdbc:postgresql://localhost:5432/booksdb
    username: admin
    password: secret
```

---

Variables d'environnement :

```yaml
spring:
  datasource:
    username: ${DB_USER}
    password: ${DB_PASSWORD}
```

---

# 29. Bonnes pratiques

## Injection par constructeur

Toujours :

```java
@RequiredArgsConstructor
public class BookService {
    private final BookRepository repository;
}
```

Jamais :

```java
@Autowired
private BookRepository repository;
```

---

## DTO partout

Toujours :

```java
RequestDTO
ResponseDTO
```

Jamais :

```java
Entity exposée directement
```

---

## Une responsabilité par classe

Controller :

```text
HTTP
```

Service :

```text
Métier
```

Repository :

```text
Base de données
```

---

## Pagination obligatoire

Toujours :

```http
GET /books?page=0&size=20
```

---

## Logging structuré

Toujours :

```java
log.info("Book {} created", id);
```

Jamais :

```java
log.info("Book created");
```

---

# 30. Choses à éviter absolument

## Logique métier dans Controller

❌

```java
@PostMapping
public Book create(Book book){

   if(book.getPrice() > 100){
      ...
   }
}
```

---

## Catch Exception partout

❌

```java
try {
 ...
}
catch(Exception e){
}
```

---

## Retourner des String

❌

```java
return "success";
```

---

Préférer :

```java
ResponseEntity<BookResponse>
```

---

## Endpoints incohérents

❌

```http
/getBooks
/createBook
/deleteBook
```

---

REST :

✅

```http
GET /books
POST /books
DELETE /books/1
```

---

## Ignorer la validation

❌

```java
String title;
```

---

✅

```java
@NotBlank
String title;
```

---

# 31. Architecture Professionnelle Finale

```text
Controller
    ↓
DTO Request
    ↓
Validation
    ↓
Service
    ↓
Mapper
    ↓
Repository
    ↓
JPA
    ↓
PostgreSQL

Retour :

Entity
 ↓
Mapper
 ↓
DTO Response
 ↓
Controller
 ↓
JSON
```

---

# 32. Exemple de JSON de réponse standardisé

Très utilisé en entreprise.

```json
{
  "timestamp": "2026-01-01T12:00:00",
  "status": 200,
  "message": "Success",
  "data": {
    "id": 1,
    "title": "Spring Boot"
  }
}
```

Wrapper :

```java
public record ApiResponse<T>(
        LocalDateTime timestamp,
        int status,
        String message,
        T data
) {}
```

---

# 33. Ce qu'on retrouve dans une vraie API d'entreprise

- Spring Boot
- PostgreSQL
- Flyway
- JWT
- Swagger/OpenAPI
- DTO
- MapStruct
- Validation
- Global Exception Handler
- Pagination
- Logging
- Tests unitaires
- Tests d'intégration
- Docker
- CI/CD
- Monitoring
- Observabilité

---

# Conclusion

Pour développer une API Spring Boot robuste :

1. Utiliser une architecture en couches.
2. Toujours passer par un Service.
3. Utiliser DTO + Validation.
4. Centraliser les exceptions.
5. Paginer les résultats.
6. Documenter avec OpenAPI.
7. Sécuriser avec JWT.
8. Écrire des tests.
9. Logger correctement.
10. Respecter les conventions REST.

La combinaison la plus courante en entreprise aujourd'hui est :

```text
Java 21
Spring Boot 3
Spring Security
JWT
PostgreSQL
JPA/Hibernate
MapStruct
Swagger/OpenAPI
Docker
JUnit + Mockito
Flyway
```

En maîtrisant ce projet "Book Management API", vous possédez déjà environ 80% des concepts utilisés dans les APIs Spring Boot professionnelles.
