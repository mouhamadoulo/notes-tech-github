
# Sécurité en Angular (versions ≥ 18) : le guide complet

> Ce guide couvre Angular 18, 19, 20 et 21. La plupart des mécanismes existent depuis longtemps, mais certaines fonctionnalités (CSP automatique, validation SSRF côté SSR) sont récentes : elles sont signalées avec la version d'introduction. La version actuelle au moment de la rédaction est Angular v21.

## Table des matières

1. Le modèle de sécurité d'Angular
2. XSS : sanitisation et contextes de sécurité
3. `DomSanitizer` et les méthodes `bypassSecurityTrust*`
4. Le compilateur AOT et l'injection de templates
5. Content Security Policy (CSP) et `autoCsp`
6. Trusted Types
7. Protection CSRF/XSRF avec `HttpClient`
8. XSSI (JSON vulnerability)
9. SSRF côté rendu serveur (SSR)
10. Authentification & autorisation (niveau applicatif)
11. Sécurité des dépendances
12. Checklist récapitulative

---

## 1. Le modèle de sécurité d'Angular

Le principe fondateur est simple : **toute valeur est considérée comme non fiable par défaut**. Lorsqu'une valeur est insérée dans le DOM via une liaison de template ou une interpolation, Angular l'assainit et l'échappe automatiquement.

À l'inverse, **les templates sont considérés comme du code de confiance** (exécutable). C'est pourquoi la règle d'or est :

> Ne jamais construire un template en concaténant des entrées utilisateur avec de la syntaxe de template. Cela ouvre la porte à l'injection de code arbitraire.

Angular distingue deux types de menaces :

| Niveau | Pris en charge par Angular | Exemples |
| --- | --- | --- |
| Vulnérabilités web (XSS, CSRF, XSSI, SSRF) | Oui, mécanismes intégrés | Sanitisation, intercepteur XSRF |
| Sécurité applicative (authn/authz) | Non, à votre charge | Guards, gestion des tokens |

Les sections 2 à 9 traitent du premier niveau, la section 10 du second.

---

## 2. XSS : sanitisation et contextes de sécurité

Le XSS (Cross-Site Scripting) consiste à injecter du code malveillant dans une page. Angular bloque cela en assainissant les valeurs selon leur **contexte de sécurité** :

| Contexte | Utilisé quand… | Assainissable ? |
| --- | --- | --- |
| `HTML` | liaison vers `[innerHTML]` | Oui |
| `STYLE` | liaison CSS vers `[style]` | Oui |
| `URL` | liaison `[href]`, `[src]` | Oui |
| `RESOURCE_URL` | `<script src>`, `<iframe src>` | **Non** (contient du code arbitraire) |

### Interpolation vs `innerHTML`

L'interpolation `{{ }}` échappe **toujours** le contenu : le HTML est affiché tel quel, jamais interprété.

```html
<!-- inner-html.component.html -->

<!-- Le contenu est échappé : l'utilisateur voit littéralement <script>... -->
<p>{{ htmlSnippet }}</p>

<!-- Le HTML est interprété, mais Angular le NETTOIE avant insertion -->
<p [innerHTML]="htmlSnippet"></p>
```

```typescript
// inner-html.component.ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-inner-html',
  templateUrl: './inner-html.component.html',
})
export class InnerHtmlComponent {
  // Valeur potentiellement contrôlée par un attaquant (URL, formulaire…)
  htmlSnippet = 'Bonjour <script>alert("piraté")</script> <b>monde</b>';
}
```

Résultat de la liaison `[innerHTML]` : Angular **supprime la balise `<script>`** mais conserve le `<b>`. En mode développement, un avertissement est affiché dans la console chaque fois qu'une valeur est modifiée par la sanitisation — surveillez ces messages, ils révèlent souvent un flux de données mal maîtrisé.

### Pourquoi éviter les API DOM directes

Les API natives (`document`, `ElementRef.nativeElement`, bibliothèques tierces de manipulation du DOM) **ne bénéficient d'aucune sanitisation automatique**.

```typescript
// ❌ DANGEREUX : contournement total de la protection Angular
constructor(private el: ElementRef) {}

ngAfterViewInit() {
  // Si userInput est contrôlé par un attaquant => XSS
  this.el.nativeElement.innerHTML = this.userInput;
}
```

