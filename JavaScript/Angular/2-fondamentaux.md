# Les Fondamentaux d'Angular

# 1. Introduction à Angular

## Qu'est-ce qu'Angular ?

Angular est un framework frontend développé par Google permettant de créer des applications web modernes, dynamiques et maintenables.

Il est basé sur :

- TypeScript
- Une architecture orientée composants
- L'injection de dépendances
- Le data binding bidirectionnel
- Une structure modulaire

Angular est particulièrement adapté aux applications complexes de type SPA (**Single Page Application**).

---

# 2. Architecture Angular

Une application Angular est organisée autour de plusieurs briques fondamentales :

```text
Application
│
├── Composants
│
├── Services
│
├── Templates
│
├── Directives
│
├── Pipes
│
├── Routing
│
└── Modules (ou Standalone Components)
```

## Rôle des différents éléments

### Composants

Responsables de l'affichage et de la logique de présentation.

### Services

Responsables de la logique métier et des appels API.

### Templates

Définissent l'interface utilisateur.

### Directives

Permettent de modifier dynamiquement le DOM.

### Pipes

Permettent de transformer les données affichées.

### Routing

Gère la navigation entre les pages.

### Modules

Permettent de regrouper les fonctionnalités.

---

# 3. Les Modules Angular

## Pourquoi les modules existent ?

Historiquement Angular organisait l'application en modules.

Exemple :

```typescript
@NgModule({
  declarations: [
    AppComponent,
    UserComponent
  ],
  imports: [
    BrowserModule
  ],
  bootstrap: [
    AppComponent
  ]
})
export class AppModule {}
```

---

## Éléments principaux d'un module

### declarations

Liste des composants, directives et pipes.

```typescript
declarations: [
  UserComponent
]
```

### imports

Liste des modules utilisés.

```typescript
imports: [
  CommonModule,
  FormsModule
]
```

### providers

Liste des services disponibles.

```typescript
providers: [
  UserService
]
```

### bootstrap

Composant de démarrage.

```typescript
bootstrap: [AppComponent]
```

---

# Les Standalone Components (Angular moderne)

Depuis Angular 14+, les composants standalone remplacent progressivement les modules.

Exemple :

```typescript
@Component({
  selector: 'app-user',
  standalone: true,
  imports: [CommonModule],
  templateUrl: './user.component.html'
})
export class UserComponent {}
```

Avantages :

- Moins de code
- Architecture plus simple
- Chargement plus optimisé
- Plus facile à maintenir

Aujourd'hui, Angular recommande fortement l'approche standalone.

---

# 4. Les Composants (Components)

## Définition

Un composant est l'unité de base d'une application Angular.

Chaque composant possède :

- Une classe TypeScript
- Un template HTML
- Des styles CSS

---

## Exemple

### user.component.ts

```typescript
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html'
})
export class UserComponent {
  name = 'Jean';
}
```

### user.component.html

```html
<h1>{{ name }}</h1>
```

Résultat :

```html
<h1>Jean</h1>
```

---

## Structure d'un composant

```typescript
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html',
  styleUrls: ['./user.component.css']
})
```

### selector

Nom de la balise HTML.

```html
<app-user></app-user>
```

### templateUrl

Fichier HTML associé.

### styleUrls

Fichiers CSS associés.

---

# 5. Les Templates HTML

Le template représente la vue du composant.

Exemple :

```html
<h1>Bienvenue</h1>

<p>Utilisateur connecté</p>

<button>Valider</button>
```

Angular enrichit le HTML grâce à :

- Data Binding
- Directives
- Pipes
- Événements

---

# 6. Data Binding

Le Data Binding permet de relier les données TypeScript au template HTML.

Il existe 4 types principaux.

---

# 6.1 Interpolation {{ }}

## Principe

Afficher une donnée dans le HTML.

Composant :

```typescript
name = 'Alice';
```

Template :

```html
<p>{{ name }}</p>
```

Résultat :

```html
<p>Alice</p>
```

---

## Expressions possibles

```html
<p>{{ 2 + 3 }}</p>

<p>{{ name.toUpperCase() }}</p>

<p>{{ isAdmin ? 'Oui' : 'Non' }}</p>
```

---

# 6.2 Property Binding [property]

## Principe

Envoyer une valeur TypeScript vers une propriété HTML.

Composant :

