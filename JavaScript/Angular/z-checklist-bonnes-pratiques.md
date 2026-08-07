# ✅ Checklist des bonnes pratiques Angular 22

> Cette checklist est orientée Angular 22 et privilégie les approches modernes :
> Standalone Components, Signals, zoneless change detection, contrôle de flux natif,
> rendu différé, APIs réactives Angular récentes et architecture orientée domaine.

---

# 🏗️ Architecture

## Structure du projet

- [ ] Organiser le code par domaine métier (feature-based architecture).
- [ ] Éviter les dossiers génériques contenant trop de responsabilités.
- [ ] Séparer clairement :
  - [ ] `core`
  - [ ] `shared`
  - [ ] `features`
- [ ] Limiter les dépendances entre features.
- [ ] Favoriser une architecture modulaire et découplée.
- [ ] Respecter les conventions Angular modernes.
- [ ] Préparer l'application pour un fonctionnement zoneless.

---
# 🏛️ Pattern Facade

## Isolation de la logique métier

- [ ] Utiliser une Facade par domaine métier lorsque celui-ci expose plusieurs cas d'usage.
- [ ] Les composants ne communiquent qu'avec leur Facade.
- [ ] Les composants ne connaissent pas directement les services HTTP, stores ou repositories.
- [ ] Une Facade orchestre les appels aux services, stores et autres dépendances.
- [ ] Les Facades exposent une API simple et orientée métier.

Exemple :

```typescript
@Component({...})
export class UsersPage {

  readonly users = this.usersFacade.users;
  readonly loading = this.usersFacade.loading;

  constructor(private readonly usersFacade: UsersFacade) {}

  ngOnInit() {
    this.usersFacade.loadUsers();
  }
}
```

## Responsabilités

Une Facade peut :

- [ ] Exposer des Signals ou Observables destinés à l'UI.
- [ ] Orchestrer plusieurs appels API.
- [ ] Coordonner plusieurs services métier.
- [ ] Gérer les interactions avec un store.
- [ ] Centraliser les cas d'usage d'une feature.

Une Facade ne doit pas :

- [ ] Contenir de logique de présentation.
- [ ] Contenir de logique HTTP bas niveau.
- [ ] Remplacer les services métier.
- [ ] Devenir un "God Service".

## Organisation

Structure recommandée :

```

features/
users/
users.page.ts
users.facade.ts
users.service.ts
users.repository.ts
users.models.ts

```

## Bonnes pratiques

- [ ] Préférer une Facade par feature.
- [ ] Retourner des Signals lorsque l'état est consommé par les composants.
- [ ] Conserver les appels HTTP dans des services dédiés.
- [ ] Limiter les dépendances injectées dans les composants.
- [ ] Les composants restent déclaratifs et testables.
- [ ] Tester les Facades indépendamment des composants.

---

# 🧩 Standalone First

## Composants et directives

- [ ] Utiliser exclusivement des composants standalone.
- [ ] Éviter la création de nouveaux `NgModule`.
- [ ] Migrer progressivement les anciens modules Angular.
- [ ] Importer explicitement les dépendances nécessaires.
- [ ] Utiliser les APIs standalone :
  - [ ] composants
  - [ ] directives
  - [ ] pipes
  - [ ] routes

## Bootstrap moderne

- [ ] Utiliser `bootstrapApplication()`.
- [ ] Éviter `platformBrowserDynamic()`.
- [ ] Préférer les providers fonctionnels.

Exemple :

```typescript
bootstrapApplication(AppComponent, {
  providers: [
    provideRouter(routes)
  ]
});
```

---

# ⚡ Change Detection moderne

## Zoneless Angular

* [ ] Évaluer l'utilisation du mode zoneless.
* [ ] Utiliser :

```typescript
provideZonelessChangeDetection()
```

* [ ] Éviter les dépendances à `NgZone`.
* [ ] Ne pas déclencher manuellement des cycles de détection.

## Bonnes pratiques

* [ ] Préférer les Signals aux mécanismes impératifs.
* [ ] Éviter les mutations d'état.
* [ ] Utiliser des structures immuables.
* [ ] Garder les composants déclaratifs.

---

# 🧩 Composants Angular 22

## API moderne des composants

* [ ] Utiliser `input()` au lieu de `@Input()` dans les nouveaux composants.

Exemple :

```typescript
user = input.required<User>();
```

* [ ] Utiliser `output()` au lieu de `@Output()`.

Exemple :

```typescript
selected = output<User>();
```

* [ ] Utiliser `model()` pour les composants avec liaison bidirectionnelle.

