# ✅ Checklist des bonnes pratiques Angular 21

> Cette checklist est orientée Angular 21 et privilégie les approches modernes : Standalone Components, Signals, contrôle de flux natif, rendu différé et APIs réactives récentes.

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
- [ ] Respecter les conventions de nommage Angular.

## Standalone First

- [ ] Utiliser exclusivement des composants standalone.
- [ ] Éviter la création de nouveaux `NgModule`.
- [ ] Importer explicitement les dépendances dans chaque composant.
- [ ] Utiliser les routes standalone.

---

# 🧩 Composants

## Conception

- [ ] Respecter le principe de responsabilité unique.
- [ ] Limiter la logique métier dans les composants.
- [ ] Déléguer la logique métier aux services ou stores.
- [ ] Garder les composants petits et réutilisables.
- [ ] Privilégier la composition plutôt que l'héritage.

## Change Detection

- [ ] Utiliser systématiquement `ChangeDetectionStrategy.OnPush`.
- [ ] Éviter les mutations d'objets.
- [ ] Utiliser des structures de données immuables.
- [ ] Favoriser les Signals pour la gestion d'état local.

## Cycle de vie

- [ ] Utiliser `DestroyRef`.
- [ ] Utiliser `takeUntilDestroyed()`.
- [ ] Éviter les nettoyages manuels dans `ngOnDestroy()` lorsque possible.
- [ ] Supprimer les hooks inutiles.

---

# ⚡ Signals

## Gestion d'état locale

- [ ] Utiliser `signal()` pour les états locaux.
- [ ] Utiliser `computed()` pour les valeurs dérivées.
- [ ] Utiliser `effect()` uniquement pour les effets de bord.
- [ ] Garder les `effect()` simples et prédictibles.

## Bonnes pratiques

- [ ] Éviter les chaînes complexes d'effets.
- [ ] Préférer `computed()` à un `effect()` lorsque possible.
- [ ] Éviter les mises à jour circulaires entre signals.
- [ ] Centraliser les états partagés dans des stores dédiés.

---

# 🔄 RxJS

## Utilisation

- [ ] Utiliser RxJS principalement pour :
  - [ ] HTTP
  - [ ] WebSocket
  - [ ] événements asynchrones
  - [ ] flux complexes

## Bonnes pratiques

- [ ] Éviter les subscriptions imbriquées.
- [ ] Utiliser :
  - [ ] `switchMap`
  - [ ] `concatMap`
  - [ ] `mergeMap`
  - [ ] `exhaustMap`
  selon le besoin métier.
- [ ] Utiliser `catchError`.
- [ ] Utiliser `shareReplay()` lorsque pertinent.
- [ ] Éviter les abonnements manuels si un signal ou un pipe async suffit.

## Interop Signals/RxJS

- [ ] Utiliser `toSignal()` pour exposer un Observable au template.
- [ ] Utiliser `toObservable()` lorsque nécessaire.
- [ ] Éviter les conversions inutiles entre Signals et Observables.

---

# 🎨 Templates

## Contrôle de flux moderne

- [ ] Utiliser `@if`.
- [ ] Utiliser `@for`.
- [ ] Utiliser `@switch`.
- [ ] Éviter les anciennes syntaxes `*ngIf` et `*ngFor` dans les nouveaux développements.

## Boucles

- [ ] Toujours utiliser `track`.
- [ ] Utiliser un identifiant métier stable.

### Exemple

```html
@for (user of users(); track user.id) {
  <app-user-card [user]="user" />
}
```

## Lisibilité

* [ ] Éviter les expressions complexes.
* [ ] Éviter les appels de méthodes coûteux.
* [ ] Préférer les `computed()` pour préparer les données.
* [ ] Limiter la profondeur d'imbrication.

---

# 🚀 Performance

## Rendu

* [ ] Utiliser `@defer` pour les composants lourds.
* [ ] Charger les écrans secondaires à la demande.
* [ ] Différer les bibliothèques coûteuses.

### Exemple

```html
@defer {
  <app-dashboard-chart />
}
@placeholder {
  <app-loader />
}
```

## Lazy Loading

* [ ] Utiliser le lazy loading sur toutes les features importantes.
* [ ] Fractionner les bundles volumineux.
* [ ] Vérifier régulièrement la taille du bundle.

## Détection de changements

* [ ] Utiliser OnPush partout.
* [ ] Utiliser des données immuables.
* [ ] Éviter les rerenders inutiles.

---

# 🌐 HTTP

## Organisation

* [ ] Centraliser les appels API dans des services.
* [ ] Utiliser un service par domaine métier.
* [ ] Typage complet des DTO.

## Interceptors

* [ ] Authentification.
* [ ] Refresh Token.
* [ ] Logging.
* [ ] Gestion des erreurs.
* [ ] Headers communs.

