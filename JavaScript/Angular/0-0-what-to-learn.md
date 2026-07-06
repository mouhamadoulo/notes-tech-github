Pour bien maîtriser **Angular**, il est utile de progresser des bases vers les concepts avancés. Voici les notions les plus importantes.

## 1. Les fondamentaux

* Architecture d'une application Angular
* Structure d'un projet Angular
* CLI Angular (`ng new`, `ng serve`, `ng generate`, `ng build`)
* TypeScript (classes, interfaces, decorators, generics)

## 2. Les composants (Components)

* Création de composants
* Cycle de vie (`ngOnInit`, `ngOnDestroy`, `ngOnChanges`, etc.)
* Communication entre composants

  * `@Input()`
  * `@Output()`
  * `EventEmitter`
* Projection de contenu (`<ng-content>`)

## 3. Les templates

* Interpolation (`{{ }}`)
* Property Binding (`[property]`)
* Event Binding (`(click)`)
* Two-way Binding (`[(ngModel)]`)
* Directives structurelles

  * `*ngIf`
  * `*ngFor`
  * `*ngSwitch`
* Directives d'attribut

## 4. Les directives

* Directives intégrées
* Création de directives personnalisées
* `HostListener`
* `HostBinding`

## 5. Les pipes

* Pipes intégrés
* Paramètres des pipes
* Pipes personnalisés
* Pipes purs et impurs

## 6. Les services et l'injection de dépendances

* Création de services
* Dependency Injection (DI)
* Fournisseurs (`providers`)
* Hiérarchie des injecteurs
* Singleton

## 7. Le routage

* Configuration des routes
* Paramètres d'URL
* Query Parameters
* Navigation
* Routes enfants
* Lazy Loading
* Guards

  * `CanActivate`
  * `CanDeactivate`
  * `Resolve`
  * `CanMatch`

## 8. Les formulaires

### Template-driven Forms

* `FormsModule`
* Validation

### Reactive Forms

* `FormControl`
* `FormGroup`
* `FormArray`
* Validators
* Validators personnalisés

## 9. HTTP

* `HttpClient`
* Requêtes GET/POST/PUT/DELETE
* Gestion des erreurs
* Interceptors
* Authentification avec JWT

## 10. RxJS (très important)

* Observables
* Subscription
* Subjects
* BehaviorSubject
* Operators

  * `map`
  * `filter`
  * `switchMap`
  * `mergeMap`
  * `concatMap`
  * `debounceTime`
  * `distinctUntilChanged`
  * `catchError`
  * `tap`
  * `forkJoin`
  * `combineLatest`
  * `zip`

## 11. Gestion de l'état

* Services + BehaviorSubject
* Signals (Angular moderne)
* NgRx (Store)
* Component Store

## 12. Les Signals (Angular 16+)

* `signal()`
* `computed()`
* `effect()`
* Signal Inputs
* Signal Queries
* Comparaison avec RxJS

## 13. Détection des changements

* Change Detection
* `OnPush`
* `ChangeDetectorRef`
* Optimisation des performances

## 14. Modules et composants standalone

* NgModules (applications historiques)
* Standalone Components
* Standalone Pipes
* Standalone Directives
* `bootstrapApplication()`

## 15. Communication entre composants

* Parent → Enfant
* Enfant → Parent
* Frères via Service
* Signals
* RxJS

## 16. Performance

* Lazy Loading
* Preloading
* TrackBy
* Virtual Scrolling
* Optimisation des listes
* Code Splitting

## 17. Sécurité

* Sanitization
* XSS
* CSRF
* Authentification
* Autorisation

## 18. Tests

* Unit Tests
* Tests de composants
* Tests de services
* Jasmine
* Karma
* Jest

## 19. Déploiement

* Build de production
* Variables d'environnement
* Optimisation
* Docker
* CI/CD

## 20. Bonnes pratiques

* Architecture par fonctionnalités (feature-based)
* Séparation des responsabilités
* Smart vs Presentational Components
* Typage fort avec TypeScript
* Organisation des dossiers
* Convention de nommage

---

### Ordre d'apprentissage recommandé

1. ✅ TypeScript
2. ✅ Composants
3. ✅ Templates et Data Binding
4. ✅ Directives
5. ✅ Pipes
6. ✅ Services et Dependency Injection
7. ✅ Routing
8. ✅ Formulaires réactifs
9. ✅ HttpClient
10. ✅ RxJS
11. ✅ Signals
12. ✅ Standalone Components
13. ✅ Change Detection
14. ✅ Interceptors
15. ✅ Guards
16. ✅ Lazy Loading
17. ✅ Gestion de l'état (BehaviorSubject → Signals → NgRx)
18. ✅ Tests
19. ✅ Optimisation des performances
20. ✅ Déploiement

Pour un développeur Angular en 2026, les compétences les plus recherchées sont généralement : **TypeScript**, **RxJS**, **Signals**, **Standalone Components**, **Reactive Forms**, **HttpClient**, **Routing**, **Dependency Injection**, **Interceptors**, **Guards**, **Change Detection (OnPush)** et les notions d'optimisation des performances. Maîtriser ces sujets permet de couvrir l'essentiel des besoins en entreprise.
