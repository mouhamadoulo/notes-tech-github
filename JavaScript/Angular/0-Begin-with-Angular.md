# 🔷 <ins>Qu’est-ce qu’Angular ?</ins>

Angular est un framework front-end open source développé par Google, basé sur **TypeScript**.

Il permet de créer des applications web dynamiques, robustes et maintenables, notamment des **Single Page Applications (SPA)**.

Depuis Angular 17, le framework a fortement évolué et **Angular 22 utilise par défaut les Standalone Components**, les **Signals** et les **nouveaux templates** (`@if`, `@for`, `@switch`).

---

# 📦 Installation

## Prérequis

* Installer **Node.js** (version LTS compatible avec Angular 22)
* Node installe automatiquement **npm**

Installation du CLI Angular :

```bash
npm install -g @angular/cli
```

Vérifier l'installation :

```bash
ng version
```

---

# 🧱 Concepts fondamentaux

## 1. Architecture par composants

Une application Angular est composée de composants.

Chaque composant possède :

* un template HTML
* une logique TypeScript
* un style CSS/SCSS

Exemple Standalone :

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-example',
  standalone: true,
  templateUrl: './example.component.html',
  styleUrl: './example.component.scss'
})
export class ExampleComponent {}
```

> Depuis Angular 17+, les composants standalone sont la méthode recommandée.

---

## 2. Standalone Components (à la place des NgModules)

Les **NgModules ne sont plus nécessaires** dans la majorité des projets.

Les dépendances sont directement importées dans le composant.

```ts
@Component({
  standalone: true,
  imports: [CommonModule],
  template: `...`
})
export class HomeComponent {}
```

Le point d'entrée de l'application utilise :

```ts
bootstrapApplication(AppComponent);
```

au lieu de :

```ts
platformBrowserDynamic().bootstrapModule(AppModule);
```

Les NgModules restent disponibles pour les anciens projets.

---

## 3. Data Binding

Angular conserve les quatre types de liaison.

| Type             | Syntaxe       | Description              |
| ---------------- | ------------- | ------------------------ |
| Interpolation    | `{{ value }}` | Afficher une valeur      |
| Property Binding | `[property]`  | Du TS vers le HTML       |
| Event Binding    | `(click)`     | Événement utilisateur    |
| Two-way Binding  | `[(ngModel)]` | Liaison bidirectionnelle |

Exemple :

```html
<input [(ngModel)]="name">

<h2>{{ name }}</h2>
```

---

## 4. Control Flow (nouvelle syntaxe)

Les directives structurelles classiques existent toujours mais sont remplacées par une nouvelle syntaxe.

### Condition

```html
@if (isLogged()) {
    <p>Bienvenue</p>
}
```

### Boucle

```html
@for (user of users(); track user.id) {

    <p>{{ user.name }}</p>

}
```

### Switch

```html
@switch (status()) {

    @case ('loading') {
        Chargement...
    }

    @case ('success') {
        Succès
    }

    @default {
        Erreur
    }

}
```

Cette syntaxe est désormais recommandée.

---

## 5. Signals

Les **Signals** remplacent progressivement une partie de l'utilisation de RxJS pour l'état local.

Créer un signal :

```ts
import { signal } from '@angular/core';

count = signal(0);
```

Lire sa valeur :

```ts
count()
```

Modifier :

```ts
count.set(10);

count.update(v => v + 1);
```

Valeur calculée :

```ts
import { computed } from '@angular/core';

double = computed(() => count() * 2);
```

---

## 6. Injection de dépendances

On peut toujours utiliser le constructeur :

```ts
constructor(private service: UserService){}
```

Mais la méthode moderne est :

```ts
import { inject } from '@angular/core';

userService = inject(UserService);
```

---

## 7. Routing

Le routing fonctionne avec des composants standalone.

```ts
export const routes: Routes = [
  {
    path: '',
    component: HomeComponent
  }
];
```

ou avec le Lazy Loading :

```ts
{
    path: 'admin',
    loadComponent: () =>
        import('./admin.component')
            .then(c => c.AdminComponent)
}
```

---

## 8. Formulaires

Deux approches existent toujours :

* Template Forms
* Reactive Forms (recommandées)

```ts
form = new FormGroup({
    name: new FormControl('')
});
```

---

## 9. HTTP

Les requêtes HTTP utilisent toujours `HttpClient`.

```ts
http = inject(HttpClient);

this.http.get('/api/users')
    .subscribe(users => console.log(users));
