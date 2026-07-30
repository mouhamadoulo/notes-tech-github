Si tu utilises **JWT (JSON Web Token)** pour sécuriser une API REST, le token est généralement récupéré de l'une des façons suivantes :

### 1. Après l'authentification (le plus courant)

Le client envoie ses identifiants à un endpoint de connexion :

```http
POST /login
Content-Type: application/json

{
  "username": "alice",
  "password": "motdepasse"
}
```

Le serveur valide les identifiants et renvoie un JWT :

```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9..."
}
```

Le client stocke ce token (mémoire, sessionStorage, localStorage, ou cookie HttpOnly selon l'architecture).

---

### 2. Lors des appels aux API

Le client envoie ensuite le JWT dans l'en-tête HTTP `Authorization` :

```http
GET /api/users
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

### 3. Côté serveur

Le serveur récupère le token depuis l'en-tête :

#### En Java (Spring Boot)

```java
String header = request.getHeader("Authorization");

if (header != null && header.startsWith("Bearer ")) {
    String jwt = header.substring(7);
}
```

Avec Spring Security, cette logique est généralement placée dans un filtre (`OncePerRequestFilter`) qui :

1. lit l'en-tête `Authorization`,
2. extrait le JWT,
3. le valide,
4. authentifie l'utilisateur.

---

### 4. Récupérer les informations contenues dans le JWT

Une fois le token validé, tu peux lire son contenu (les *claims*) :

```java
Claims claims = Jwts.parserBuilder()
    .setSigningKey(key)
    .build()
    .parseClaimsJws(jwt)
    .getBody();

String username = claims.getSubject();
```

---

### Schéma global

```text
Client
   │
   │ POST /login (username, password)
   ▼
Serveur
   │
   │ Vérifie les identifiants
   │ Génère un JWT
   ▼
Client
   │
   │ Authorization: Bearer <JWT>
   ▼
API REST
   │
   │ Vérifie le JWT
   │ Récupère l'utilisateur
   ▼
Réponse
```

Si tu utilises **Spring Boot + Spring Security**, je peux aussi te montrer un exemple complet avec un `JwtAuthenticationFilter` et la configuration de sécurité.

---

Oui, dans la majorité des architectures **le token est récupéré côté front**.

Le déroulement classique est le suivant :

1. L'utilisateur saisit son identifiant et son mot de passe.
2. Le **front** envoie une requête `POST /login` au serveur.
3. Le **back-end** authentifie l'utilisateur et génère un JWT.
4. Le **back-end** renvoie le JWT dans la réponse.
5. Le **front** récupère ce token et le stocke (ou il est stocké automatiquement dans un cookie selon l'architecture).
6. Pour chaque appel à une API protégée, le **front** envoie le token.

Par exemple :

```text
Utilisateur
      │
      ▼
Front
POST /login
(username/password)
      │
      ▼
Back-end
Vérifie les identifiants
Génère un JWT
      │
      ▼
Front
Reçoit le JWT
      │
GET /api/users
Authorization: Bearer <JWT>
      │
      ▼
Back-end
Valide le JWT
Retourne les données
```

### Cas 1 : Token dans la réponse (très courant)

Le front reçoit le token :

```json
{
  "token": "eyJhbGciOiJIUzI1NiIs..."
}
```

Puis l'envoie dans les requêtes :

```javascript
fetch("/api/users", {
  headers: {
    Authorization: `Bearer ${token}`
  }
});
```

### Cas 2 : Cookie HttpOnly (plus sécurisé)

Dans certaines applications, le back-end **ne renvoie pas directement le JWT au JavaScript**. Il le place dans un **cookie HttpOnly**.

Dans ce cas :

* le front **ne peut pas lire le token** (c'est volontaire pour limiter les risques de vol par XSS) ;
* le navigateur envoie automatiquement le cookie à chaque requête vers le serveur.

Dans cette architecture, on ne manipule pas l'en-tête `Authorization` côté front.

### Quelle approche choisir ?

* **SPA (React, Angular, Vue) + API REST** : le plus fréquent est un JWT envoyé dans l'en-tête `Authorization: Bearer ...`.
* **Applications nécessitant un niveau de sécurité élevé** : l'utilisation d'un **cookie HttpOnly** est souvent préférée, car le token n'est jamais accessible au code JavaScript.

En résumé, **oui, le front récupère généralement le token**, sauf lorsqu'il est stocké dans un **cookie HttpOnly**, auquel cas le navigateur le gère automatiquement sans que le front puisse y accéder.

---

Pour une architecture **Angular + Spring Boot**, les pratiques recommandées ont évolué. Aujourd'hui, le choix dépend principalement de ton niveau d'exigence en matière de sécurité.

## Option 1 : JWT dans un cookie HttpOnly (recommandée)

C'est l'approche généralement recommandée pour une application web Angular.

### Fonctionnement

1. L'utilisateur se connecte.
2. Spring Boot génère un JWT.
3. Le JWT est envoyé dans un **cookie HttpOnly, Secure, SameSite**.
4. Angular ne voit jamais le token.
5. Le navigateur envoie automatiquement le cookie à chaque requête.

```
Angular
    │ POST /login
    ▼
Spring Boot
    │
    ├── Vérifie les identifiants
    └── Set-Cookie: access_token=...; HttpOnly; Secure
    ▼
Navigateur
    │
    └── envoie automatiquement le cookie
```

### Avantages

* Le JWT n'est pas accessible via JavaScript.
* Protection contre le vol du token en cas de XSS.
* Pas besoin de gérer le stockage du token dans Angular.

### Points d'attention

* Il faut gérer les attaques **CSRF** (par exemple avec un token CSRF ou `SameSite=Lax/Strict` selon les cas).
* Il faut configurer correctement le CORS (`allowCredentials(true)` côté Spring et `withCredentials: true` côté Angular).

---

## Option 2 : JWT dans le header Authorization

C'est l'approche historique, encore très utilisée.

```
POST /login
        │
        ▼
JWT renvoyé dans le body
        │
        ▼
Angular stocke le token
        │
Authorization: Bearer xxx
        ▼
Spring Boot
```

### Où stocker le token ?

Du meilleur au moins bon :

* ✔️ mémoire (service Angular) si possible
* ✔️ sessionStorage (compromis acceptable)
* ⚠️ localStorage (courant mais plus exposé au XSS)

Pourquoi éviter `localStorage` ? Parce que si un script malveillant s'exécute dans la page (XSS), il peut lire le token.

---

# Durée de vie des tokens

Il est recommandé de distinguer :

* **Access Token**

  * durée de vie courte (5 à 15 minutes)
  * utilisé pour appeler les API

* **Refresh Token**

  * durée de vie longue (quelques jours à quelques semaines)
  * idéalement stocké dans un cookie HttpOnly
  * permet d'obtenir un nouvel Access Token sans reconnecter l'utilisateur

C'est le modèle utilisé par de nombreuses applications modernes.

---

# Avec Spring Security

La configuration classique consiste à :

* utiliser Spring Security avec des filtres JWT ;
* rendre l'application **stateless** (`SessionCreationPolicy.STATELESS`) ;
* vérifier le JWT à chaque requête ;
* placer l'utilisateur authentifié dans le `SecurityContext`.

---

# Avec Angular

Les bonnes pratiques sont :

* utiliser un **HttpInterceptor** pour ajouter automatiquement le header `Authorization` si tu utilises des Bearer tokens ;
* centraliser toute la logique d'authentification dans un `AuthService` ;
* gérer le rafraîchissement du token automatiquement lorsqu'un `401 Unauthorized` est reçu (si tu utilises des refresh tokens).

---

# Architecture recommandée aujourd'hui

Pour une application Angular + Spring Boot moderne :

* ✅ Access Token JWT à courte durée de vie.
* ✅ Refresh Token stocké dans un **cookie HttpOnly**.
* ✅ Si tu choisis les cookies pour l'authentification, préfère également y stocker l'Access Token pour éviter qu'il soit accessible à JavaScript.
* ✅ HTTPS obligatoire en production.
* ✅ Validation des signatures, des dates d'expiration (`exp`) et des autorisations (claims/roles) côté Spring.
* ✅ Gestion des rôles via les claims du JWT (par exemple `ROLE_ADMIN`, `ROLE_USER`).

Cette approche réduit les risques liés au XSS tout en conservant une authentification sans état (stateless) côté serveur. Elle est aujourd'hui largement adoptée pour les applications Angular/Spring Boot exposées sur le Web.

