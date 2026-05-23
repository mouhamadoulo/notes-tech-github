# ANNOTATIONS DANS SPRINGBOOT
Spring Boot **vit par ses annotations**, et au début ça peut vite ressembler à de la magie noire.<br>
Il existe des dizaines d'annotations.<br>
On va en parcourir certaines ci-dessous.

## 1. <ins>Annotations de base (démarrage & configuration)</ins>
### @SpringBootApplication
👉 Point d’entrée principal d’une app Spring Boot.
```java 
@SpringBootApplication
public class DemoApplication {
    public static void main(String[] args) {
        SpringApplication.run(DemoApplication.class, args);
    }
}
```
🧠 Elle combine 3 annotations :
```
- @Configuration
- @EnableAutoConfiguration
- @ComponentScan
```

### @ Configuration
👉 Indique que la classe contient des beans Spring.
```java
@Configuration
public class AppConfig {
}
```

### @Bean
👉 Déclare un bean manuellement.
```java
@Configuration
public class AppConfig {

    @Bean
    public RestTemplate restTemplate() {
        return new RestTemplate();
    }
}

```

### @ComponentScan
👉 Dit à Spring où chercher les composants.
```java
@ComponentScan("com.example.demo")
```

## 2. <ins>Annotations de composants (IoC / Injection de dépendances)</ins>
### @ Component
👉 Composant générique Spring.
```java
@Component
public class EmailService {
}
```

### @ Service
👉 Spécialisation de ```@Component``` (logique métier).
```java
@Service
public class UserService {
}
```

### @Repository
👉 Spécialisation pour la couche DAO / accès aux données.
```java
@Repository
public class UserRepository {
}
```

🧠 Avantage : traduit certaines exceptions en ```DataAccessException```.

### @ Controller
👉 Contrôleur MVC (retourne une vue).
```java
@Controller
public class PageController {
}
```

### @ RestController
👉 Contrôleur REST (retourne du JSON).
```java
@RestController
public class UserController {
}
```

🧠 Équivaut à :
```java
@Controller + @ResponseBody
```

## 3. <ins>Injection de dépendances</ins>

### @Autowired
👉 Injecte automatiquement un bean.
```java
@Autowired
private UserService userService;
```

💡 Aujourd’hui, on préfère **l’injection par constructeur :**
```java
@Service
public class UserService {

    private final UserRepository repository;

    public UserService(UserRepository repository) {
        this.repository = repository;
    }
}
```

### @ Qualifier
👉 Choisit quel bean injecter s’il y en a plusieurs.
```java
@Autowired
@Qualifier("emailService")
private NotificationService service;
```

### @ Primary
👉 Bean par défaut.
```java
@Primary
@Service
public class EmailNotificationService implements NotificationService {
}
```

## 4. <ins>Annotations Web / REST</ins>
### @RequestMapping
👉 Mapping générique (GET, POST, etc.).
```java
@RequestMapping("/api/users")
```

### @GetMapping, @PostMapping, etc.
👉 Raccourcis modernes.
```java
@GetMapping("/{id}")
public User getUser(@PathVariable Long id) {
    return userService.findById(id);
}
```
Autres :
- @PostMapping
- @PutMapping
- @DeleteMapping
- @PatchMapping

### @PathVariable
👉 Récupère une variable dans l’URL.
```java
@GetMapping("/users/{id}")
public User get(@PathVariable Long id) { }
```

### @RequestParam
👉 Paramètre de requête.
```java
@GetMapping("/users")
public List<User> find(@RequestParam int page) { }
```

### @RequestBody
👉 Corps JSON → objet Java.
```java
@PostMapping("/users")
public User create(@RequestBody User user) { }
```

### @ResponseBody
👉 Retour direct dans la réponse HTTP.
```java
@ResponseBody
public String hello() {
    return "Hello";
}
```

### @ResponseStatus
👉 Force le code HTTP.
```java
@ResponseStatus(HttpStatus.CREATED)
```

## 5. <ins>Validation (JSR-380 / Bean Validation)</ins>

### @Valid
👉 Active la validation.
```java
@PostMapping
public void create(@Valid @RequestBody User user) { }
```

### Annotations de validation courantes
```java
@NotNull
@NotBlank
@Size(min = 3, max = 20)
@Email
@Min(18)
@Max(99)
@Pattern(regexp = "^[A-Z0-9]+$")
```

Exemple :
```java
public class User {

    @NotBlank
    private String name;

    @Email
    private String email;
}
```

## 6. <ins>Gestion des exceptions</ins>

### @ ExceptionHandler
👉 Gère une exception précise.
```java
@ExceptionHandler(UserNotFoundException.class)
public ResponseEntity<String> handle() {
    return ResponseEntity.notFound().build();
}
```

### @ControllerAdvice
👉 Gestion globale des exceptions.
```java
@ControllerAdvice
public class GlobalExceptionHandler {
}
```

### @RestControllerAdvice
👉 Version REST (JSON).

## 7. <ins>Spring Data JPA</ins>

### @Entity
👉 Entité JPA.
```java
@Entity
public class User {
}
```

### @ Id, @GeneratedValue
```java
@Id
@GeneratedValue(strategy = GenerationType.IDENTITY)
private Long id;
```

### Relations
```java
@OneToMany
@ManyToOne
@OneToOne
@ManyToMany
```

Exemple :
```java
@ManyToOne
@JoinColumn(name = "user_id")
private User user;
```

### @Repository
Avec interface :
```java
public interface UserRepository extends JpaRepository<User, Long> {
}
```

## 8. Configuration & propriétés

### @ Value
👉 Injecte une propriété.
```java
@Value("${app.name}")
private String appName;
```

### @ConfigurationProperties
👉 Mapping propre de config.
```java
@ConfigurationProperties(prefix = "app")
public class AppProperties {
    private String name;
}
```

## 9. Sécurité (Spring Security – bases)

### @EnableWebSecurity
👉 Active la sécurité.

### @PreAuthorize
👉 Sécurité par méthode.
```java
@PreAuthorize("hasRole('ADMIN')")
public void deleteUser() { }
```

### @Secured
```java
@Secured("ROLE_ADMIN")
```

## 10. Tests

### @SpringBootTest
👉 Test d’intégration.
```java
@SpringBootTest
class DemoApplicationTests {
}
```

### @WebMvcTest
👉 Test des contrôleurs uniquement.

### @ MockBean
👉 Mock d’un bean Spring.
```java
@MockBean
private UserService userService;
```

# 🧠 Résumé rapide (mental map)
| Couche     | Annotations                                      |
| ---------- | ------------------------------------------------ |
| Boot       | `@SpringBootApplication`                         |
| Composants | `@Component`, `@Service`, `@Repository`          |
| Web        | `@RestController`, `@GetMapping`, `@RequestBody` |
| DI         | `@Autowired`, `@Qualifier`                       |
| Validation | `@Valid`, `@NotNull`, `@Email`                   |
| JPA        | `@Entity`, `@Id`, `@ManyToOne`                   |
| Config     | `@Value`, `@ConfigurationProperties`             |
| Sécurité   | `@PreAuthorize`                                  |
| Tests      | `@SpringBootTest`, `@MockBean`                   |
