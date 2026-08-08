Dans une application **Angular 22 + Spring Boot**, le schéma classique est : Angular collecte les identifiants, Spring Security authentifie l'utilisateur, le backend émet un **access token JWT**, puis Angular l'envoie sur les appels protégés.

### Diagramme Mermaid

```mermaid
sequenceDiagram
    autonumber

    actor U as Utilisateur
    participant A as Angular
    participant API as Spring Boot API
    participant SEC as Spring Security
    participant DB as Base de données

    U->>A: Saisit email + mot de passe
    A->>API: POST /api/auth/login
    Note over A,API: { email, password }

    API->>SEC: Authentifier l'utilisateur
    SEC->>DB: Charger l'utilisateur
    DB-->>SEC: User + password hash + rôles

    SEC->>SEC: Vérifier le mot de passe

    alt Identifiants invalides
        SEC-->>API: AuthenticationException
        API-->>A: 401 Unauthorized
        A-->>U: Afficher erreur
    else Identifiants valides
        SEC-->>API: Authentication OK
        API->>API: Générer Access Token JWT
        API-->>A: 200 OK + JWT

        Note over A: Stockage / gestion du token

        U->>A: Accède à une page protégée
        A->>API: GET /api/orders
        Note over A,API: Authorization: Bearer <JWT>

        API->>SEC: Validation JWT
        SEC->>SEC: Vérifier signature + expiration

        alt JWT invalide ou expiré
            SEC-->>A: 401 Unauthorized
        else JWT valide
            SEC->>SEC: Extraire user + rôles
            SEC-->>API: SecurityContext authentifié
            API->>DB: Charger les données
            DB-->>API: Données
            API-->>A: 200 OK + données
            A-->>U: Affichage
        end
    end
```

Il faut bien distinguer **authentification** et **autorisation** : le login prouve *qui tu es*, tandis que les rôles/permissions déterminent *ce que tu peux faire*.

## Et où stocker le JWT ?

Il existe principalement trois approches.

| Stockage                              | Sécurité XSS | CSRF           | Recommandation                    |
| ------------------------------------- | ------------ | -------------- | --------------------------------- |
| `localStorage`                        | ❌ Faible     | ✅ Peu concerné | ⚠️ À éviter pour une app sensible |
| `sessionStorage`                      | ❌ Faible     | ✅ Peu concerné | ⚠️ Pas beaucoup mieux             |
| Cookie `HttpOnly + Secure + SameSite` | ✅ Meilleur   | ⚠️ À gérer     | ✅ Recommandé                      |
| Access token uniquement en mémoire    | ✅ Très bon   | ✅              | ✅ Très intéressant                |

### 1. `localStorage` — simple mais vulnérable au XSS

On rencontre souvent :

```typescript
localStorage.setItem('access_token', token);
```

Puis un interceptor :

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {

  const token = localStorage.getItem('access_token');

  if (token) {
    req = req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    });
  }

  return next(req);
};
```

Le problème majeur est qu'un JavaScript exécuté à cause d'une faille XSS peut faire :

```javascript
localStorage.getItem('access_token')
```

et potentiellement exfiltrer le JWT.

Pour une application bancaire ou contenant des données sensibles, je n'en ferais donc pas l'architecture cible.

---

## 2. Cookie `HttpOnly` — généralement préférable

Le backend Spring Boot peut retourner :

```http
Set-Cookie: refresh_token=xxxx;
HttpOnly;
Secure;
SameSite=Strict;
Path=/api/auth/refresh
```

Avec :

* `HttpOnly` → JavaScript ne peut pas lire le cookie ;
* `Secure` → cookie transmis uniquement via HTTPS ;
* `SameSite` → réduit fortement certains scénarios CSRF ;
* expiration contrôlée côté serveur.

Angular **ne récupère donc jamais directement le refresh token**.

C'est le navigateur qui gère le cookie.

Cela empêche par exemple :

```typescript
document.cookie
```

de récupérer un cookie marqué `HttpOnly`.

---

# Architecture que je recommande

Pour une application Angular + Spring Boot moderne, une architecture particulièrement intéressante est :

```text
                    ┌─────────────────────────┐
                    │       Angular 22        │
                    │                         │
                    │ Access Token            │
                    │ uniquement en mémoire   │
                    └───────────┬─────────────┘
                                │
                  Authorization: Bearer JWT
                                │
                                ▼
                    ┌─────────────────────────┐
                    │      Spring Boot        │
                    │    Spring Security      │
                    └───────────┬─────────────┘
                                │
                         valide le JWT
                                │
                                ▼
                    ┌─────────────────────────┐
                    │ APIs / Services / DB    │
                    └─────────────────────────┘


        Refresh Token
              │
              ▼