Exemple :

```typescript
value = model<string>();
```

## Responsabilités

- [ ] Garder les composants simples.
- [ ] Éviter la logique métier complexe dans les templates.
- [ ] Les composants orchestrent uniquement l'affichage et les interactions utilisateur.
- [ ] Déléguer les cas d'usage métier aux Facades.
- [ ] Les Facades délèguent la logique métier aux services spécialisés.
- [ ] Favoriser la composition.

---

# 🔎 Signal Queries

## Requêtes modernes

* [ ] Préférer les signal queries aux décorateurs classiques.

Utiliser :

* [ ] `viewChild()`
* [ ] `viewChildren()`
* [ ] `contentChild()`
* [ ] `contentChildren()`

Exemple :

```typescript
button = viewChild<ElementRef>('button');
```

---

# ⚡ Signals

## Etat local

* [ ] Utiliser `signal()` pour les états locaux.
* [ ] Utiliser `computed()` pour les valeurs dérivées.
* [ ] Utiliser `effect()` uniquement pour les effets de bord.
* [ ] Garder les effets simples.

## Bonnes pratiques

* [ ] Préférer `computed()` plutôt qu'un `effect()`.
* [ ] Eviter les chaînes complexes d'effets.
* [ ] Eviter les dépendances circulaires.
* [ ] Centraliser les états partagés.

---

# 🔄 RxJS

## Utilisation recommandée

Utiliser RxJS principalement pour :

* [ ] HTTP
* [ ] WebSocket
* [ ] streams complexes
* [ ] événements externes

## Bonnes pratiques

* [ ] Eviter les subscriptions imbriquées.

* [ ] Utiliser les opérateurs adaptés :

  * [ ] `switchMap`
  * [ ] `concatMap`
  * [ ] `mergeMap`
  * [ ] `exhaustMap`

* [ ] Utiliser `catchError`.

* [ ] Utiliser `shareReplay()` lorsque nécessaire.

* [ ] Eviter les subscriptions manuelles inutiles.

---

# 🔁 Interop Signals / RxJS

* [ ] Utiliser `toSignal()` pour exposer un Observable au template.
* [ ] Utiliser `toObservable()` uniquement lorsque nécessaire.
* [ ] Eviter les conversions inutiles.

---

# 🎨 Templates Angular 22

## Contrôle de flux natif

Utiliser :

* [ ] `@if`
* [ ] `@else`
* [ ] `@for`
* [ ] `@switch`

Eviter :

* [ ] `*ngIf`
* [ ] `*ngFor`
* [ ] `*ngSwitch`

---

## Boucles

* [ ] Toujours utiliser `track`.
* [ ] Utiliser une clé stable métier.

Exemple :

```html
@for (user of users(); track user.id) {
  <app-user-card [user]="user" />
}
```

---

# 🚀 Performance

## Rendu différé

* [ ] Utiliser `@defer` pour les composants lourds.
* [ ] Charger les fonctionnalités secondaires à la demande.

Exemple :

```html
@defer {
  <app-heavy-chart />
}
@placeholder {
  <app-loader />
}
```

---

## Lazy loading

* [ ] Utiliser `loadComponent`.
* [ ] Utiliser `loadChildren` pour les features complexes.
* [ ] Contrôler régulièrement la taille des bundles.

---

# 🌍 SSR / Hydration

## Applications universelles

* [ ] Activer SSR lorsque pertinent.
* [ ] Utiliser l'hydratation Angular.
* [ ] Vérifier la compatibilité des bibliothèques tierces.
* [ ] Eviter les accès directs au DOM côté serveur.

---

# 🌐 HTTP

## Organisation

- [ ] Centraliser les appels API dans des services ou repositories dédiés.
- [ ] Les composants ne consomment jamais directement les services HTTP.
- [ ] Les appels HTTP transitent par une Facade lorsque celle-ci existe.
- [ ] Utiliser un service ou repository par domaine métier.
- [ ] Typer complètement les DTO.

## Interceptors fonctionnels

* [ ] Utiliser `HttpInterceptorFn`.
* [ ] Centraliser :

  * [ ] authentification
  * [ ] refresh token
  * [ ] logs
  * [ ] erreurs
  * [ ] headers

---

# 🛣️ Routing

## Configuration

* [ ] Utiliser les routes standalone.
* [ ] Utiliser :

  * [ ] `loadComponent`
  * [ ] `loadChildren`

## Sécurité

* [ ] Utiliser des guards fonctionnels.

Exemple :

