# 🧭 Routing en Angular 18+

Le **routing dans Angular 18** est un système central qui permet de construire des **applications SPA (Single Page Applications)** avec navigation entre vues sans rechargement de page.

Ce cours couvre **tout ce qu’un développeur Angular doit maîtriser** : configuration, routes dynamiques, guards, resolvers, lazy loading, standalone APIs (Angular 18), patterns avancés et bonnes pratiques.

---

# 1. 📦 Mise en place du routing

## 1.1 Création d’un projet avec routing

```bash
ng new my-app --routing
```

Cela génère automatiquement :
- ```app.routes.ts``` (ou ```app-routing.module.ts``` selon config)
- ```RouterModule``` configuré

## 1.2 Structure moderne Angular 18 (standalone)

Angular 18 privilégie les **standalone components**.

**app.routes.ts**
```typescript
import { Routes } from '@angular/router';

export const routes: Routes = [
  { path: '', redirectTo: 'home', pathMatch: 'full' }
];
```

**main.ts**
```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { provideRouter } from '@angular/router';
import { AppComponent } from './app/app.component';
import { routes } from './app/app.routes';

bootstrapApplication(AppComponent, {
  providers: [provideRouter(routes)]
});
```

# 2. 🧱 Définir des routes simples

## 2.1 Exemple basique
```typescript
import { Routes } from '@angular/router';
import { HomeComponent } from './home/home.component';
import { AboutComponent } from './about/about.component';

export const routes: Routes = [
  { path: 'home', component: HomeComponent },
  { path: 'about', component: AboutComponent }
];
```

## 2.2 Navigation dans le template
```html
<a routerLink="/home">Home</a>
<a routerLink="/about">About</a>

<router-outlet></router-outlet>
```
👉 ```<router-outlet>``` est l’emplacement d’affichage dynamique des composants.

# 3. 🧭 Routes dynamiques

## 3.1 Paramètres d’URL
```typescript
{ path: 'user/:id', component: UserComponent }
```

**Exemple URL :**
```bash
/user/42
```

## 3.2 Lecture du paramètre
```typescript
import { ActivatedRoute } from '@angular/router';

export class UserComponent {
  constructor(private route: ActivatedRoute) {
    this.route.params.subscribe(params => {
      console.log(params['id']);
    });
  }
}
```

## 3.3 Snapshot (lecture simple)
```typescript
const id = this.route.snapshot.paramMap.get('id');
```

# 4. 🔁 Navigation programmatique
```typescript
import { Router } from '@angular/router';

constructor(private router: Router) {}

goToUser() {
  this.router.navigate(['/user', 42]);
}
```

# 5. 🧩 Routes enfants (nested routes)

## 5.1 Définition
```typescript
export const routes: Routes = [
  {
    path: 'dashboard',
    component: DashboardComponent,
    children: [
      { path: 'stats', component: StatsComponent },
      { path: 'settings', component: SettingsComponent }
    ]
  }
];
```

## 5.2 Dashboard template
```html
<h1>Dashboard</h1>

<nav>
  <a routerLink="stats">Stats</a>
  <a routerLink="settings">Settings</a>
</nav>

<router-outlet></router-outlet>
```

# 6. 🚀 Lazy Loading (chargement différé)
Le lazy loading améliore les performances en chargeant les modules à la demande.

## 6.1 Standalone lazy route (Angular 18 recommandé)
```typescript
export const routes: Routes = [
  {
    path: 'admin',
    loadComponent: () =>
      import('./admin/admin.component').then(m => m.AdminComponent)
  }
];
```

## 6.2 Lazy loading avec feature routes
```typescript
{
  path: 'admin',
  loadChildren: () =>
    import('./admin/admin.routes').then(m => m.ADMIN_ROUTES)
}
```
**admin.routes.ts**
```typescript
import { Routes } from '@angular/router';

export const ADMIN_ROUTES: Routes = [
  { path: '', component: AdminHomeComponent },
  { path: 'users', component: AdminUsersComponent }
];
```

# 7. 🛡️ Guards (protection des routes)

