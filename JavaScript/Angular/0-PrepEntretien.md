Pour un entretien technique de développeur Angular senior avec 8 ans d’expérience, tu dois maîtriser le fonctionnement interne d’Angular, RxJS, la gestion d’état, les performances, la sécurité et l’architecture d’une application frontend.

Voici une checklist priorisée pour réviser efficacement, avec les questions classiques qu’un recruteur technique peut te poser.

# 1. Angular — fondamentaux indispensables

## Checklist Angular

0/20

### Composants et templates

Architecture d’un composant : @Component, template, styles

Cycle de vie : ngOnInit, ngOnChanges, ngOnDestroy, ngAfterViewInit

Standalone components vs NgModules

Data binding : interpolation, property binding, event binding

@Input, @Output, modèles de communication parent-enfant

Nouvelle syntaxe de contrôle : @if, @for, @switch

Directives structurelles et attributaires

Pipes standards et pipes personnalisés

### Services et injection de dépendances

@Injectable et injection par constructeur ou inject()

Hiérarchie des injecteurs et portée des providers

providedIn: 'root' vs providers au niveau du composant

Services singleton et partage d’état

Injection de tokens avec InjectionToken

Fonctionnement des providers et des dépendances

### Réactivité et formulaires

Signals : signal, computed, effect

Différences entre Signals et RxJS

Reactive Forms vs Template-driven Forms

FormGroup, FormControl, FormArray

Validateurs synchrones et asynchrones

Formulaires dynamiques et validation personnalisée

Réinitialiser la checklist

# 2. RxJS — un sujet majeur en entretien

Tu dois être capable d'expliquer ce qu'est un `Observable`, comment il fonctionne et comment composer des flux asynchrones.

- `Observable` vs `Promise`.
- `subscribe()` et désabonnement.
- `Subject`, `BehaviorSubject`, `ReplaySubject`.
- `map`, `filter`, `tap`, `catchError`, `finalize`.
- `switchMap` : abandonner le flux précédent.
- `mergeMap` : exécuter plusieurs traitements concurrents.
- `concatMap` : traiter les éléments séquentiellement.
- `exhaustMap` : ignorer les nouvelles émissions pendant un traitement en cours.
- `combineLatest`, `forkJoin`, `merge`, `withLatestFrom`.
- `debounceTime`, `distinctUntilChanged`.
- `shareReplay` et les risques de cache ou de rétention mémoire.
- Gestion des erreurs et des abonnements dans les composants.

Question classique : quelle différence entre `switchMap`, `mergeMap`, `concatMap` et `exhaustMap` ?

| Opérateur    | Comportement                                             | Exemple                        |
| ------------ | -------------------------------------------------------- | ------------------------------ |
| `switchMap`  | Remplace le flux précédent                               | Recherche dynamique            |
| `mergeMap`   | Autorise la concurrence                                  | Plusieurs appels indépendants  |
| `concatMap`  | Traite dans l'ordre                                      | Envoi séquentiel               |
| `exhaustMap` | Ignore les nouveaux déclenchements pendant le traitement | Éviter les doubles soumissions |

Exemple typique :

```
this.searchControl.valueChanges.pipe(
  debounceTime(300),
  distinctUntilChanged(),
  switchMap(query => this.api.search(query)),
  catchError(() => of([]))
).subscribe(results => {
  this.results = results;
});
```

En pratique, pense aussi à `AsyncPipe` ou `takeUntilDestroyed()` pour gérer proprement les abonnements.

# 3. Change Detection et performances

C'est un sujet particulièrement important pour un profil senior.

- Fonctionnement de la détection des changements.
- `ChangeDetectionStrategy.OnPush`.
- Différence entre changement de référence et mutation d'objet.
- Signals et propagation des changements.
- `track` dans `@for` et suivi de l'identité des éléments.
- Lazy loading des routes et des fonctionnalités.
- Chargement différé des images et des composants.
- Éviter les appels coûteux dans les templates.
- `computed()` vs `effect()`.
- Profilage avec Angular DevTools.
- Zoneless Angular et conséquences sur la détection des changements.

Exemple :

```
@Component({
  selector: 'app-user-list',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    @for (user of users; track user.id) {
      <p>{{ user.name }}</p>
    }
  `
})
export class UserListComponent {
  users: User[] = [];
}
```

Question d'entretien : pourquoi modifier directement une propriété d'un objet peut-il ne pas déclencher la mise à jour attendue dans un composant `OnPush` ?

Il faut notamment comprendre les références d'objets, les événements, les observables consommés par `AsyncPipe` et les Signals.

# 4. Routing et navigation

- Configuration des routes.
- Routes enfants et layouts.
- Lazy loading avec `loadComponent` et `loadChildren`.
- Route guards : `CanActivate`, `CanActivateChild`, `CanDeactivate`.
- Résolveurs de données : `ResolveFn`.
- Paramètres de route vs query parameters.
- Navigation programmatique avec `Router`.
- Routes de secours et page 404.
- Stratégies de préchargement.

Question classique : comment protéger une route Angular ?

```
export const authGuard: CanActivateFn = () => {
  const auth = inject(AuthService);
  const router = inject(Router);

  return auth.isAuthenticated()
    ? true
    : router.createUrlTree(['/login']);
};
```

Attention : un guard Angular ne sécurise pas à lui seul les données. Le backend doit contrôler les autorisations à chaque requête.

# 5. HttpClient et communication avec le backend

- `HttpClient` et typage des réponses.
- Intercepteurs fonctionnels avec `HttpInterceptorFn`.
- Ajout d'un token d'accès.
- Gestion centralisée des erreurs HTTP.
- Retry avec conditions appropriées.
- Timeout et annulation des requêtes.
- Pagination, filtres et paramètres de requête.
- Requêtes parallèles avec RxJS.
- Upload de fichiers et suivi de progression.
- Configuration des environnements.
- CORS : rôle du navigateur et du backend.

Exemple d'intercepteur :

```
export const authInterceptor: HttpInterceptorFn =
  (req, next) => {
    const token = inject(AuthService).getToken();

    if (!token) {
      return next(req);
    }

    return next(req.clone({
      setHeaders: {
        Authorization: `Bearer ${token}`
      }
    }));
  };