┌─────────────────────────────────────────────┐
│ Navigateur                                  │
│                                             │
│ Cookie                                      │
│ HttpOnly                                    │
│ Secure                                      │
│ SameSite                                    │
│                                             │
│ inaccessible au JavaScript Angular          │
└─────────────────────────────────────────────┘
```

Le principe est alors :

```text
ACCESS TOKEN
durée courte
ex: 5-15 minutes

        +

REFRESH TOKEN
durée longue
ex: plusieurs heures/jours
stocké dans cookie HttpOnly
```

## Le workflow devient

```mermaid
sequenceDiagram
    autonumber

    actor U as Utilisateur
    participant A as Angular
    participant B as Browser
    participant S as Spring Boot
    participant DB as Database

    U->>A: Login
    A->>S: POST /auth/login

    S->>DB: Vérifier utilisateur
    DB-->>S: User + rôles

    S->>S: Génère Access Token
    S->>S: Génère Refresh Token

    S-->>B: Set-Cookie refresh_token
    Note over B: HttpOnly + Secure + SameSite

    S-->>A: Access Token JWT

    Note over A: Access Token conservé en mémoire

    A->>S: GET /api/orders
    Note over A,S: Authorization: Bearer AccessToken

    S-->>A: 200 OK

    Note over A,S: Quelques minutes plus tard...

    A->>S: GET /api/orders
    S-->>A: 401 Token expired

    A->>S: POST /auth/refresh
    Note over B,S: Cookie refresh_token envoyé automatiquement

    S->>S: Vérifie le Refresh Token
    S-->>A: Nouveau Access Token

    A->>S: GET /api/orders
    Note over A,S: Authorization: Bearer nouveauToken

    S-->>A: 200 OK
```

C'est une architecture très intéressante à implémenter dans un projet pédagogique, parce qu'elle permet de comprendre quasiment toute la chaîne.

### Côté Angular

Tu aurais typiquement :

```text
src/app/core/auth/
│
├── models/
│   ├── login-request.ts
│   ├── login-response.ts
│   └── user.ts
│
├── services/
│   ├── auth.service.ts
│   └── token.service.ts
│
├── interceptors/
│   ├── auth.interceptor.ts
│   └── refresh-token.interceptor.ts
│
├── guards/
│   ├── auth.guard.ts
│   └── role.guard.ts
│
└── auth.routes.ts
```

Le `TokenService` pourrait conserver l'access token **uniquement en mémoire**, par exemple avec un signal :

```typescript
@Injectable({
  providedIn: 'root'
})
export class TokenService {

  private readonly accessToken = signal<string | null>(null);

  setToken(token: string): void {
    this.accessToken.set(token);
  }

  getToken(): string | null {
    return this.accessToken();
  }

  clear(): void {
    this.accessToken.set(null);
  }
}
```

Puis ton interceptor :

```typescript
export const authInterceptor: HttpInterceptorFn = (req, next) => {

  const tokenService = inject(TokenService);

  const token = tokenService.getToken();

  if (!token) {
    return next(req);
  }

  const authenticatedRequest = req.clone({
    setHeaders: {
      Authorization: `Bearer ${token}`
    }
  });

  return next(authenticatedRequest);
};
```

### Côté Spring Boot

Tu retrouverais quelque chose comme :

```text
security/
├── SecurityConfig.java
├── JwtAuthenticationFilter.java
├── JwtService.java
├── CustomUserDetailsService.java
└── AuthenticationEntryPoint.java