## 7.1 CanActivate
```typescript
import { CanActivateFn } from '@angular/router';

export const authGuard: CanActivateFn = (route, state) => {
  const isLoggedIn = !!localStorage.getItem('token');
  return isLoggedIn;
};
```

## 7.2 Application
```typescript
{
  path: 'admin',
  component: AdminComponent,
  canActivate: [authGuard]
}
```

## 7.3 CanMatch (remplace souvent CanLoad)
```typescript
export const adminMatchGuard: CanMatchFn = () => {
  return true;
};
```

# 8. 📦 Resolvers (pré-chargement de données)
Permet de charger les données AVANT l’affichage du composant.

## 8.1 Resolver
```typescript
import { ResolveFn } from '@angular/router';

export const userResolver: ResolveFn<any> = (route) => {
  const id = route.paramMap.get('id');
  return fetch(`/api/users/${id}`).then(r => r.json());
};
```

## 8.2 Utilisation
```typescript
{
  path: 'user/:id',
  component: UserComponent,
  resolve: { user: userResolver }
}
```

## 8.3 Récupération
```typescript
constructor(private route: ActivatedRoute) {
  this.route.data.subscribe(data => {
    console.log(data['user']);
  });
}
```

# 9. 🔄 Query Params

## 9.1 URL
```bash
/products?page=2&sort=asc
```

## 9.2 Lecture
```typescript
this.route.queryParams.subscribe(params => {
  console.log(params['page']);
});
```

## 9.3 Navigation avec query params
```typescript
this.router.navigate(['/products'], {
  queryParams: { page: 2, sort: 'asc' }
});
```

# 10. 🧭 Redirections
```typescript
{ path: '', redirectTo: 'home', pathMatch: 'full' }
```

# 11. ❌ Route wildcard (404)
```typescript
{ path: '**', component: NotFoundComponent }
```

# 12. 🧠 États avancés du Router

## 12.1 Navigation events
```typescript
this.router.events.subscribe(event => {
  console.log(event);
});
```

Types utiles :
- ```NavigationStart```
- ```NavigationEnd```
- ```NavigationCancel```

# 13. 🎯 Route data statique
```typescript
{
  path: 'about',
  component: AboutComponent,
  data: { title: 'À propos' }
}
```
Lecture :
```typescript
this.route.data.subscribe(data => {
  console.log(data['title']);
});
```

# 14. 🧱 Stratégies de routing avancées

## 14.1 Preloading strategy
```typescript
import { provideRouter, withPreloading, PreloadAllModules } from '@angular/router';

provideRouter(routes, withPreloading(PreloadAllModules))
```

## 14.2 Scroll restoration
```typescript
provideRouter(routes, withInMemoryScrolling({
  scrollPositionRestoration: 'enabled'
}));
```

# 15. 🧼 Bonnes pratiques

✔️ **Architecture recommandée**<br>
- 1 feature = 1 route lazy-loaded
- éviter les composants lourds dans app principal
- utiliser resolvers pour données critiques
- sécuriser avec guards
- préférer ```loadComponent``` en Angular 18
  
❌ **À éviter**<br>
- trop de logique dans les components route
- navigation hardcodée excessive
- absence de lazy loading
- surcharge du ```app.component```

# 16. 🧭 Schéma mental du Router Angular
```bash
URL → Router → Matching Route → Guards → Resolver → Component → View
```

# 17. 🚀 Exemple complet moderne Angular 18
```typescript
export const routes: Routes = [
  {
    path: '',
    loadComponent: () =>
      import('./pages/home/home.component').then(m => m.HomeComponent)
  },
  {
    path: 'user/:id',
    loadComponent: () =>
      import('./pages/user/user.component').then(m => m.UserComponent),
    canActivate: [authGuard],
    resolve: { user: userResolver }
  },
  {
    path: '**',
    loadComponent: () =>
      import('./pages/not-found/not-found.component').then(m => m.NotFoundComponent)
  }
];
```

# 🎯 Conclusion
Le routing Angular 18 repose sur :
- Standalone APIs
- Lazy loading systématique
- Guards fonctionnels (```CanActivateFn```)
- Resolvers modernes
- Router declaratif et typé

👉 Maîtriser le routing Angular, c’est maîtriser l’architecture entière de ton application SPA.
