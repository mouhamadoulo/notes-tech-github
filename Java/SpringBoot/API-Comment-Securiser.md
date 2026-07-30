Pour sécuriser des API REST en **Spring Boot**, la solution dépend du type d'application (interne, publique, microservices, etc.). Dans la plupart des cas, **Spring Security + JWT ou OAuth2** est l'approche recommandée.

Voici les principales bonnes pratiques.

### 1. Ajouter Spring Security

Ajoute la dépendance :

**Maven**

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```

Par défaut, toutes les routes seront protégées.

---

### 2. Authentification avec JWT

Le schéma classique est :

1. L'utilisateur s'authentifie (`/login`)
2. Le serveur vérifie les identifiants
3. Le serveur génère un JWT
4. Le client envoie ce JWT dans chaque requête :

```
Authorization: Bearer eyJhbGciOiJIUzI1NiIs...
```

---

### 3. Configurer Spring Security

Exemple :

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http
        .csrf(csrf -> csrf.disable())
        .sessionManagement(session ->
            session.sessionCreationPolicy(SessionCreationPolicy.STATELESS))
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/login").permitAll()
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .requestMatchers("/user/**").hasAnyRole("USER", "ADMIN")
            .anyRequest().authenticated()
        );

    return http.build();
}
```

---

### 4. Vérifier le JWT avec un filtre

Créer un filtre qui :

* lit le header Authorization
* vérifie la signature
* vérifie l'expiration
* charge l'utilisateur
* remplit le `SecurityContext`

```java
public class JwtAuthenticationFilter extends OncePerRequestFilter {

    @Override
    protected void doFilterInternal(
            HttpServletRequest request,
            HttpServletResponse response,
            FilterChain filterChain)
            throws ServletException, IOException {

        String header = request.getHeader("Authorization");

        if (header != null && header.startsWith("Bearer ")) {
            String token = header.substring(7);

            // validation du JWT
            // récupération du username
            // création de Authentication
        }

        filterChain.doFilter(request, response);
    }
}
```

---

### 5. Utiliser HTTPS

Ne jamais exposer une API REST en HTTP.

Toutes les communications doivent passer par :

```
https://api.monsite.com
```

---

### 6. Limiter les permissions

Exemple :

```java
@PreAuthorize("hasRole('ADMIN')")
@GetMapping("/users")
public List<User> getUsers() {
    ...
}
```

ou

```java
@PreAuthorize("hasAuthority('READ_PRODUCTS')")
```

---

### 7. Valider les entrées

Toujours utiliser Bean Validation :

```java
public class UserDto {

    @NotBlank
    private String username;

    @Email
    private String email;

    @Size(min = 8)
    private String password;
}
```

Puis :

```java
@PostMapping
public void create(@Valid @RequestBody UserDto dto) {
}
```

---

### 8. Ne jamais exposer les exceptions

Créer un gestionnaire global :

```java
@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(Exception.class)
    public ResponseEntity<String> handle(Exception e) {
        return ResponseEntity
                .status(HttpStatus.INTERNAL_SERVER_ERROR)
                .body("Une erreur est survenue");
    }
}
```

Évite de renvoyer des traces de pile ou des informations sensibles.

---

### 9. Gérer le CORS

Limiter les domaines autorisés :

```java
@Bean
CorsConfigurationSource corsConfigurationSource() {

    CorsConfiguration config = new CorsConfiguration();

    config.setAllowedOrigins(List.of(
        "https://monfront.com"
    ));

    config.setAllowedMethods(List.of(
        "GET","POST","PUT","DELETE"
    ));

    config.setAllowedHeaders(List.of("*"));

    UrlBasedCorsConfigurationSource source =
            new UrlBasedCorsConfigurationSource();

    source.registerCorsConfiguration("/**", config);

    return source;
}
```

---

### 10. Protéger contre les attaques par force brute

Mettre en place un **rate limiting** (par IP, utilisateur ou clé API), par exemple avec **Bucket4j** ou via une passerelle d'API (API Gateway, NGINX, etc.).

---

### 11. Stocker les secrets correctement

Ne pas écrire :

```properties
jwt.secret=monSecret123
```

dans un dépôt Git.

Préférer :

* variables d'environnement ;
* un gestionnaire de secrets (Vault, AWS Secrets Manager, Azure Key Vault, etc.).

---

### 12. Journaliser les accès

Conserver des journaux contenant :

* utilisateur authentifié ;
* adresse IP (si pertinente) ;
* endpoint appelé ;
* date ;
* code de retour.

Évite toutefois de journaliser des mots de passe, des jetons JWT complets ou d'autres données sensibles.

---

## Architecture recommandée

```
Client
   │
   │ Login
   ▼
Spring Boot
   │
   ├── AuthenticationManager
   ├── JWT Generator
   └── retourne le JWT
         │
         ▼
Client
   │
Authorization: Bearer JWT
   │
   ▼
JwtAuthenticationFilter
   │
Validation du JWT
   │
SecurityContext
   │
Contrôleur REST
```

## Pour une API REST moderne

La combinaison suivante est largement adoptée :

* Spring Security 6
* Authentification **JWT** stateless
* HTTPS obligatoire
* Contrôle d'accès par rôles ou permissions
* Validation des entrées avec `@Valid`
* Gestion centralisée des exceptions
* CORS restrictif
* Rate limiting
* Gestion sécurisée des secrets
* Journalisation adaptée et surveillance

Si ton projet est destiné à des utilisateurs externes ou s'intègre à un système d'identité, tu peux aller plus loin en utilisant **OAuth2/OpenID Connect** (par exemple avec Keycloak, Auth0 ou un autre fournisseur d'identité), ce qui évite d'implémenter toi-même toute la gestion des utilisateurs et des jetons.