```typescript
export const authGuard: CanActivateFn = () =>
  inject(AuthService).isAuthenticated();
```

* [ ] Utiliser les resolvers uniquement lorsque nécessaire.

---

# 📝 Formulaires

## Reactive Forms

* [ ] Utiliser Reactive Forms typés.
* [ ] Eviter les formulaires non typés.
* [ ] Centraliser les validateurs.

Exemple :

```typescript
const form = new FormGroup({
  email: new FormControl('', {
    nonNullable: true
  })
});
```

## Signal Forms

* [ ] Evaluer l'utilisation des Signal Forms selon la maturité du projet.
* [ ] Ne pas migrer uniquement par effet de nouveauté.

---

# 🛡️ Sécurité

* [ ] Ne jamais stocker de secrets côté frontend.
* [ ] Ne jamais faire confiance aux entrées utilisateur.
* [ ] Utiliser l'encapsulation Angular contre XSS.
* [ ] Eviter `innerHTML`.
* [ ] Auditer les dépendances.

---

# 📦 Gestion d'état

## Etat local

* [ ] Utiliser Signals.

## Etat partagé

* [ ] Préférer un store basé Signals.
* [ ] Ajouter une librairie externe uniquement si nécessaire.

Avant un store global :

* [ ] Plusieurs fonctionnalités utilisent-elles cet état ?
* [ ] L'état doit-il survivre entre plusieurs routes ?
* [ ] Les Signals locaux sont-ils insuffisants ?

## Accès à l'état

- [ ] Les composants accèdent à l'état via une Facade.
- [ ] Une Facade expose les Signals, computed et actions nécessaires à l'UI.
- [ ] Le store reste une implémentation interne à la Facade.
- [ ] Éviter d'injecter directement un store dans les composants de présentation.
      
---

# 🧪 Tests

## Unitaires

* [ ] Tester les services métier.
* [ ] Tester les guards.
* [ ] Tester les composants critiques.
* [ ] Tester les computed signals.

## Intégration

* [ ] Tester les scénarios utilisateurs.
* [ ] Tester les interactions composants.

## End-to-End

* [ ] Couvrir les parcours critiques.
* [ ] Exécuter dans la CI/CD.

---

# 📏 Qualité de code

## TypeScript

* [ ] Activer `strict`.
* [ ] Eviter totalement `any`.
* [ ] Définir les types publics.
* [ ] Utiliser :

  * [ ] `Pick`
  * [ ] `Omit`
  * [ ] `Partial`
  * [ ] `Readonly`

---

# ♿ Accessibilité

* [ ] Utiliser HTML sémantique.
* [ ] Vérifier navigation clavier.
* [ ] Ajouter ARIA si nécessaire.
* [ ] Tester avec lecteurs d'écran.
* [ ] Vérifier contraste et focus.

---

# 🚀 CI/CD

* [ ] Lancer les tests automatiquement.
* [ ] Vérifier ESLint.
* [ ] Vérifier Prettier.
* [ ] Construire les applications production.
* [ ] Contrôler les tailles de bundles.
* [ ] Déployer automatiquement les environnements.

---

# 🔍 Revue finale avant Merge

* [ ] Composants standalone.
* [ ] APIs modernes utilisées (`input`, `output`, `model`).
* [ ] Templates avec `@if` / `@for`.
* [ ] Boucles avec `track`.
* [ ] Signals utilisés correctement.
* [ ] Aucun effet inutile.
* [ ] Subscriptions maîtrisées.
* [ ] Code strictement typé.
* [ ] Aucun secret exposé.
* [ ] Performance vérifiée.
* [ ] Accessibilité vérifiée.
* [ ] Les composants dépendent uniquement de leur Facade.
* [ ] Aucun appel HTTP direct depuis un composant.
* [ ] Les Facades restent focalisées sur un seul domaine métier.

---

# ⭐ Règle d'or Angular 22

* [ ] Standalone partout.
* [ ] Signals pour l'état réactif.
* [ ] RxJS pour les flux asynchrones complexes.
* [ ] Zoneless lorsque possible.
* [ ] `input()` / `output()` / `model()` pour les nouveaux composants.
* [ ] `@if`, `@for`, `@switch` partout.
* [ ] `@defer` pour les charges lourdes.
* [ ] SSR + hydration lorsque pertinent.
* [ ] TypeScript strict sans compromis.
* [ ] Architecture métier avant architecture technique.

```

Cette version est alignée avec une base de projet Angular 22 moderne et peut servir de **guide d’équipe / checklist de revue de PR**.
```
