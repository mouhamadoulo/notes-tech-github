En Angular, `constructor` et `ngOnInit()` sont tous les deux exécutés lors de la création d'un composant, mais **ils n'ont pas le même rôle**. La différence principale est liée au **cycle de vie du composant**.

## 1. Le `constructor`

Le `constructor` est une fonctionnalité de **TypeScript/JavaScript**, pas d'Angular.

Il est appelé **dès que l'objet est créé**, avant qu'Angular n'ait complètement initialisé le composant.

### À quoi sert-il ?

Principalement à :

* injecter les dépendances (services) ;
* initialiser des propriétés simples.

```typescript
export class UserComponent {

  constructor(private userService: UserService) {
    console.log("Constructor appelé");
  }

}
```

### Ce qu'il ne faut pas faire

Évitez d'y mettre :

* des appels HTTP ;
* de la logique métier importante ;
* du code qui dépend des `@Input()`.

À ce moment-là, Angular n'a pas encore terminé d'initialiser le composant.

---

## 2. `ngOnInit()`

`ngOnInit()` est un **hook du cycle de vie Angular**.

Il est appelé **une seule fois**, juste après qu'Angular a :

* créé le composant ;
* injecté les dépendances ;
* initialisé les propriétés décorées avec `@Input()`.

```typescript
export class UserComponent implements OnInit {

  constructor(private userService: UserService) {}

  ngOnInit(): void {
    console.log("OnInit appelé");
  }

}
```

---

## Exemple concret

Imaginons un composant qui reçoit un identifiant.

```typescript
@Component({...})
export class UserComponent implements OnInit {

  @Input()
  userId!: number;

  constructor() {
    console.log(this.userId);
  }

  ngOnInit() {
    console.log(this.userId);
  }

}
```

Si le composant est utilisé ainsi :

```html
<app-user [userId]="10"></app-user>
```

L'ordre sera :

```
Constructor
→ userId = undefined

ngOnInit
→ userId = 10
```

Pourquoi ?

Parce que les `@Input()` sont affectés **après** le constructeur.

---

## Quand utiliser lequel ?

### Utilisez le `constructor` pour :

✅ Injection de dépendances

```typescript
constructor(
  private http: HttpClient,
  private router: Router
) {}
```

✅ Initialiser des variables simples

```typescript
constructor() {
  this.title = "Accueil";
}
```

---

### Utilisez `ngOnInit()` pour :

✅ Charger des données

```typescript
ngOnInit() {
  this.userService.getUsers().subscribe(...);
}
```

✅ Faire des appels HTTP

```typescript
ngOnInit() {
  this.loadProducts();
}
```

✅ Utiliser les valeurs de `@Input()`

```typescript
ngOnInit() {
  console.log(this.userId);
}
```

✅ Lancer la logique métier du composant

---

## Pourquoi ne pas tout mettre dans le constructeur ?

Imaginez que le constructeur serve uniquement à **assembler** le composant.

Une fois le composant construit, Angular dit :

> "Le composant est prêt, tu peux maintenant commencer ton travail."

C'est exactement le rôle de `ngOnInit()`.

Cela permet notamment à Angular de garantir que :

* les services sont injectés ;
* les `@Input()` sont disponibles ;
* le composant est correctement initialisé avant d'exécuter votre logique.

---

## Résumé

| `constructor`                         | `ngOnInit()`                                           |
| ------------------------------------- | ------------------------------------------------------ |
| Fait partie de TypeScript             | Fait partie d'Angular                                  |
| Appelé lors de la création de l'objet | Appelé après l'initialisation du composant             |
| Injection des dépendances             | Initialisation métier                                  |
| `@Input()` pas encore disponibles     | `@Input()` disponibles                                 |
| Éviter les appels HTTP                | Idéal pour les appels HTTP et le chargement de données |

### Bonne pratique

Une règle simple est :

* **`constructor`** → *"De quoi mon composant a-t-il besoin pour être créé ?"* (injection de services, initialisation simple).
* **`ngOnInit()`** → *"Que doit faire mon composant une fois prêt ?"* (chargement des données, appels API, logique d'initialisation).

Cette séparation rend le code plus lisible, plus facile à tester et conforme aux conventions Angular.