```typescript
imageUrl = 'photo.jpg';
```

Template :

```html
<img [src]="imageUrl">
```

Angular devient :

```html
<img src="photo.jpg">
```

---

## Autres exemples

```html
<button [disabled]="isLoading">
  Enregistrer
</button>
```

```typescript
isLoading = true;
```

Résultat :

Bouton désactivé.

---

# 6.3 Event Binding (click)

## Principe

Réagir à un événement utilisateur.

Composant :

```typescript
increment() {
  console.log('Click');
}
```

Template :

```html
<button (click)="increment()">
  Ajouter
</button>
```

Lorsque l'utilisateur clique :

```typescript
increment()
```

est exécutée.

---

## Exemple compteur

```typescript
count = 0;

increment() {
  this.count++;
}
```

```html
<p>{{ count }}</p>

<button (click)="increment()">
  +
</button>
```

---

# 6.4 Two-Way Binding [(ngModel)]

## Principe

Synchronisation dans les deux sens :

```text
TypeScript <--> HTML
```

---

### Import nécessaire

```typescript
import { FormsModule } from '@angular/forms';
```

---

### Exemple

Composant :

```typescript
name = '';
```

Template :

```html
<input [(ngModel)]="name">

<p>{{ name }}</p>
```

Lorsque l'utilisateur tape :

```text
Bonjour
```

Le composant reçoit immédiatement :

```typescript
name = 'Bonjour'
```

---

## Décomposition

```html
[(ngModel)]
```

équivaut à :

```html
[value]="name"
(input)="name = $event.target.value"
```

C'est donc :

```text
Property Binding
+
Event Binding
```

---

# 7. Les Directives

## Définition

Les directives modifient dynamiquement le DOM.

Trois grandes catégories :

- Structurelles
- D'attribut
- Personnalisées

---

# 7.1 @if (anciennement *ngIf)

## Rôle

Afficher ou masquer un élément.

---

### Syntaxe moderne

```html
@if (isConnected) {
  <p>Connecté</p>
}
```

---

### Exemple

```typescript
isConnected = true;
```

```html
@if (isConnected) {
  <h2>Bienvenue</h2>
}
```

Résultat :

```html
<h2>Bienvenue</h2>
```

---

### Sinon

```html
@if (isConnected) {
  <h2>Bienvenue</h2>
} @else {
  <h2>Connexion requise</h2>
}
```

---

## Ancienne syntaxe

```html
<h2 *ngIf="isConnected">
  Bienvenue
</h2>
```

Toujours fonctionnelle mais moins recommandée.

---

# 7.2 @for (anciennement *ngFor)

## Rôle

Parcourir une collection.

---

### Exemple

```typescript
users = [
  'Alice',
  'Bob',
  'Charlie'
];
```

---

### Nouvelle syntaxe

```html
@for (user of users; track user) {
  <li>{{ user }}</li>
}
```

Résultat :

```html
<li>Alice</li>
<li>Bob</li>
<li>Charlie</li>
```

---

## Ancienne syntaxe

```html
<li *ngFor="let user of users">
  {{ user }}
</li>
```

---

## Track

Très important pour les performances.

```html
@for (user of users; track user.id)
```

Permet à Angular de savoir quel élément a changé.

---

# 7.3 ngClass

## Rôle

Ajouter des classes CSS dynamiquement.

---

### Exemple simple

```typescript
isActive = true;
```

```html
<div [ngClass]="{'active': isActive}">
  Utilisateur
</div>
```

Résultat :

```html
<div class="active">
```

---

## Plusieurs classes

```html
<div
  [ngClass]="{
    'active': isActive,
    'admin': isAdmin
  }"
>
</div>
```

---

# 7.4 ngStyle

## Rôle

Appliquer des styles dynamiques.

---

### Exemple

```typescript
color = 'red';
```

```html
<p [ngStyle]="{'color': color}">
  Bonjour
</p>
```

Résultat :

```html
<p style="color:red">
```

---

## Exemple avancé

```html
<div
  [ngStyle]="{
    'background-color': bgColor,
    'font-size': size + 'px'
  }"
>
</div>
```

---

# 8. Cycle de vie des composants

Chaque composant traverse plusieurs étapes durant son existence.

```text
Création
↓
Initialisation
↓
Mises à jour
↓
Destruction
```