Préférez toujours les templates Angular. Si vous devez vraiment assainir manuellement une valeur, utilisez `DomSanitizer.sanitize()` avec le bon `SecurityContext` :

```typescript
import { inject, SecurityContext } from '@angular/core';
import { DomSanitizer } from '@angular/platform-browser';

const sanitizer = inject(DomSanitizer);

// Renvoie une chaîne nettoyée, sûre à insérer dans un contexte HTML
const safe = sanitizer.sanitize(SecurityContext.HTML, this.userInput);
```

---

## 3. `DomSanitizer` et les méthodes `bypassSecurityTrust*`

Parfois on a légitimement besoin d'insérer du contenu « dangereux » (une URL `<iframe>`, un SVG, etc.). On peut alors dire à Angular : « j'ai vérifié cette valeur, fais-moi confiance ».

```typescript
import { DomSanitizer } from '@angular/platform-browser';
```

Les cinq méthodes disponibles, à utiliser avec **le contexte exact** :

- `bypassSecurityTrustHtml`
- `bypassSecurityTrustStyle`
- `bypassSecurityTrustScript`
- `bypassSecurityTrustUrl`
- `bypassSecurityTrustResourceUrl`

### Exemple : intégrer une vidéo YouTube en toute sécurité

L'attribut `<iframe src>` est un contexte `RESOURCE_URL` : Angular **refusera** une liaison directe. On construit donc une `SafeResourceUrl`, mais **au plus près de la donnée d'entrée validée**.

```typescript
// video.component.ts
import { Component, inject } from '@angular/core';
import { DomSanitizer, SafeResourceUrl } from '@angular/platform-browser';

@Component({
  selector: 'app-video',
  template: `
    <input #id placeholder="ID YouTube" (change)="loadVideo(id.value)" />
    @if (videoUrl) {
      <iframe [src]="videoUrl" width="640" height="360" title="vidéo"></iframe>
    }
  `,
})
export class VideoComponent {
  private sanitizer = inject(DomSanitizer);
  videoUrl?: SafeResourceUrl;

  loadVideo(id: string) {
    // 1. On VALIDE strictement l'entrée (ici : ID alphanumérique de 11 caractères)
    if (!/^[A-Za-z0-9_-]{11}$/.test(id)) {
      throw new Error('ID YouTube invalide');
    }
    // 2. On construit l'URL à partir d'une base fixe et de l'ID validé
    const url = `https://www.youtube.com/embed/${id}`;
    // 3. On marque la valeur comme fiable
    this.videoUrl = this.sanitizer.bypassSecurityTrustResourceUrl(url);
  }
}
```

> ⚠️ **Règle d'or.** Ne passez **jamais** une valeur brute contrôlée par l'utilisateur à `bypassSecurityTrust*`. Validez d'abord (regex, allowlist), construisez la valeur à partir d'une base fixe, et n'appelez `bypass…` qu'en dernier ressort. Ces méthodes sont marquées « *Security Risk* » dans la documentation et doivent être auditées en revue de sécurité.

---

## 4. Le compilateur AOT et l'injection de templates

Le compilateur **AOT (Ahead-Of-Time)** est le compilateur par défaut du CLI Angular. Il prévient toute une classe de vulnérabilités appelée **injection de template**, en plus d'améliorer les performances.

Le compilateur **JIT** (Just-In-Time), lui, compile les templates dans le navigateur à l'exécution. Comme Angular **fait confiance au code de template**, générer dynamiquement des templates contenant des données utilisateur revient à donner le contrôle total de l'application à un attaquant.

```typescript
// ❌ ANTI-PATTERN absolu : ne JAMAIS faire cela
@Component({
  // Injecter une donnée utilisateur dans le template = injection de code
  template: `<div>${userControlledValue}</div>`,
})
```

À retenir :

- Conservez **AOT en production** (comportement par défaut, ne le désactivez pas).
- Pour des formulaires dynamiques, utilisez les **Dynamic Forms** d'Angular, pas la génération de templates.
- Ne construisez **jamais** de template Angular côté serveur avec un moteur de templating.

---

## 5. Content Security Policy (CSP) et `autoCsp`

La CSP est une défense **en profondeur** : même si une faille XSS existe, le navigateur refuse d'exécuter les scripts non autorisés. On l'active via l'en-tête HTTP `Content-Security-Policy`.

Politique minimale pour une application Angular neuve :

```
Content-Security-Policy: default-src 'self'; style-src 'self' 'nonce-XXXXX'; script-src 'self' 'nonce-XXXXX';
```

Le **nonce** est une valeur aléatoire, **unique à chaque requête** et imprévisible. Le navigateur n'exécute que les scripts/styles portant ce nonce.

### Trois façons de fournir le nonce à Angular

**a) Option `autoCsp`** — la plus simple. Angular 19 a introduit la CSP automatique basée sur des hash (en Developer Preview), qui se configure dans `angular.json` via la propriété `autoCsp`. Angular génère alors automatiquement les hash/nonces dans `index.html`.

```json
// angular.json
{
  "projects": {
    "mon-app": {
      "architect": {
        "build": {
          "options": {
            "security": {
              "autoCsp": true
            }
          }
        }
      }
    }
  }
}
```

**b) Attribut `ngCspNonce`** — si vous disposez d'un templating côté serveur qui injecte le nonce dans l'en-tête HTTP **et** dans `index.html` :

```html
<app-root ngCspNonce="RANDOM_NONCE_PAR_REQUETE"></app-root>
```

**c) Token `CSP_NONCE`** — si le nonce est connu à l'exécution et que vous voulez pouvoir mettre `index.html` en cache :

```typescript
// main.ts
import { bootstrapApplication, CSP_NONCE } from '@angular/core';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, {
  providers: [
    {
      provide: CSP_NONCE,
      useValue: (globalThis as any).myRandomNonceValue,
    },
  ],
});
```

> 🔑 **Unicité du nonce.** Le nonce doit être unique par requête et non prévisible ; s'il est devinable, la protection CSP est contournée. Attention avec un CDN : si le serveur d'origine génère le nonce et que le CDN met la réponse HTML en cache, tous les visiteurs reçoivent le même nonce. Générez-le idéalement au niveau Edge/CDN.

Exemple d'en-tête plus complet pour une application réelle :

```
Content-Security-Policy:
  default-src 'self';
  script-src 'self' 'nonce-<dynamique>';
  style-src 'self' 'nonce-<dynamique>';
  img-src 'self' https:;
  connect-src 'self' https://api.mondomaine.com;
  object-src 'none';
  base-uri 'self';
  frame-ancestors 'none';
  require-trusted-types-for 'script';