auth/
├── AuthController.java
├── AuthService.java
├── LoginRequest.java
├── LoginResponse.java
└── RefreshTokenService.java
```

Et Spring Security suit alors approximativement :

```text
HTTP Request
     │
     ▼
JwtAuthenticationFilter
     │
     ├── récupérer Authorization
     │
     ├── extraire Bearer JWT
     │
     ├── vérifier signature
     │
     ├── vérifier expiration
     │
     ├── récupérer utilisateur
     │
     └── créer Authentication
                │
                ▼
         SecurityContext
                │
                ▼
         Controller
                │
       @PreAuthorize(...)
                │
                ▼
             Service
```

Il y a un point important à retenir : **Angular ne doit jamais être considéré comme responsable de la sécurité**. Un `roleGuard` Angular qui empêche un utilisateur d'ouvrir `/admin` améliore l'UX, mais ce n'est pas une véritable protection. L'autorisation doit toujours être réappliquée dans Spring Boot, par exemple avec `@PreAuthorize("hasRole('ADMIN')")`.

Pour ton projet d'apprentissage Angular 22 / Spring Boot, je te conseillerais donc d'implémenter successivement **login → JWT → interceptor → Spring Security filter → guards → rôles/permissions → expiration → refresh token HttpOnly → logout → révocation/rotation du refresh token**. C'est ce parcours qui te donnera une vision complète de l'authentification moderne.

---

Dans le diagramme précédent, l’autorisation était seulement évoquée avec les rôles et `@PreAuthorize`, mais **pas réellement détaillée**. Voici un diagramme complet qui enchaîne **authentification + autorisation**, avec ce qui se passe côté Angular et surtout côté Spring Security.

```mermaid
sequenceDiagram
    autonumber

    actor U as Utilisateur
    participant A as Angular 22
    participant API as Spring Boot
    participant JWT as JwtAuthenticationFilter
    participant SS as Spring Security
    participant DB as Base de données

    %% =====================
    %% AUTHENTIFICATION
    %% =====================

    rect rgb(235, 245, 255)
        Note over U,DB: 1. AUTHENTIFICATION — Qui es-tu ?

        U->>A: Saisit email + mot de passe
        A->>API: POST /api/auth/login

        API->>SS: authenticate(email, password)
        SS->>DB: Rechercher utilisateur
        DB-->>SS: User + password hash + rôles

        SS->>SS: Vérification du mot de passe

        alt Identifiants incorrects
            SS-->>API: AuthenticationException
            API-->>A: 401 Unauthorized
            A-->>U: Login refusé

        else Identifiants corrects
            SS-->>API: Utilisateur authentifié

            API->>API: Génération JWT
            Note over API: JWT contient par exemple<br/>sub = user@email.com<br/>roles = [USER, MANAGER]

            API-->>A: Access Token JWT
            Note over A: JWT conservé en mémoire
        end
    end

    %% =====================
    %% ANGULAR
    %% =====================

    rect rgb(245, 245, 255)
        Note over U,A: 2. Contrôle UX côté Angular

        U->>A: Accède à /admin

        A->>A: authGuard()
        A->>A: roleGuard("ADMIN")

        alt rôle absent
            A-->>U: Redirection /forbidden
        else rôle présent
            A-->>U: Affichage de la page
        end
    end

    %% =====================
    %% REQUETE API
    %% =====================

    U->>A: Demande suppression utilisateur

    A->>A: Auth Interceptor
    A->>API: DELETE /api/users/42
    Note over A,API: Authorization: Bearer eyJ...

    %% =====================
    %% JWT VALIDATION
    %% =====================

    rect rgb(255, 250, 230)
        Note over API,SS: 3. Validation du JWT

        API->>JWT: Requête interceptée

        JWT->>JWT: Extraire Bearer Token
        JWT->>JWT: Vérifier signature
        JWT->>JWT: Vérifier expiration
        JWT->>JWT: Extraire identité + rôles

        alt JWT invalide
            JWT-->>A: 401 Unauthorized
        else JWT valide
            JWT->>SS: Créer Authentication
            SS->>SS: Stocker dans SecurityContext
        end
    end

    %% =====================
    %% AUTORISATION
    %% =====================

    rect rgb(235, 255, 235)
        Note over API,DB: 4. AUTORISATION — As-tu le droit ?

        SS->>SS: Vérifier règles de sécurité

        Note over SS: Exemple :<br/>DELETE /api/users/**<br/>nécessite ROLE_ADMIN

        alt utilisateur possède ROLE_ADMIN
            SS->>API: Autorisation accordée
            API->>DB: DELETE user id=42
            DB-->>API: Suppression OK
            API-->>A: 204 No Content
            A-->>U: Utilisateur supprimé

        else utilisateur possède seulement ROLE_USER
            SS-->>A: 403 Forbidden
            A-->>U: Accès interdit
        end
    end
```

La distinction fondamentale est donc :

```text
AUTHENTIFICATION
       │
       │ "Qui es-tu ?"
       ▼
email/password
       │
       ▼
   JWT valide
       │
       ▼
Authentication
       │
       ▼
SecurityContext
       │
       │
       ▼
AUTORISATION
       │
       │ "As-tu le droit de faire ça ?"
       ▼
Roles / Permissions
       │
       ├── autorisé ─────► Controller / Service
       │
       └── interdit ─────► 403 Forbidden
```

### Exemple concret avec trois utilisateurs

Imaginons que ton application possède :

```text
Alice
roles = [USER]

Bob
roles = [USER, MANAGER]

Charles
roles = [USER, ADMIN]
```

Et les règles suivantes :

```text
GET    /api/profile         → USER, MANAGER, ADMIN
GET    /api/users           → MANAGER, ADMIN
DELETE /api/users/{id}      → ADMIN uniquement
```

Alice peut donc consulter son profil, mais pas la liste des utilisateurs. Bob peut consulter la liste mais ne peut pas supprimer un utilisateur. Charles peut faire les trois.

Côté Spring Boot, cela peut être défini globalement :

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    return http
        .authorizeHttpRequests(auth -> auth

            // Public
            .requestMatchers("/api/auth/**")
            .permitAll()

            // ADMIN uniquement
            .requestMatchers(HttpMethod.DELETE, "/api/users/**")
            .hasRole("ADMIN")

            // MANAGER ou ADMIN
            .requestMatchers(HttpMethod.GET, "/api/users/**")
            .hasAnyRole("MANAGER", "ADMIN")

            // Tout utilisateur authentifié
            .requestMatchers("/api/profile/**")
            .authenticated()

            // Tout le reste interdit
            .anyRequest()
            .denyAll()
        )
        .build();
}
```

Mais tu peux également protéger directement les méthodes :

```java
@PreAuthorize("hasRole('ADMIN')")
@DeleteMapping("/{id}")
public void deleteUser(@PathVariable Long id) {
    userService.delete(id);
}
```

Ou avec plusieurs rôles :

```java
@PreAuthorize("hasAnyRole('ADMIN', 'MANAGER')")
@GetMapping
public List<UserDto> findAll() {
    return userService.findAll();
}
```

Et tu peux aller plus loin que les **rôles** en utilisant des **permissions** :

```java
@PreAuthorize("hasAuthority('USER_DELETE')")
public void deleteUser(Long id) {
    // ...
}
```

Tu arrives alors à une organisation du type :

```text
ADMIN
 ├── USER_READ
 ├── USER_CREATE
 ├── USER_UPDATE
 └── USER_DELETE

MANAGER
 ├── USER_READ
 └── USER_UPDATE

USER
 └── PROFILE_READ
```

C'est souvent plus flexible que de coder uniquement des `ROLE_ADMIN`, `ROLE_MANAGER`, etc.

### Et Angular dans tout ça ?

Angular peut également connaître les rôles :

```typescript
interface CurrentUser {
  id: number;
  email: string;
  roles: string[];
}
```

Puis avoir un guard :

```typescript
export const adminGuard: CanActivateFn = () => {

  const authService = inject(AuthService);
  const router = inject(Router);

  if (authService.hasRole('ADMIN')) {
    return true;
  }

  return router.createUrlTree(['/forbidden']);
};
```

Et :

```typescript
{
  path: 'admin',
  canActivate: [authGuard, adminGuard],
  loadComponent: () =>
    import('./admin/admin.component')
      .then(c => c.AdminComponent)
}
```

Mais le point **le plus important de tout le système** est :

> **Un Guard Angular n'est pas une mesure de sécurité.**

Quelqu'un peut complètement ignorer Angular et appeler directement :

```bash
DELETE /api/users/42
Authorization: Bearer <JWT>
```

La vraie protection doit donc toujours être :

```text
Angular
   │
   ├── Guard
   │      └── UX / navigation
   │
   ▼
Spring Boot
   │
   ├── JwtAuthenticationFilter
   │
   ├── SecurityContext
   │
   ├── SecurityFilterChain
   │
   └── @PreAuthorize
   │
   ▼
VRAIE SÉCURITÉ
```

Enfin, retiens aussi la différence HTTP essentielle : **401 Unauthorized** signifie en pratique « je ne peux pas t'authentifier » — token absent, invalide ou expiré — tandis que **403 Forbidden** signifie « je sais qui tu es, mais tu n'as pas le droit de faire cette action ». C'est précisément le passage de l'**authentification** à l'**autorisation**.

---

Oui. Et surtout, **JWT, OAuth2, OpenID Connect et SSO ne sont pas des alternatives au même niveau** : ils répondent à des problèmes différents.

Le plus important est de séparer les concepts :

| Concept             | À quoi ça sert ?                               | Exemple                                        |
| ------------------- | ---------------------------------------------- | ---------------------------------------------- |
| **Session serveur** | Maintenir l’utilisateur connecté               | Cookie `JSESSIONID`                            |
| **JWT**             | Format de jeton contenant des claims           | `sub`, `roles`, `exp`                          |
| **OAuth 2.0**       | Déléguer l’accès à une ressource               | “Autoriser cette app à accéder à Google Drive” |
| **OpenID Connect**  | Authentifier un utilisateur au-dessus d’OAuth2 | “Se connecter avec Google”                     |
| **SSO**             | Une connexion pour plusieurs applications      | Microsoft Entra ID, Keycloak                   |
| **SAML 2.0**        | Fédération d’identité, souvent en entreprise   | SSO avec un IdP corporate                      |

Donc **JWT n’est pas un protocole d’authentification à lui seul**. C’est essentiellement un **format de token**.

## 1. Alternative classique à JWT : la session serveur

C’est l’approche historique et toujours parfaitement valable.

```mermaid
sequenceDiagram
    actor U as Utilisateur
    participant A as Angular
    participant S as Spring Boot
    participant DB as DB / Session Store

    U->>A: Login / mot de passe
    A->>S: POST /login
    S->>DB: Vérifier utilisateur
    DB-->>S: Utilisateur valide

    S->>S: Créer session
    S-->>A: Set-Cookie: JSESSIONID=xyz

    Note over A: Cookie HttpOnly

    A->>S: GET /api/orders
    Note over A,S: Cookie JSESSIONID envoyé
    S->>DB: Charger session
    DB-->>S: User + rôles
    S-->>A: Données
```

Spring Boot connaît très bien cette approche avec Spring Security.

Avantage : facile à révoquer, car le serveur contrôle la session. Inconvénient : le serveur doit stocker l’état de session, éventuellement dans Redis en environnement distribué.

Avec JWT, on cherche généralement une architecture plus **stateless** :

```text
Session :
client -> sessionId -> serveur -> session utilisateur

JWT :
client -> token signé contenant les informations -> serveur
```

## 2. OAuth 2.0

OAuth2 concerne avant tout **l’autorisation déléguée**.

Imagine que ton application Angular souhaite accéder aux données Microsoft de l’utilisateur.

Tu ne veux surtout pas demander :

```text
Email Microsoft :
Mot de passe Microsoft :
```

Tu rediriges plutôt l’utilisateur vers Microsoft :

```mermaid
sequenceDiagram
    actor U as Utilisateur
    participant A as Angular
    participant IDP as Authorization Server
    participant API as Spring Boot API

    U->>A: Clique "Se connecter"
    A->>IDP: Authorization Request

    IDP->>U: Login
    U->>IDP: Identifiants + MFA

    IDP-->>A: Authorization Code

    A->>IDP: Code + PKCE
    IDP-->>A: Access Token

    A->>API: Authorization: Bearer Access Token
    API->>API: Validation du token
    API-->>A: Ressource protégée
```

OAuth2 définit notamment des rôles :

```text
Resource Owner
     ↓
Utilisateur

Client
     ↓
Application Angular

Authorization Server
     ↓
Keycloak / Entra ID / Auth0 / Okta

Resource Server
     ↓
API Spring Boot
```

Et plusieurs flows existent. Pour une SPA Angular moderne, le standard est généralement :

```text
Authorization Code Flow
+
PKCE
```

Le vieux `Implicit Flow` est à éviter.

## 3. OpenID Connect

OpenID Connect, ou **OIDC**, ajoute une couche d’authentification à OAuth2.

En simplifiant :

```text
OAuth2
    ↓
"Cette application peut-elle accéder à cette ressource ?"

OIDC
    ↓
"Qui est cet utilisateur ?"
```

OIDC ajoute notamment un **ID Token**.

Tu peux donc avoir :

```text
Access Token
    ↓
Accéder aux APIs

ID Token
    ↓
Identité de l'utilisateur
```

Un ID Token est très souvent lui-même un JWT :

```json
{
  "sub": "123456789",
  "name": "Moustapha",
  "email": "user@example.com",
  "iss": "https://identity.example.com",
  "aud": "angular-client",
  "exp": 1786215600
}
```

Cela montre pourquoi dire :

> OAuth2 ou JWT ?

n’est pas vraiment correct.

Tu peux très bien avoir :

```text
OAuth2
+
OpenID Connect
+
JWT
```

ensemble.

## 4. SSO

SSO signifie **Single Sign-On**.

C’est davantage une **architecture / expérience d’authentification** qu’un protocole.

Imaginons une entreprise avec :

```text
Application RH
Application Finance
Application Tickets
Application Intranet
```

Sans SSO :

```text
RH        -> login
Finance   -> login
Tickets   -> login
Intranet  -> login
```

Avec SSO :

```text
             ┌─────────────┐
             │    IdP      │
             │ Keycloak    │
             │ Entra ID    │
             └──────┬──────┘
                    │
                 LOGIN
                    │
        ┌───────────┼────────────┐
        ↓           ↓            ↓
       RH       Finance       Intranet
```

Une fois authentifié auprès de l’Identity Provider, les différentes applications peuvent reconnaître l’utilisateur sans lui demander de se reconnecter.

## 5. OIDC + OAuth2 + SSO ensemble

C’est justement ce qu’on retrouve souvent dans les architectures modernes.

```mermaid
flowchart LR
    U["Utilisateur"]

    A1["Angular App A"]
    A2["Angular App B"]
    A3["Angular App C"]

    IDP["Identity Provider<br/>Keycloak / Entra ID / Okta"]

    API1["Spring Boot API A"]
    API2["Spring Boot API B"]

    U --> A1
    U --> A2
    U --> A3

    A1 -->|"OIDC Login"| IDP
    A2 -->|"OIDC Login"| IDP
    A3 -->|"OIDC Login"| IDP

    IDP -->|"ID Token"| A1
    IDP -->|"Access Token"| A1

    A1 -->|"Bearer Access Token"| API1
    A1 -->|"Bearer Access Token"| API2

    API1 -->|"Validate Token"| IDP
    API2 -->|"Validate Token"| IDP
```

Ici :

```text
OIDC
→ gère l'identité

OAuth2
→ gère les autorisations / tokens d'accès

JWT
→ peut être le format des tokens

SSO
→ permet d'utiliser le même login sur plusieurs applications

Keycloak / Entra ID
→ fournit tout cela
```

## 6. Et SAML ?

Il y a aussi **SAML 2.0**, très présent dans les grandes entreprises et les environnements bancaires historiques.

On peut résumer grossièrement :

```text
SAML
→ XML
→ ancien mais extrêmement répandu en entreprise
→ très utilisé pour le SSO

OIDC
→ JSON / HTTP / REST-friendly
→ plus naturel pour SPA, mobile, APIs
→ souvent privilégié pour les architectures modernes
```

Exemple de SSO :

```text
Angular
   ↓
Application
   ↓
Identity Provider entreprise
   ↓
SAML / OIDC
   ↓
Utilisateur authentifié
```

## 7. Cas Angular + Spring Boot moderne

Une architecture fréquente serait :

```text
                         ┌─────────────────────┐
                         │      Keycloak       │
                         │        ou           │
                         │ Microsoft Entra ID  │
                         └─────────┬───────────┘
                                   │
                         OAuth2 / OpenID Connect
                                   │
                                   ▼
┌───────────────┐          ┌────────────────┐
│   Angular 22  │◄────────►│ Identity       │
│               │          │ Provider       │
└───────┬───────┘          └────────────────┘
        │
        │ Access Token
        │
        │ Authorization:
        │ Bearer eyJ...
        ▼
┌────────────────────────────┐
│ Spring Boot                │
│                            │
│ OAuth2 Resource Server     │
│ Spring Security            │
│                            │
│ validation signature       │
│ issuer                     │
│ audience                   │
│ scopes                     │
│ roles                      │
└─────────────┬──────────────┘
              │
              ▼
        Controllers
              │
       @PreAuthorize(...)
```

Spring Boot peut alors être configuré en **Resource Server** :

```java
@Bean
SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {

    http
        .authorizeHttpRequests(auth -> auth
            .requestMatchers("/public/**").permitAll()
            .requestMatchers("/admin/**").hasRole("ADMIN")
            .anyRequest().authenticated()
        )
        .oauth2ResourceServer(oauth2 ->
            oauth2.jwt(Customizer.withDefaults())
        );

    return http.build();
}
```

Et tu peux avoir dans `application.yml` :

```yaml
spring:
  security:
    oauth2:
      resourceserver:
        jwt:
          issuer-uri: https://idp.example.com/realms/my-app
```

Le gros changement par rapport à ton exemple précédent est que **Spring Boot ne génère plus forcément lui-même les JWT**.

C'est :

```text
Avant :

Angular
   ↓ login/password
Spring Boot
   ↓
génère JWT


Avec OIDC :

Angular
   ↓
Identity Provider
   ↓
génère les tokens

Angular
   ↓ access token
Spring Boot
   ↓
valide le token
```

## 8. Que choisir ?

Pour apprendre, je te conseille de voir les architectures dans cet ordre :

1. **Session + cookie HttpOnly** : pour comprendre l'authentification stateful.
2. **JWT maison Spring Boot** : pour comprendre access token, refresh token, signature, expiration, interceptor et filters.
3. **OAuth2** : pour comprendre authorization server, client et resource server.
4. **OpenID Connect** : pour externaliser réellement l'authentification.
5. **Keycloak + Angular + Spring Boot** : excellent projet pédagogique pour voir OIDC, OAuth2, rôles, scopes, refresh, SSO et fédération d'identité.
6. **SSO multi-applications** : deux Angular + deux Spring Boot partageant le même Keycloak.
7. **SAML** : intéressant ensuite, surtout si tu travailles avec des SI d'entreprise existants.

Le point clé à garder en tête est donc :

```text
JWT       = format de token
OAuth2    = framework d'autorisation
OIDC      = protocole d'authentification basé sur OAuth2
SSO       = expérience : un login pour plusieurs applications
SAML      = autre protocole de fédération / SSO
Keycloak  = produit capable d'implémenter OAuth2 + OIDC + SAML + SSO
```

Pour un projet **Angular 22 + Spring Boot** moderne, **OIDC/OAuth2 avec Keycloak ou Microsoft Entra ID** est probablement le scénario le plus intéressant après avoir compris le JWT “fait maison”.