```

Révise également la différence entre authentification et autorisation, le stockage des tokens et les risques XSS.

# 6. Architecture Angular senior

- Architecture par fonctionnalités (feature-based).
- Séparation composants de présentation et composants conteneurs.
- Services métier vs services d'accès aux données.
- Gestion de l'état : Signals, services, NgRx selon les besoins.
- Composants réutilisables et bibliothèques partagées.
- Design system et composants UI.
- Typage strict TypeScript.
- Gestion des erreurs globale.
- Patterns de conception utiles : facade, adapter, strategy.
- Monorepo et bibliothèques partagées, par exemple avec Nx.
- SSR, hydratation et rendu côté serveur.

Question senior : comment organiser une application Angular avec plusieurs domaines fonctionnels, des appels REST communs et des règles d'accès différentes ?

Tu dois savoir justifier tes choix, et surtout éviter le surdécoupage en services ou l'introduction systématique d'une bibliothèque de gestion d'état.

# 7. TypeScript — les notions à connaître

- `interface` vs `type`.
- `unknown` vs `any`.
- Union et intersection de types.
- Génériques.
- Types utilitaires : `Pick`, `Omit`, `Partial`, `Record`, `Readonly`.
- Narrowing, type guards et discriminated unions.
- `readonly`, optional chaining et nullish coalescing.
- `enum` vs union de chaînes littérales.
- `Promise`, `async/await`.
- Modules ES et imports.
- Décorateurs TypeScript et métadonnées Angular.

# 8. Sécurité frontend

- XSS et échappement des données.
- Sanitization Angular et risques de `bypassSecurityTrust...`.
- Authentification et autorisation.
- JWT et expiration des tokens.
- Guards et contrôle côté backend.
- CSRF, notamment avec les cookies d'authentification.
- CORS et ses limites.
- Protection des données sensibles dans les logs.
- Gestion des secrets : ne jamais mettre un secret serveur dans le bundle frontend.

# 9. Tests et qualité

- Tests unitaires des composants et services.
- Tests des observables et des Signals.
- Tests des formulaires réactifs.
- Tests HTTP avec `HttpTestingController`.
- Tests des guards et des intercepteurs.
- Tests de composants avec TestBed.
- Tests end-to-end avec Playwright ou Cypress.
- Doubles de test, mocks et spies.
- Accessibilité : navigation clavier, labels et attributs ARIA.
- Lint, formatage et intégration continue.

# 10. Questions techniques à te poser à voix haute

Essaie de répondre sans aide, puis ouvre la réponse pour vérifier.

1\. Quelle différence entre un Observable et une Promise ?2. Quelle différence entre un Subject et un BehaviorSubject ?3. Pourquoi utiliser OnPush ?4. Comment éviter les fuites mémoire ?5. Comment éviter les appels API à chaque frappe ?6. Comment gérer l'état d'une application ?7. Comment améliorer le temps de chargement initial ?8. Comment sécuriser une route ?

# 11. Plan de révision express

## 20 min

Angular Core et TypeScript

Composants, cycle de vie, DI, formulaires et typage.

## 20 min

RxJS et réactivité

switchMap, combineLatest, erreurs, Signals et désabonnements.

## 15 min

Routing, HTTP et sécurité

Guards, intercepteurs, JWT, erreurs HTTP et contrôle backend.

## 15 min

Performance, architecture et tests

OnPush, lazy loading, architecture par fonctionnalités, tests HTTP et composants.

Mes 10 priorités pour un entretien Angular senior :

1. Cycle de vie des composants et injection de dépendances.
2. RxJS : `switchMap`, `mergeMap`, `concatMap`, `exhaustMap`.
3. Signals vs Observables.
4. Change Detection et `OnPush`.
5. Routing et guards.
6. HttpClient et intercepteurs.
7. Reactive Forms et validateurs.
8. Architecture et gestion d'état.
9. Sécurité frontend.
10. Tests unitaires et performances.

Le meilleur exercice final consiste à expliquer l'architecture d'une application Angular connectée à une API Spring Boot : connexion utilisateur, routes protégées, formulaires, gestion des erreurs, état de l'application et tests. C'est un excellent moyen de montrer que tu maîtrises autant les concepts que leur application concrète.