```

Chaque directive restreint un type de ressource : `connect-src` limite les API joignables, `object-src 'none'` bloque les plugins, `frame-ancestors 'none'` empêche le clickjacking, etc.

---

## 6. Trusted Types

Trusted Types est une fonctionnalité de plateforme web qui **verrouille les « sinks » DOM dangereux** (`innerHTML`, `eval`, `insertAdjacentHTML`…) pour qu'ils n'acceptent que des valeurs typées non falsifiables, jamais des chaînes brutes. C'est la défense la plus efficace contre le DOM-XSS, car elle agit au niveau du DOM lui-même.

On l'active via l'en-tête HTTP, avec l'une des **politiques Angular** :

| Politique | Quand l'activer |
| --- | --- |
| `angular` | Toujours requise pour qu'Angular fonctionne sous Trusted Types |
| `angular#bundler` | Si vous faites du lazy-loading de chunks |
| `angular#unsafe-bypass` | Si vous utilisez une méthode `bypassSecurityTrust*` |
| `angular#unsafe-jit` | Si l'application tourne en mode JIT |
| `angular#unsafe-upgrade` | Application hybride AngularJS (`@angular/upgrade`) |

Application standard (AOT, sans bypass) :

```
Content-Security-Policy: trusted-types angular; require-trusted-types-for 'script';
```

Application qui utilise `bypassSecurityTrustHtml` & co. :

```
Content-Security-Policy: trusted-types angular angular#unsafe-bypass; require-trusted-types-for 'script';
```

> Configurez ces en-têtes à trois endroits : l'infrastructure de production, `ng serve` (via la propriété `headers` de `angular.json`) pour le dev local, et Karma (`customHeaders` dans `karma.config.js`) pour les tests. Si un navigateur ne supporte pas Trusted Types, l'application reste protégée par le `DomSanitizer` d'Angular.

