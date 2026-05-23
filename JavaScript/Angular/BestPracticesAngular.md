# ✅ Best Practices Angular (2026)

## 🧱 Architecture
- Architecture **feature-based** (par domaine métier, pas technique)
- `core/` → services globaux, interceptors, guards
- `shared/` → composants/pipes/directives réutilisables
- `features/` → modules métier
- Respecter le **Single Responsibility Principle**
- Éviter les dépendances circulaires

## 📦 Organisation du code
- 1 composant = 1 dossier
- Nommage clair :
  - `*.component.ts`
  - `*.service.ts`
  - `*.model.ts`
  - `*.enum.ts`

## ⚙️ Composants
- Utiliser `ChangeDetectionStrategy.OnPush`
- Favoriser les composants **présentationnels (dumb)**
- Passer les données via `@Input()` / `@Output()`
- Éviter la logique métier dans les composants
- Limiter la taille (<300 lignes)

## 🔁 RxJS & gestion des flux
- Utiliser les **Observables partout**
- Préférer `async pipe` au `subscribe()`
- Gérer les unsubscribe (`takeUntil`, `destroyRef`)
- Éviter les nested subscriptions

## 🧠 State Management
- Simple → services + RxJS
- Complexe → NgRx / Signals
- Ne jamais stocker l’état dans les composants

## 🌐 HTTP & API
- Centraliser les appels API dans des services
- Utiliser des **HttpInterceptors** (auth, erreurs)
- Typage strict des réponses (interfaces)

## 🛡️ Sécurité
- Utiliser des **Guards** (`CanActivate`)
- Gérer les tokens via interceptor
- Éviter `localStorage` pour données sensibles

## 🚀 Performance
- Lazy loading des modules
- `trackBy` dans `*ngFor`
- Éviter les calculs lourds dans templates
- Utiliser `OnPush`

## 🎯 Forms
- Préférer **Reactive Forms**
- Validation côté client + serveur
- Validators custom centralisés

## 🧪 Tests
- Tester services et logique métier
- Mock des dépendances
- Utiliser Jest ou Jasmine

## 🎨 UI / UX
- Pas de logique complexe dans HTML
- Utiliser un design system (Angular Material…)
- Séparer UI et logique

## 🧰 Qualité de code
- ESLint + Prettier
- Typage strict (`strict: true`)
- Alias imports (`@app`, `@shared`)

## 🔄 CI/CD
- Build prod optimisé (`ng build --prod`)
- Lint + tests dans pipeline
- Versioning propre (Git flow ou trunk-based)

## 📚 Bonnes habitudes
- Documenter le code complexe
- Refactor régulier
- Supprimer le code mort
- Faire des code reviews

## 🧠 Règle d’or
> Composants simples + logique dans services + RxJS propre = app scalable