## Gestion des erreurs

* [ ] Centraliser les erreurs globales.
* [ ] Fournir des messages utilisateur clairs.
* [ ] Journaliser les erreurs techniques.

---

# 🛣️ Routing

## Configuration

* [ ] Utiliser les routes standalone.
* [ ] Utiliser `loadComponent`.
* [ ] Utiliser `loadChildren` pour les features complexes.

## Sécurité

* [ ] Utiliser des guards fonctionnels.
* [ ] Utiliser des resolvers lorsque nécessaire.
* [ ] Vérifier les autorisations avant chargement.

### Exemple

```typescript
export const authGuard: CanActivateFn = () => {
  return inject(AuthService).isAuthenticated();
};
```

---

# 📝 Formulaires

## Reactive Forms

* [ ] Utiliser Reactive Forms.
* [ ] Utiliser les formulaires typés.
* [ ] Éviter les formulaires non typés.

### Exemple

```typescript
const form = new FormGroup({
  email: new FormControl<string>('', { nonNullable: true }),
});
```

## Validation

* [ ] Créer des validateurs réutilisables.
* [ ] Centraliser les messages d'erreur.
* [ ] Éviter la duplication de logique.

---

# 🛡️ Sécurité

## Frontend

* [ ] Ne jamais stocker de secrets.
* [ ] Ne jamais faire confiance aux données utilisateur.
* [ ] Utiliser les protections XSS natives d'Angular.
* [ ] Éviter l'utilisation de `innerHTML`.

## Authentification

* [ ] Utiliser des tokens courts.
* [ ] Implémenter un mécanisme de refresh.
* [ ] Gérer l'expiration de session.

---

# 📦 Gestion d'état

## État local

* [ ] Utiliser les Signals.

## État partagé

* [ ] Utiliser un Store basé sur Signals lorsque nécessaire.
* [ ] Éviter l'introduction d'une librairie de state management sans besoin réel.

## Avant d'ajouter un Store global

* [ ] Le besoin est-il réellement partagé ?
* [ ] Plusieurs écrans utilisent-ils cet état ?
* [ ] Les Signals locaux sont-ils insuffisants ?

---

# 🧪 Tests

## Unitaires

* [ ] Tester les services métier.
* [ ] Tester les guards.
* [ ] Tester les composants critiques.
* [ ] Tester les computed signals importants.

## Intégration

* [ ] Tester les flux utilisateurs principaux.
* [ ] Tester les interactions entre composants.

## End-to-End

* [ ] Couvrir les parcours critiques.
* [ ] Intégrer les tests dans la CI/CD.

---

# 📏 Qualité de code

## TypeScript

* [ ] Activer `strict`.
* [ ] Interdire `any`.
* [ ] Définir explicitement les types publics.
* [ ] Utiliser les utilitaires TypeScript (`Pick`, `Omit`, `Partial`, etc.).

## Linting

* [ ] Utiliser ESLint.
* [ ] Utiliser Prettier.
* [ ] Automatiser les vérifications dans la CI.

## Revues de code

* [ ] Vérifier les performances.
* [ ] Vérifier l'accessibilité.
* [ ] Vérifier la maintenabilité.
* [ ] Vérifier la couverture de tests.

---

# ♿ Accessibilité

* [ ] Utiliser les éléments HTML sémantiques.
* [ ] Vérifier la navigation clavier.
* [ ] Ajouter les attributs ARIA lorsque nécessaire.
* [ ] Tester le contraste des couleurs.
* [ ] Vérifier les lecteurs d'écran.

---

# 🚀 CI/CD

* [ ] Exécuter les tests automatiquement.
* [ ] Exécuter ESLint automatiquement.
* [ ] Refuser les merges en cas d'échec.
* [ ] Générer les builds de production automatiquement.
* [ ] Déployer automatiquement les environnements non productifs.

---

# 🔍 Revue finale avant Merge

* [ ] Le composant est standalone.
* [ ] OnPush est activé.
* [ ] Les données sont immuables.
* [ ] Les subscriptions sont nettoyées.
* [ ] Les templates utilisent `@if` / `@for`.
* [ ] Les boucles utilisent `track`.
* [ ] Le code est strictement typé.
* [ ] Les tests sont présents.
* [ ] Aucun `any` non justifié.
* [ ] Aucun code mort.
* [ ] Aucun secret exposé.
* [ ] La performance a été vérifiée.

---

# ⭐ Règle d'or Angular 21

* [ ] Signals pour l'état local.
* [ ] RxJS pour l'asynchrone.
* [ ] Standalone partout.
* [ ] OnPush partout.
* [ ] `@if`, `@for`, `@switch` partout.
* [ ] `@defer` dès qu'un composant est lourd.
* [ ] TypeScript strict sans compromis.
* [ ] Architecture métier avant architecture technique.