---

## 7. Protection CSRF/XSRF avec `HttpClient`

Une attaque **CSRF** trompe un utilisateur authentifié pour qu'il déclenche, à son insu, une requête modifiant l'état (virement, suppression…) depuis un site malveillant. Le navigateur joint automatiquement les cookies du domaine cible.

La parade standard est le motif **« cookie-to-header token »** :

1. Le serveur place un token aléatoire dans un cookie lisible par JS (`XSRF-TOKEN` par défaut).
2. Le client lit ce cookie et le renvoie dans un en-tête (`X-XSRF-TOKEN`).
3. Le serveur compare cookie et en-tête.

Seul du code servi par **votre** domaine peut lire ce cookie (même-origine), donc un site tiers ne peut pas reproduire l'en-tête.

`HttpClient` fait **automatiquement** la moitié client de ce schéma. Par défaut, un intercepteur lit le cookie `XSRF-TOKEN` et le place dans l'en-tête `X-XSRF-TOKEN`, uniquement sur les requêtes mutantes (POST, PUT, DELETE…) vers des URL relatives ou de même origine, jamais sur les GET/HEAD.

### Configuration

```typescript
// app.config.ts
import { ApplicationConfig } from '@angular/core';
import {
  provideHttpClient,
  withXsrfConfiguration,
} from '@angular/common/http';

export const appConfig: ApplicationConfig = {
  providers: [
    provideHttpClient(
      // À utiliser SEULEMENT si votre backend emploie d'autres noms
      withXsrfConfiguration({
        cookieName: 'CUSTOM_XSRF_TOKEN',
        headerName: 'X-Custom-Xsrf-Header',
      }),
    ),
  ],
};
```

Pour désactiver (rare, par exemple si vous gérez le CSRF autrement) :

```typescript
import { provideHttpClient, withNoXsrfProtection } from '@angular/common/http';

provideHttpClient(withNoXsrfProtection());
```

> ⚠️ Angular ne gère que **la moitié client**. Votre backend doit poser le cookie `XSRF-TOKEN` et vérifier l'en-tête sur chaque requête éligible. Sans cela, la protection est inopérante. Pensez aussi à un **nom de cookie unique par application** si plusieurs apps partagent un domaine.

---

## 8. XSSI (JSON vulnerability)

L'**XSSI** (Cross-Site Script Inclusion) permet à un site tiers de lire les données d'une API JSON en l'incluant via une balise `<script>`, à condition que le JSON renvoyé soit exécutable comme du JavaScript.

La parade serveur consiste à **préfixer toutes les réponses JSON** avec une chaîne non exécutable, par convention `)]}',\n`. `HttpClient` reconnaît cette convention et retire automatiquement ce préfixe avant de parser la réponse.

```
)]}',
{"utilisateur": "alice", "solde": 1000}
```

Vous n'avez **rien à faire côté Angular** : il suffit que le backend ajoute ce préfixe. C'est une bonne pratique simple à activer côté serveur.

---

## 9. SSRF côté rendu serveur (SSR)

Avec le rendu serveur (SSR/SSG via `@angular/ssr`), une nouvelle surface d'attaque apparaît : la **SSRF** par en-têtes d'hôte falsifiés. Angular intègre désormais une validation stricte des en-têtes `Host`, `X-Forwarded-Host`, `X-Forwarded-Proto`, `X-Forwarded-Prefix` et `X-Forwarded-Port`. Par défaut, tous les en-têtes `X-Forwarded-*` sont traités comme non fiables et retirés de la requête ; les noms d'hôte non reconnus provoquent une réponse `400 Bad Request`.

### Déclarer les hôtes autorisés

```json
// angular.json
{
  "projects": {
    "mon-app": {
      "architect": {
        "build": {
          "builder": "@angular/build:application",
          "options": {
            "security": {
              "allowedHosts": [
                "mondomaine.com",
                "*.mondomaine.com"  // tous les sous-domaines
              ]
            }
          }
        }
      }
    }
  }
}
```

Au niveau du moteur d'application (Node) :