```

---

## 10. RxJS

RxJS reste utilisé pour :

* HTTP
* WebSocket
* événements
* streams de données

Mais les **Signals** remplacent souvent RxJS pour la gestion de l'état local.

---

# 🛠 Angular CLI

## Création d'un projet

```bash
ng new mon-projet
```

Angular crée maintenant automatiquement un projet Standalone.

Exemple :

```bash
ng new blog --routing --style=scss
```

---

## Lancer le projet

```bash
cd mon-projet

ng serve
```

ou

```bash
ng serve --open
```

Application disponible sur

```
http://localhost:4200
```

---

# ⚙ Options de `ng new`

| Option         | Description            |
| -------------- | ---------------------- |
| --routing      | Ajoute le routing      |
| --style=scss   | Utilise SCSS           |
| --skip-tests   | Ne crée pas les tests  |
| --strict       | Mode strict TypeScript |
| --prefix=app   | Préfixe des composants |
| --skip-install | N'installe pas npm     |

---

# ⚙ Génération

Créer un composant

```bash
ng g component home
```

Créer un composant standalone

```bash
ng g component home --standalone
```

Créer un service

```bash
ng g service services/user
```

Créer une directive

```bash
ng g directive directives/highlight
```

Créer un pipe

```bash
ng g pipe pipes/date
```

Créer une garde

```bash
ng g guard guards/auth
```

Créer une interface

```bash
ng g interface models/user
```

---

# 🚀 Développement

Lancer le serveur

```bash
ng serve
```

Ouvrir automatiquement

```bash
ng serve --open
```

Changer le port

```bash
ng serve --port 4300
```

HTTPS

```bash
ng serve --ssl
```

---

# 🏗 Build

Développement

```bash
ng build
```

Production

```bash
ng build --configuration production
```

Rebuild automatique

```bash
ng build --watch
```

---

# 🧪 Tests

Tests unitaires

```bash
ng test
```

Angular ne génère plus automatiquement de configuration **end-to-end (E2E)**. Si vous souhaitez des tests E2E, il est recommandé d'utiliser des outils comme **Playwright** ou **Cypress**.

---

# 🔄 Mise à jour

Mettre Angular à jour

```bash
ng update
```

Mettre également le CLI

```bash
ng update @angular/core @angular/cli
```

---

# 🔍 Analyse

Aide

```bash
ng help
```

Aide d'une commande

```bash
ng generate --help
```

Version

```bash
ng version
```

---

# 📁 Arborescence d'un projet Angular 22

```
src/

 ├── app/
 │    ├── app.component.ts
 │    ├── app.component.html
 │    ├── app.config.ts
 │    ├── app.routes.ts
 │
 ├── assets/
 ├── styles.scss
 ├── main.ts
```

Les fichiers **app.config.ts** et **app.routes.ts** remplacent en grande partie l'ancien `AppModule`.

---

# ⭐ Nouveautés majeures d'Angular 22

* ✅ Standalone Components par défaut
* ✅ Plus besoin de NgModules dans la majorité des projets
* ✅ Signals pour la gestion de l'état
* ✅ `@if`, `@for`, `@switch` à la place de `*ngIf`, `*ngFor`
* ✅ `inject()` à la place du constructeur lorsque pertinent
* ✅ Lazy Loading simplifié avec `loadComponent`
* ✅ Optimisations de performances et d'hydratation (SSR)

---

# ✅ Commandes essentielles

| Action                | Commande                              |
| --------------------- | ------------------------------------- |
| Créer un projet       | `ng new mon-projet`                   |
| Lancer le serveur     | `ng serve`                            |
| Créer un composant    | `ng g c home`                         |
| Créer un service      | `ng g s user`                         |
| Build production      | `ng build --configuration production` |
| Mettre Angular à jour | `ng update`                           |

---

# ✅ Avantages

* Architecture robuste et scalable
* TypeScript fortement typé
* Standalone Components plus simples
* Signals pour une meilleure réactivité
* Excellent support du SSR et de l'hydratation
* Maintenu activement par Google
* Écosystème complet (CLI, Router, Forms, HttpClient...)

---

# ⚠ Inconvénients

* Courbe d'apprentissage plus importante que des bibliothèques comme React ou Vue
* Taille du framework plus conséquente
* Les nombreuses nouveautés (Signals, Control Flow, Standalone) nécessitent une mise à jour des pratiques pour les développeurs venant d'anciennes versions

---

# 📌 Cas d'usage

* Applications d'entreprise
* Dashboards complexes
* Applications SaaS
* ERP / CRM
* Progressive Web Apps (PWA)
* Applications avec SSR
* Projets nécessitant une architecture solide et évolutive
