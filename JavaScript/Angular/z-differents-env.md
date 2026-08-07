À partir des versions récentes d'Angular (et donc d'Angular 22), la gestion des environnements a évolué. La recommandation n'est plus de tout mettre dans `environment.ts` avec des remplacements de fichiers systématiques, surtout pour les applications qui doivent être déployées dans plusieurs environnements sans reconstruire l'application.

Voici les bonnes pratiques actuelles.

## 1. Que mettre dans la configuration Angular (`angular.json`) ?

La configuration sert principalement à définir :

* les optimisations de build
* les budgets
* les source maps
* les assets
* les remplacements éventuels de fichiers (si tu en utilises encore)

Exemple :

```json
"configurations": {
  "production": {
    "optimization": true,
    "outputHashing": "all"
  },
  "development": {
    "optimization": false,
    "sourceMap": true
  }
}
```

Il est recommandé de limiter au maximum les `fileReplacements`.

---

## 2. Que mettre dans `environment.ts` ?

Aujourd'hui, uniquement des constantes de build.

Par exemple :

```ts
export const environment = {
  production: false,
  appName: 'Mon App',
  version: '22.0.0'
};
```

ou

```ts
export const environment = {
  production: true
};
```

Éviter d'y mettre :

* URL d'API
* tokens
* clés
* configuration spécifique au serveur

Pourquoi ?

Parce que cela oblige à reconstruire l'application pour chaque environnement.

---

## 3. Où mettre l'URL de l'API ?

La meilleure pratique consiste à charger une configuration au démarrage.

Exemple :

```
assets/
    config/
        config.json
```

```json
{
    "apiUrl": "https://api.monsite.com",
    "authUrl": "https://auth.monsite.com"
}
```

Puis un service :

```ts
@Injectable({
  providedIn: 'root'
})
export class AppConfigService {

  config!: AppConfig;

  constructor(private http: HttpClient) {}

  load() {
    return firstValueFrom(
      this.http.get<AppConfig>('assets/config/config.json')
    ).then(c => this.config = c);
  }
}
```

Au démarrage :

```ts
bootstrapApplication(AppComponent, {
  providers: [
    provideAppInitializer(() => inject(AppConfigService).load())
  ]
});
```

Ensuite :

```ts
this.config.apiUrl
```

Ainsi :

* une seule build
* un simple remplacement du JSON lors du déploiement

---

## 4. Où mettre les secrets ?

Nulle part dans Angular.

Une SPA est téléchargée par le navigateur.

Tout ce qui est dedans est visible :

* API Keys
* Client Secret
* Password
* JWT secret
* etc.

Il faut considérer que :

> Tout ce qui est envoyé au navigateur est public.

Les secrets doivent rester :

* sur le backend
* dans des variables d'environnement du serveur
* dans un coffre-fort de secrets (Vault, Azure Key Vault, AWS Secrets Manager, etc.)

---

## 5. Les API Keys ?

Il faut distinguer deux cas.

### API publique

Exemple :

* Google Maps
* Stripe Publishable Key
* reCAPTCHA Site Key

Ces clés sont faites pour être visibles.

On peut les mettre dans :

```json
config.json
```

ou

```ts
environment.ts
```

si elles sont identiques pour toutes les installations.

---

### API privée

Exemple :

```
OpenAI Secret Key
Stripe Secret Key
GitHub Token
```

Ne jamais les mettre dans Angular.

Le frontend appelle ton backend.

Le backend appelle ensuite le service tiers.

---

## 6. Comment gérer plusieurs environnements ?

Par exemple :

```
config.dev.json
config.test.json
config.prod.json
```

Au déploiement :

```
config.json
```

est remplacé par le bon fichier.

Exemple :

```
dist/

assets/
    config/
        config.json
```

L'application est identique.

Seule la configuration change.

C'est la méthode la plus utilisée avec Docker, Kubernetes, Azure App Service, Nginx, etc.

---

## 7. Architecture recommandée

```
src/
│
├── app/
│   ├── core/
│   │     app-config.service.ts
│   │
│   └── ...
│
├── assets/
│   └── config/
│        config.json
│
├── environments/
│   └── environment.ts
│
└── main.ts
```

`environment.ts`

```ts
export const environment = {
  production: false
};
```

`config.json`

```json
{
  "apiUrl": "...",
  "authUrl": "...",
  "applicationName": "...",
  "featureFlags": {
    "newDashboard": true
  }
}
```

`AppConfig`

```ts
export interface AppConfig {
  apiUrl: string;
  authUrl: string;
  applicationName: string;
  featureFlags: {
    newDashboard: boolean;
  };
}
```

## En résumé

Je recommande cette répartition :

| Type de donnée                                                | Emplacement                                                         |
| ------------------------------------------------------------- | ------------------------------------------------------------------- |
| `production`, flags de build                                  | `environment.ts`                                                    |
| URL API                                                       | `assets/config/config.json` chargé au démarrage                     |
| Feature flags                                                 | `config.json` (ou un service distant si elles changent fréquemment) |
| Nom de l'application                                          | `config.json`                                                       |
| Version de build                                              | `environment.ts` ou injectée lors du build                          |
| Clés publiques (Google Maps, Stripe publishable)              | `config.json`                                                       |
| Secrets (OpenAI, Stripe Secret, mots de passe, JWT Secret...) | Backend uniquement, jamais dans Angular                             |

Cette approche permet de produire une seule build Angular pour tous les environnements (dev, recette, préproduction, production), en ne modifiant que le fichier de configuration fourni au moment du déploiement.