```typescript
import { AngularNodeAppEngine } from '@angular/ssr/node';

const nodeAppEngine = new AngularNodeAppEngine({
  allowedHosts: ['mondomaine.com', '*.trusted.com'],
  // N'activez ceci QUE derrière un proxy de confiance qui valide ces en-têtes
  trustProxyHeaders: ['x-forwarded-host', 'x-forwarded-proto'],
});
```

On peut aussi passer la variable d'environnement `NG_ALLOWED_HOSTS="mondomaine.com,*.trusted.com"`.

> ⚠️ Évitez `allowedHosts: ['*']` : accepter n'importe quel `Host` expose à l'injection d'en-tête d'hôte et à la SSRF. N'activez `trustProxyHeaders` que si un reverse proxy de confiance contrôle réellement ces en-têtes, sinon un attaquant pourra les usurper.

---

## 10. Authentification & autorisation (niveau applicatif)

Ce niveau **n'est pas couvert par Angular** : c'est à vous de l'implémenter. Voici les patterns sûrs et idiomatiques pour Angular ≥ 18 (standalone + API fonctionnelles).

### a) Intercepteur fonctionnel pour le token d'accès

Depuis Angular 15+, on privilégie les intercepteurs **fonctionnels** :

```typescript
// auth.interceptor.ts
import { HttpInterceptorFn, HttpErrorResponse } from '@angular/common/http';
import { inject } from '@angular/core';
import { catchError, throwError } from 'rxjs';
import { AuthService } from './auth.service';

export const authInterceptor: HttpInterceptorFn = (req, next) => {
  const auth = inject(AuthService);
  const token = auth.getAccessToken();

  // N'attachez le token QU'AUX appels vers votre propre API,
  // jamais vers des domaines tiers (fuite de credentials).
  const isApiUrl = req.url.startsWith('/api') ||
                   req.url.startsWith('https://api.mondomaine.com');

  const authReq = token && isApiUrl
    ? req.clone({ setHeaders: { Authorization: `Bearer ${token}` } })
    : req;

  return next(authReq).pipe(
    catchError((err: HttpErrorResponse) => {
      if (err.status === 401) {
        auth.logout(); // token expiré/invalide
      }
      return throwError(() => err);
    }),
  );
};
```

Enregistrement :

```typescript
// app.config.ts
import { provideHttpClient, withInterceptors } from '@angular/common/http';

export const appConfig = {
  providers: [
    provideHttpClient(withInterceptors([authInterceptor])),
  ],
};
```

### b) Guards fonctionnels (`CanActivateFn`)

Depuis Angular 15+, les guards basés sur des classes sont dépréciés au profit des **guards fonctionnels** :

```typescript
// auth.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';

export const authGuard: CanActivateFn = (route, state) => {
  const auth = inject(AuthService);
  const router = inject(Router);

  if (auth.isAuthenticated()) {
    return true;
  }
  // Redirige vers le login en mémorisant l'URL demandée
  return router.createUrlTree(['/login'], {
    queryParams: { redirectTo: state.url },
  });
};
```

Guard de **rôle** (autorisation fine) :

```typescript
// role.guard.ts
import { inject } from '@angular/core';
import { CanActivateFn, Router } from '@angular/router';
import { AuthService } from './auth.service';

export const roleGuard = (requiredRole: string): CanActivateFn => {
  return () => {
    const auth = inject(AuthService);
    const router = inject(Router);
    return auth.hasRole(requiredRole)
      ? true
      : router.createUrlTree(['/forbidden']);
  };
};
```

Utilisation dans les routes :

```typescript
// app.routes.ts
import { Routes } from '@angular/router';
import { authGuard } from './auth.guard';
import { roleGuard } from './role.guard';

export const routes: Routes = [
  {
    path: 'admin',
    canActivate: [authGuard, roleGuard('admin')],
    loadComponent: () => import('./admin/admin.component')
      .then(m => m.AdminComponent),
  },
];
```

> 🔒 **Un guard ne sécurise QUE l'UI.** Un utilisateur peut désactiver le JS, modifier le bundle ou appeler l'API directement. **Chaque endpoint sensible doit revérifier l'autorisation côté serveur.** Le guard améliore l'expérience, le serveur fait l'autorité.

### c) Stockage des tokens : le point le plus risqué

