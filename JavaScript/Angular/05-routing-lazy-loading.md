Le **lazy loading** en Angular consiste à **charger certains modules de l'application uniquement lorsqu'ils sont nécessaires**, plutôt que de les inclure dans le bundle principal téléchargé au démarrage.

L'objectif est d'améliorer :

* le temps de chargement initial ;
* les performances perçues ;
* la taille du bundle principal.

## Sans lazy loading

Imaginons une application avec :

* Accueil
* Profil
* Administration
* Rapports

Au démarrage, Angular télécharge tout :

```text
main.js
├── HomeModule
├── ProfileModule
├── AdminModule
└── ReportsModule
```

Même si l'utilisateur ne visite jamais la partie Administration, son code est déjà téléchargé.

---

## Avec lazy loading

Angular charge uniquement ce qui est nécessaire au départ :

```text
main.js
├── HomeModule
└── AppModule
```

Puis :

```text
/admin  → téléchargement de admin.module
/reports → téléchargement de reports.module
```

Chaque module est téléchargé à la demande.

---

## Mise en place moderne (Angular 17+)

Avec les routes standalone :

```typescript
export const routes: Routes = [
  {
    path: 'admin',
    loadComponent: () =>
      import('./admin/admin.component')
        .then(m => m.AdminComponent)
  }
];
```

Quand l'utilisateur accède à `/admin`, Angular :

1. télécharge le fichier JavaScript correspondant ;
2. charge le composant ;
3. affiche la page.

---

## Lazy loading d'un ensemble de routes

Pour charger plusieurs pages :

```typescript
export const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () =>
      import('./admin/admin.routes')
        .then(m => m.ADMIN_ROUTES)
  }
];
```

Fichier `admin.routes.ts` :

```typescript
export const ADMIN_ROUTES: Routes = [
  {
    path: '',
    component: AdminDashboardComponent
  },
  {
    path: 'users',
    component: UsersComponent
  }
];
```

Le bloc complet "admin" est téléchargé seulement lorsqu'un utilisateur visite `/admin`.

---

## Ancienne approche (NgModules)

Avant les composants standalone :

```typescript
const routes: Routes = [
  {
    path: 'admin',
    loadChildren: () =>
      import('./admin/admin.module')
        .then(m => m.AdminModule)
  }
];
```

Et dans `admin-routing.module.ts` :

```typescript
const routes: Routes = [
  {
    path: '',
    component: AdminComponent
  }
];
```

Cette approche reste supportée.

---

## Comment vérifier que ça fonctionne ?

Dans les outils de développement du navigateur :

1. Ouvrir l'onglet **Network**.
2. Charger l'application.
3. Observer les fichiers JavaScript téléchargés.
4. Naviguer vers `/admin`.

Vous verrez alors apparaître un nouveau fichier JavaScript (un **chunk**) chargé dynamiquement.

---

## Lazy loading vs Eager loading

| Eager Loading                  | Lazy Loading                                     |
| ------------------------------ | ------------------------------------------------ |
| Tout est chargé au démarrage   | Chargé à la demande                              |
| Démarrage plus lent            | Démarrage plus rapide                            |
| Navigation instantanée ensuite | Premier accès à une section légèrement plus lent |
| Simple à mettre en œuvre       | Nécessite un découpage en routes/modules         |

---

## Préchargement (Preloading)

Pour éviter le délai lors de la première navigation vers une route lazy-loaded, Angular peut les précharger en arrière-plan :

```typescript
provideRouter(
  routes,
  withPreloading(PreloadAllModules)
);
```

Comportement :

1. L'application démarre rapidement.
2. Angular affiche l'accueil.
3. En arrière-plan, il télécharge progressivement les modules lazy-loaded.

C'est souvent un bon compromis entre performance initiale et fluidité de navigation.

---

### Résumé

Le lazy loading en Angular repose principalement sur :

```typescript
loadComponent(...)
```

ou

```typescript
loadChildren(...)
```

Angular crée alors des **chunks JavaScript séparés** qui ne sont téléchargés que lorsque l'utilisateur accède à la route concernée, ce qui réduit le temps de chargement initial de l'application.
