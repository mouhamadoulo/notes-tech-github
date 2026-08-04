# Contexte

mon frontend angular utilise un fichier proxy conf json pour définir l'url du backend springboot , est-ce la bonne pratique ? dois-je faire une config dans le backend ?

# Solution

Oui, en **développement**, c'est **la meilleure pratique** ! En revanche, pour la **production**, la façon de gérer la communication change.

Voici un récapitulatif clair de ce qu'il faut retenir et de la configuration côté backend.

---

## 1. En développement : Le Proxy Angular (`proxy.conf.json`)

Pendant que tu développes avec `ng serve`, utiliser un `proxy.conf.json` est l'approche idéale pour deux raisons :

* **Évite la gestion de CORS en dev :** Le navigateur envoie la requête au serveur de dev Angular (`http://localhost:4200`), qui la redirige lui-même vers Spring Boot (`http://localhost:8080`). Comme c'est un transfert de serveur à serveur, le navigateur ne bloque rien.
* **URLs relatives dans le code :** Tu peux écrire tes services Angular avec des URLs relatives (ex: `/api/users`) au lieu de hardcoder `http://localhost:8080/api/users`.

---

## 2. En production : Ce qui change

En production, `proxy.conf.json` **n'existe plus**. Angular est compilé en fichiers statiques (HTML/JS/CSS) servis par un serveur web (Nginx, Apache, Caddy, etc.).

Deux options s'offrent à toi selon ton architecture :

### Option A : Même domaine / Reverse Proxy (Recommandé)

Un serveur comme **Nginx** sert le frontend et redirige les requêtes `/api` vers Spring Boot.

* **Besoin de configurer CORS dans Spring Boot ?** **Non.** Le frontend et le backend partagent le même nom de domaine et le même port.

### Option B : Frontend et Backend séparés (Domaines différents)

Par exemple : `[https://app.mon-domaine.com](https://app.mon-domaine.com)` (Front) et `[https://api.mon-domaine.com](https://api.mon-domaine.com)` (Back).

* **Besoin de configurer CORS dans Spring Boot ?** **Oui.** Le navigateur bloquera les requêtes cross-origin si Spring Boot ne les autorise pas explicitement.

---

## 3. Faut-il une configuration dans Spring Boot ?

* **En Dev (avec proxy) :** **Non**, aucune configuration Spring Boot n'est nécessaire.
* **En Prod (sans Reverse Proxy) :** **Oui**, il faut activer CORS dans Spring Boot.

### Exemple de configuration CORS dans Spring Boot (si besoin)

Si ton front et ton back sont sur des domaines différents en production, tu peux créer une classe de configuration WebMvc :

```java
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class CorsConfig implements WebMvcConfigurer {

    @Override
    public void addCorsMappings(CorsRegistry registry) {
        registry.addMapping("/api/**")
                .allowedOrigins("https://app.mon-domaine.com") // Ton domaine frontend en prod
                .allowedMethods("GET", "POST", "PUT", "DELETE", "OPTIONS")
                .allowedHeaders("*")
                .allowCredentials(true);
    }
}

```

> **Astuce Sécurité :** Évite d'utiliser `@CrossOrigin(origins = "*")` directement sur tes contrôleurs en production, surtout si tu gères de l'authentification avec des cookies ou du stockage de sessions.
>
> # Autre Question
>
> que mettre dans springboot pour que mon appli angular puisse communiquer avec mes api rest ?
>
> # Response
>
> Pour qu'une application **Angular** puisse communiquer avec ton backend **Spring Boot**, tu dois principalement configurer **CORS (Cross-Origin Resource Sharing)**.

## Solution recommandée : Configuration globale CORS

Dans un projet Spring Boot 4, crée une classe de configuration.

```java
package com.bank.paymentmessages.config;

import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;
import org.springframework.web.servlet.config.annotation.CorsRegistry;
import org.springframework.web.servlet.config.annotation.WebMvcConfigurer;

@Configuration
public class WebConfig {

    @Bean
    WebMvcConfigurer corsConfigurer() {
        return new WebMvcConfigurer() {

            @Override
            public void addCorsMappings(CorsRegistry registry) {

                registry.addMapping("/api/**")
                        .allowedOrigins("http://localhost:4200")
                        .allowedMethods("GET", "POST", "PUT", "PATCH", "DELETE", "OPTIONS")
                        .allowedHeaders("*")
                        .allowCredentials(true);
            }
        };
    }
}
```

---

## Si tu utilises Spring Security

Si plus tard tu ajoutes Spring Security, il faudra également activer CORS dans la configuration de sécurité :

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http
        .cors(Customizer.withDefaults())
        .csrf(csrf -> csrf.disable());

    return http.build();
}
```

et ajouter une source CORS :

```java
@Bean
CorsConfigurationSource corsConfigurationSource() {

    CorsConfiguration configuration = new CorsConfiguration();

    configuration.setAllowedOrigins(List.of("http://localhost:4200"));
    configuration.setAllowedMethods(List.of(
            "GET",
            "POST",
            "PUT",
            "PATCH",
            "DELETE",
            "OPTIONS"));

    configuration.setAllowedHeaders(List.of("*"));
    configuration.setAllowCredentials(true);

    UrlBasedCorsConfigurationSource source =
            new UrlBasedCorsConfigurationSource();

    source.registerCorsConfiguration("/**", configuration);

    return source;
}
```

---

## Alternative : directement sur un contrôleur

Tu peux également autoriser Angular uniquement sur un contrôleur :

```java
@RestController
@RequestMapping("/api/messages")
@CrossOrigin(origins = "http://localhost:4200")
public class PaymentMessageController {
}
```

Mais ce n'est pas la meilleure approche lorsqu'on a plusieurs contrôleurs.

---

## Pour ton projet Payment Messages

Je te conseillerais cette structure :

```
backend
└── src/main/java
    └── com.bank.paymentmessages
        ├── config
        │   ├── WebConfig.java
        │   └── OpenApiConfig.java
        ├── controller
        ├── service
        ├── repository
        ├── entity
        └── dto
```

---

## Bonus : utiliser des propriétés

Tu peux éviter de coder l'URL Angular en dur.

**application.yml**

```yaml
app:
  cors:
    allowed-origins:
      - http://localhost:4200
```

Puis l'injecter dans `WebConfig`. Cela permettra d'avoir des origines différentes entre les environnements (`dev`, `recette`, `prod`) sans modifier le code.

### En développement

* Angular : `http://localhost:4200`
* Spring Boot : `http://localhost:8080`

Angular pourra alors appeler par exemple :

```typescript
this.http.get('http://localhost:8080/api/messages');
```

sans être bloqué par le navigateur. Pour ton projet, une **configuration CORS globale dans `WebConfig`** est la solution la plus propre et la plus évolutive.