| Méthode | Vulnérable au XSS | Vulnérable au CSRF | Verdict |
| --- | --- | --- | --- |
| `localStorage` / `sessionStorage` | **Oui** (lisible en JS) | Non | À éviter pour les tokens sensibles |
| Cookie `HttpOnly` + `Secure` + `SameSite` | Non (illisible en JS) | Oui (mitigé par token CSRF) | **Recommandé** |
| Mémoire JS (variable/service) | Partiellement | Non | Bon pour l'access token court |

Approche recommandée : **refresh token dans un cookie `HttpOnly; Secure; SameSite=Strict`** (géré par le serveur, invisible au JS) + **access token court en mémoire** (effacé au rechargement, ré-obtenu via le refresh token).

```typescript
// auth.service.ts (esquisse)
import { Injectable, signal } from '@angular/core';

@Injectable({ providedIn: 'root' })
export class AuthService {
  // Token en mémoire uniquement : jamais dans localStorage
  private accessToken = signal<string | null>(null);

  getAccessToken(): string | null {
    return this.accessToken();
  }

  setAccessToken(token: string): void {
    this.accessToken.set(token);
  }

  isAuthenticated(): boolean {
    return this.accessToken() !== null;
  }

  logout(): void {
    this.accessToken.set(null);
    // Le cookie HttpOnly du refresh token est révoqué côté serveur
  }
}
```

> Ne décodez jamais un JWT côté client pour décider d'une autorisation « de confiance » : sa signature n'est vérifiable que par le serveur. Le décodage client ne sert qu'à de l'affichage (nom, rôle indicatif).

---

## 11. Sécurité des dépendances

Une part importante des failles vient des paquets npm. Intégrez ces vérifications à votre CI.

```bash
# Audit des vulnérabilités connues
npm audit

# Correction automatique quand c'est possible
npm audit fix

# Détecter les paquets obsolètes
npm outdated
```

Bonnes pratiques :

- **Restez à jour avec Angular.** Les bibliothèques Angular reçoivent des mises à jour régulières qui corrigent des failles ; consultez le change log pour les correctifs de sécurité. Utilisez `ng update` régulièrement.
- **Ne modifiez jamais votre copie d'Angular** : une version « customisée » prend du retard et rate les correctifs.
- Bloquez les versions avec un **lockfile** (`package-lock.json`) et vérifiez-le en CI.
- Activez **Dependabot / Renovate** pour les montées de version automatiques.
- Surveillez les **advisories GitHub** du dépôt `angular/angular-cli` et `angular/angular`.

---

## 12. Checklist récapitulative

```text
XSS
[ ] Toujours passer par les templates ; éviter nativeElement.innerHTML
[ ] N'utiliser bypassSecurityTrust* qu'après validation stricte
[ ] Surveiller les avertissements de sanitisation en dev
[ ] Conserver le compilateur AOT en production

Défense en profondeur
[ ] Header Content-Security-Policy (autoCsp ou nonce unique/requête)
[ ] Trusted Types activés (trusted-types angular; require-trusted-types-for 'script')
[ ] frame-ancestors 'none' / object-src 'none'

HTTP
[ ] Protection XSRF active + backend qui pose et vérifie le token
[ ] Préfixe anti-XSSI )]}', côté serveur
[ ] Token Bearer attaché UNIQUEMENT aux URL de votre API

SSR
[ ] allowedHosts configuré (pas de '*')
[ ] trustProxyHeaders seulement derrière un proxy de confiance

Authn / Authz
[ ] Guards fonctionnels (CanActivateFn) côté UI
[ ] Autorisation RE-vérifiée sur CHAQUE endpoint serveur
[ ] Access token en mémoire, refresh token en cookie HttpOnly/Secure/SameSite
[ ] Aucun token sensible dans localStorage

Dépendances
[ ] npm audit + ng update intégrés à la CI
[ ] Lockfile vérifié, Dependabot/Renovate activé
```

> **Principe directeur.** Angular protège efficacement la couche **rendu** (XSS, sanitisation, CSP, Trusted Types) et fournit des aides HTTP (XSRF, XSSI). Mais **l'authentification, l'autorisation et la validation des données restent toujours, en dernier ressort, la responsabilité du serveur.** Le client améliore l'expérience ; le serveur fait l'autorité.
