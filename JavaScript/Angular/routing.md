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