Angular expose des hooks permettant d'intervenir à chaque étape.

---

# Ordre général d'exécution

```text
constructor()

↓

ngOnChanges()

↓

ngOnInit()

↓

ngDoCheck()

↓

ngAfterContentInit()

↓

ngAfterContentChecked()

↓

ngAfterViewInit()

↓

ngAfterViewChecked()

↓

...

↓

ngOnDestroy()
```

Dans ce cours nous nous concentrons sur :

- ngOnInit
- ngOnChanges
- ngOnDestroy

---

# 8.1 ngOnInit

## Quand est-il exécuté ?

Une seule fois :

```text
Après la création du composant
Après l'initialisation des inputs
```

---

### Exemple

```typescript
export class UserComponent implements OnInit {

  ngOnInit(): void {
    console.log('Initialisation');
  }

}
```

---

## Cas d'utilisation

Chargement initial :

```typescript
ngOnInit() {
  this.loadUsers();
}
```

Appel API :

```typescript
ngOnInit() {
  this.userService.getUsers();
}
```

Préparation de données :

```typescript
ngOnInit() {
  this.calculateStats();
}
```

---

# 8.2 ngOnChanges

## Quand est-il exécuté ?

À chaque changement d'un @Input().

---

### Exemple

Parent :

```html
<app-user
  [username]="name">
</app-user>
```

---

Enfant :

```typescript
@Input()
username!: string;
```

---

Hook :

```typescript
ngOnChanges(changes: SimpleChanges) {
  console.log(changes);
}
```

---

## Déclenchement

```text
Parent modifie username
↓
Angular détecte le changement
↓
ngOnChanges est appelé
```

---

### Exemple complet

```typescript
ngOnChanges(changes: SimpleChanges) {

  if (changes['username']) {

    console.log(
      'Ancienne valeur',
      changes['username'].previousValue
    );

    console.log(
      'Nouvelle valeur',
      changes['username'].currentValue
    );

  }
}
```

---

## Cas d'utilisation

Réagir à un changement venant du parent.

```typescript
@Input()
productId!: number;

ngOnChanges() {
  this.loadProduct();
}
```

---

# 8.3 ngOnDestroy

## Quand est-il exécuté ?

Juste avant la suppression du composant.

---

### Exemple

```typescript
ngOnDestroy() {
  console.log('Destruction');
}
```

---

## Cas d'utilisation principal

Nettoyer les ressources.

---

### Désabonnement RxJS

```typescript
subscription!: Subscription;

ngOnInit() {
  this.subscription =
    this.service.getData()
      .subscribe();
}

ngOnDestroy() {
  this.subscription.unsubscribe();
}
```

---

### Nettoyage d'un timer

```typescript
timerId!: number;

ngOnInit() {
  this.timerId =
    window.setInterval(() => {
      console.log('tick');
    }, 1000);
}

ngOnDestroy() {
  clearInterval(this.timerId);
}
```

---

# Résumé des hooks

| Hook | Quand ? | Nombre d'exécutions |
|--------|----------|----------|
| constructor | Création de l'objet | 1 |
| ngOnChanges | Changement d'un Input | Plusieurs |
| ngOnInit | Après initialisation | 1 |
| ngOnDestroy | Avant suppression | 1 |

---

# Exemple complet

```typescript
@Component({
  selector: 'app-user',
  standalone: true,
  templateUrl: './user.component.html'
})
export class UserComponent
  implements OnInit, OnChanges, OnDestroy {

  @Input()
  username!: string;

  ngOnChanges() {
    console.log('Input modifié');
  }

  ngOnInit() {
    console.log('Composant initialisé');
  }

  ngOnDestroy() {
    console.log('Composant détruit');
  }
}
```

---

# Synthèse Finale

Pour maîtriser Angular, il faut comprendre dans cet ordre :

1. Architecture Angular
2. Composants
3. Templates
4. Data Binding
   - Interpolation
   - Property Binding
   - Event Binding
   - Two-Way Binding
5. Directives
   - @if
   - @for
   - ngClass
   - ngStyle
6. Cycle de vie des composants
   - ngOnInit
   - ngOnChanges
   - ngOnDestroy

Ces concepts constituent le socle de quasiment toutes les applications Angular modernes, qu'elles utilisent des modules classiques ou l'approche Standalone désormais recommandée.
