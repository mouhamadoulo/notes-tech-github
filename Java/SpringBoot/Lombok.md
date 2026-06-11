# Tutoriel complet Lombok avec Spring Boot

## Introduction

### Qu'est-ce que Lombok ?

**Lombok** est une bibliothèque Java qui permet de réduire considérablement le code répétitif (*boilerplate code*) en générant automatiquement du code lors de la compilation.

Par exemple, au lieu d'écrire :

- getters
- setters
- constructeurs
- méthodes `toString()`
- méthodes `equals()` et `hashCode()`
- builders

Lombok peut les générer automatiquement à partir d'annotations.

### Avantages

✅ Réduction du code répétitif  
✅ Classes plus lisibles  
✅ Maintenance facilitée  
✅ Standardisation du code  
✅ Compatible avec Spring Boot

### Inconvénients

❌ Le code généré n'est pas visible directement dans les fichiers Java  
❌ Peut perturber certains outils mal configurés  
❌ Peut masquer certains comportements pour les développeurs débutants

---

# Installation de Lombok dans un projet Spring Boot

## Maven

Ajouter la dépendance dans `pom.xml` :

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <optional>true</optional>
</dependency>
```

Version explicite :

```xml
<dependency>
    <groupId>org.projectlombok</groupId>
    <artifactId>lombok</artifactId>
    <version>1.18.38</version>
    <scope>provided</scope>
</dependency>
```

---

## Gradle

```gradle
dependencies {
    compileOnly 'org.projectlombok:lombok:1.18.38'
    annotationProcessor 'org.projectlombok:lombok:1.18.38'

    testCompileOnly 'org.projectlombok:lombok:1.18.38'
    testAnnotationProcessor 'org.projectlombok:lombok:1.18.38'
}
```

---

# Configuration de l'IDE

## IntelliJ IDEA

### Étape 1

Installer le plugin Lombok :

```text
File
 └─ Settings
     └─ Plugins
         └─ Lombok
```

Installer puis redémarrer IntelliJ.

### Étape 2

Activer l'annotation processing :

```text
Settings
 └─ Build, Execution, Deployment
     └─ Compiler
         └─ Annotation Processors
             └─ Enable annotation processing
```

---

## Eclipse

Télécharger le jar Lombok :

```bash
java -jar lombok.jar
```

Puis suivre l'assistant d'installation.

Redémarrer Eclipse.

---

# Exemple sans Lombok

```java
public class User {

    private Long id;
    private String name;

    public User() {
    }

    public User(Long id, String name) {
        this.id = id;
        this.name = name;
    }

    public Long getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    @Override
    public String toString() {
        return "User{id=" + id + ", name='" + name + "'}";
    }
}
```

---

# Exemple avec Lombok

```java
import lombok.Data;

@Data
public class User {

    private Long id;
    private String name;
}
```

Même résultat avec seulement quelques lignes.

---

# Les annotations Lombok les plus importantes

---

# @Getter

Génère automatiquement les getters.

```java
import lombok.Getter;

@Getter
public class User {

    private String username;
}
```

Code généré :

```java
public String getUsername() {
    return username;
}
```

---

# @Setter

Génère automatiquement les setters.

```java
import lombok.Setter;

@Setter
public class User {

    private String username;
}
```

Code généré :

```java
public void setUsername(String username) {
    this.username = username;
}
```

---

# @Getter et @Setter sur un attribut

```java
public class User {

    @Getter
    @Setter
    private String username;
}
```

---

# @ToString

Génère la méthode `toString()`.

```java
import lombok.ToString;

@ToString
public class User {

    private Long id;
    private String username;
}
```

Utilisation :

```java
System.out.println(user);
```

Résultat :

```text
User(id=1, username=john)
```

---

# @EqualsAndHashCode

Génère automatiquement :

```java
equals()
hashCode()
```

```java
import lombok.EqualsAndHashCode;

@EqualsAndHashCode
public class User {

    private Long id;
    private String email;
}
```

---

# @NoArgsConstructor

Génère un constructeur vide.

```java
import lombok.NoArgsConstructor;

@NoArgsConstructor
public class User {

    private String name;
}
```

Génère :

```java
public User() {
}
```

---

# @AllArgsConstructor

Génère un constructeur avec tous les champs.

```java
import lombok.AllArgsConstructor;

@AllArgsConstructor
public class User {

    private Long id;
    private String name;
}
```

Génère :

```java
public User(Long id, String name) {
    this.id = id;
    this.name = name;
}
```

---

# @RequiredArgsConstructor

Construit uniquement les champs :

- `final`
- `@NonNull`

```java
import lombok.RequiredArgsConstructor;

@RequiredArgsConstructor
public class User {

    private final String username;

    private final String email;

    private Integer age;
}
```

Génère :

```java
public User(String username, String email) {
    this.username = username;
    this.email = email;
}
```

---

# @Data

Annotation la plus connue.

Équivaut à :

```java
@Getter
@Setter
@ToString
@EqualsAndHashCode
@RequiredArgsConstructor
```

Exemple :

```java
import lombok.Data;

@Data
public class User {

    private Long id;
    private String name;
}
```

---

# @Value

Version immutable de `@Data`.

Tous les champs deviennent :

```java
private final
```

Exemple :

```java
import lombok.Value;

@Value
public class UserDto {

    Long id;
    String username;
}
```

Objet immutable :

```java
UserDto dto = new UserDto(1L, "john");
```

Aucun setter généré.

---

# @Builder

Permet la création fluide d'objets.

## Sans Builder

```java
User user = new User(
    1L,
    "John",
    "john@mail.com"
);
```

---

## Avec Builder

```java
import lombok.Builder;
import lombok.Data;

@Data
@Builder
public class User {

    private Long id;
    private String name;
    private String email;
}
```

Utilisation :

```java
User user = User.builder()
        .id(1L)
        .name("John")
        .email("john@mail.com")
        .build();
```

Très utile pour :

- DTO
- Entités
- Objets complexes

---

# @Singular

Utilisé avec Builder pour les collections.

```java
@Builder
public class Order {

    @Singular
    private List<String> products;
}
```

Utilisation :

```java
Order order = Order.builder()
        .product("Laptop")
        .product("Mouse")
        .build();
```

---

# @Builder.Default

Valeur par défaut dans un Builder.

```java
@Builder
public class User {

    private String name;

    @Builder.Default
    private boolean active = true;
}
```

---

# @NonNull

Ajoute une vérification de null.

```java
import lombok.NonNull;

public class UserService {

    public void createUser(@NonNull String username) {

    }
}
```

Code généré :

```java
if (username == null) {
    throw new NullPointerException();
}
```

---

# @Slf4j

Très utilisé dans Spring Boot.

Génère automatiquement un logger.

```java
import lombok.extern.slf4j.Slf4j;

@Slf4j
@Service
public class UserService {

    public void save() {

        log.info("Utilisateur sauvegardé");
    }
}
```

Évite :

```java
private static final Logger logger =
        LoggerFactory.getLogger(UserService.class);
```

---

# @Cleanup

Ferme automatiquement les ressources.

```java
@Cleanup
InputStream in =
    new FileInputStream("file.txt");
```

Equivalent :

```java
try {
}
finally {
    in.close();
}
```

Aujourd'hui on préfère généralement :

```java
try-with-resources
```

---

# @SneakyThrows

Permet d'éviter la déclaration explicite d'exceptions.

```java
@SneakyThrows
public void readFile() {

    Files.readString(Path.of("test.txt"));
}
```

À utiliser avec prudence.

---

# @Accessors

Permet une syntaxe fluide.

```java
@Getter
@Setter
@Accessors(chain = true)
public class User {

    private String username;
    private String email;
}
```

Utilisation :

```java
User user = new User()
        .setUsername("john")
        .setEmail("john@mail.com");
```

---

# Lombok dans Spring Boot

---

# DTO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserDto {

    private Long id;
    private String username;
}
```

---

# Entity JPA

### Bonnes pratiques

Éviter :

```java
@Data
@Entity
```

Car :

- relations JPA
- lazy loading
- equals/hashCode

peuvent provoquer des effets de bord.

Préférer :

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String username;
}
```

---

# Service Spring

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository repository;

    public void createUser() {

        log.info("Création utilisateur");
    }
}
```

L'injection se fait automatiquement :

```java
public UserService(UserRepository repository) {
    this.repository = repository;
}
```

---

# Controller Spring Boot

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/users")
public class UserController {

    private final UserService service;

    @GetMapping
    public List<UserDto> findAll() {

        return service.findAll();
    }
}
```

Aucun constructeur à écrire.

---

# Cas réel complet

## DTO

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
public class UserDto {

    private Long id;
    private String username;
    private String email;
}
```

---

## Entity

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
@Table(name = "users")
public class User {

    @Id
    @GeneratedValue
    private Long id;

    private String username;

    private String email;
}
```

---

## Repository

```java
@Repository
public interface UserRepository
        extends JpaRepository<User, Long> {
}
```

---

## Service

```java
@Slf4j
@Service
@RequiredArgsConstructor
public class UserService {

    private final UserRepository repository;

    public User save(User user) {

        log.info("Saving user {}", user.getUsername());

        return repository.save(user);
    }
}
```

---

## Controller

```java
@RestController
@RequiredArgsConstructor
@RequestMapping("/users")
public class UserController {

    private final UserService service;

    @PostMapping
    public User save(
            @RequestBody User user) {

        return service.save(user);
    }
}
```

---

# Annotations les plus utilisées en entreprise

| Annotation | Usage |
|------------|--------|
| @Getter | Getters |
| @Setter | Setters |
| @Builder | Construction fluide |
| @Data | DTO simples |
| @Value | DTO immutables |
| @Slf4j | Logging |
| @NoArgsConstructor | Constructeur vide |
| @AllArgsConstructor | Constructeur complet |
| @RequiredArgsConstructor | Injection Spring |
| @ToString | Debug |
| @EqualsAndHashCode | Comparaison |
| @Builder.Default | Valeur par défaut |

---

# Bonnes pratiques

## DTO

Utiliser :

```java
@Data
@Builder
@NoArgsConstructor
@AllArgsConstructor
```

ou

```java
@Value
@Builder
```

---

## Services Spring

Utiliser :

```java
@RequiredArgsConstructor
@Slf4j
@Service
```

---

## Controllers Spring

Utiliser :

```java
@RequiredArgsConstructor
@RestController
```

---

## Entités JPA

Préférer :

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
@Builder
@Entity
```

Éviter :

```java
@Data
@Entity
```

---

# Résumé

Lombok permet de supprimer la majorité du code répétitif Java grâce à des annotations.

Les annotations essentielles à maîtriser dans un projet Spring Boot sont :

```java
@Getter
@Setter
@Builder
@Data
@Value
@RequiredArgsConstructor
@NoArgsConstructor
@AllArgsConstructor
@Slf4j
```

Configuration minimale recommandée pour un projet Spring Boot moderne :

```java
@Getter
@Setter
@Builder
@NoArgsConstructor
@AllArgsConstructor
@Entity
```

pour les entités JPA, et :

```java
@RequiredArgsConstructor
@Slf4j
@Service
```

pour les services Spring.

Cela permet d'obtenir un code plus concis, plus lisible et plus facile à maintenir.
