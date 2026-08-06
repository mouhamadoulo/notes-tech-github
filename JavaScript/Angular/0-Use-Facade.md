Le pattern **Facade** en Angular est un **pattern d'architecture** qui consiste à créer une couche intermédiaire entre les composants et la logique métier (services, API, state management, etc.).

L'idée est simple :

> **Le composant ne sait pas comment les données sont récupérées ou modifiées. Il parle uniquement à une Facade.**

Ce pattern est très utilisé avec :

* Signals (Angular 17+)
* RxJS
* NgRx
* Akita
* NGXS
* ou même sans librairie de state management.

Dans **Angular 22**, avec les **Signals** et les nouvelles APIs (`inject()`, `resource()`, etc.), la Facade est devenue encore plus naturelle.

---

# Sans Facade

Imaginons une page affichant des utilisateurs.

Le composant fait tout :

```ts
@Component({...})
export class UsersComponent {

  private userService = inject(UserService);

  users = signal<User[]>([]);
  loading = signal(false);

  ngOnInit() {
    this.loading.set(true);

    this.userService.getUsers().subscribe(users => {
      this.users.set(users);
      this.loading.set(false);
    });
  }

  delete(id: number) {
    this.userService.delete(id).subscribe(() => {
      this.users.update(users => users.filter(u => u.id !== id));
    });
  }

}
```

Le composant :

* appelle l'API
* gère le loading
* gère les erreurs
* gère le state
* met à jour les données

Il fait beaucoup trop de choses.

---

# Avec une Facade

Le composant ne fait plus que piloter l'UI.

```
UsersComponent
       │
       ▼
 UsersFacade
       │
       ▼
 UserService
       │
       ▼
     Backend
```

---

# Création de la Facade

```ts
@Injectable({
  providedIn: 'root'
})
export class UsersFacade {

  private userService = inject(UserService);

  users = signal<User[]>([]);
  loading = signal(false);
  error = signal<string | null>(null);

  loadUsers() {

    this.loading.set(true);

    this.userService.getUsers().subscribe({
      next: users => {
        this.users.set(users);
        this.loading.set(false);
      },
      error: () => {
        this.error.set('Impossible de charger');
        this.loading.set(false);
      }
    });

  }

  deleteUser(id: number) {

    this.userService.delete(id).subscribe(() => {
      this.users.update(users =>
        users.filter(u => u.id !== id)
      );
    });

  }

}
```

Toute la logique est ici.

---

# Le composant devient minuscule

```ts
@Component({...})
export class UsersComponent {

  facade = inject(UsersFacade);

  users = this.facade.users;
  loading = this.facade.loading;

  ngOnInit() {
    this.facade.loadUsers();
  }

  delete(id: number) {
    this.facade.deleteUser(id);
  }

}
```

Il ne connaît même plus le `UserService`.

---

# Template

```html
@if(loading()) {

  <mat-spinner/>

} @else {

  @for(user of users(); track user.id){

      <app-user-card
          [user]="user"
          (delete)="delete(user.id)">
      </app-user-card>

  }

}
```

Le template est également très simple.

---

# Un exemple plus réaliste : E-commerce

Imaginons une page produit.

Sans Facade :

```
ProductComponent

↓ appelle

ProductService

↓ appelle

CartService

↓ appelle

WishlistService

↓ appelle

NotificationService

↓ appelle

AnalyticsService

↓ appelle

Router
```

Le composant finit avec 400 lignes.

---

Avec une Facade :

```
ProductComponent
         │
         ▼
    ProductFacade
         │
 ┌───────┼─────────────┐
 ▼       ▼             ▼
Product CartService Wishlist
Service
         ▼
Notification
         ▼
Analytics
```

Le composant :

```ts
addToCart() {
    this.facade.addCurrentProductToCart();
}
```

Et toute la logique est cachée :

```ts
addCurrentProductToCart() {

   this.cartService.add(this.product());

   this.notification.success("Produit ajouté");

   this.analytics.track("ADD_TO_CART");

   this.router.navigate(['/cart']);

}
```

Le composant n'a aucune idée de tout cela.

---

# Avec les Signals (Angular 22)

On peut exploiter les `computed` pour exposer des états dérivés.

```ts
@Injectable({
  providedIn: 'root'
})
export class UsersFacade {

  users = signal<User[]>([]);

  selectedUser = signal<number | null>(null);

  currentUser = computed(() =>
    this.users().find(u => u.id === this.selectedUser())
  );

}
```

Le composant :

```ts
currentUser = this.facade.currentUser;
```

Sans avoir à écrire de logique supplémentaire.

---

# Une Facade peut agréger plusieurs services

```ts
@Injectable({
  providedIn: 'root'
})
export class DashboardFacade {

  private users = inject(UserService);
  private orders = inject(OrderService);
  private stats = inject(StatsService);

  dashboard = signal<Dashboard | null>(null);

  load() {

    forkJoin({
      users: this.users.getUsers(),
      orders: this.orders.getOrders(),
      stats: this.stats.getStats()
    }).subscribe(result => {

      this.dashboard.set(result);

    });

  }

}
```

Le composant ne reçoit qu'un seul objet :

```ts
dashboard = facade.dashboard;
```

Au lieu de devoir coordonner trois appels HTTP différents.

---

# Avec un Store basé sur les Signals

Une pratique courante consiste à séparer les responsabilités en trois couches :

```
UserComponent
      │
      ▼
UserFacade
      │
      ▼
UserStore (Signals)
      │
      ▼
UserApiService
```

Chaque couche a un rôle précis :

* **UserComponent** : affiche les données et réagit aux interactions utilisateur.
* **UserFacade** : expose des méthodes métier (`loadUsers()`, `deleteUser()`, `selectUser()`) et masque les détails d'implémentation.
* **UserStore** : conserve l'état avec des `signal()` et des `computed()`.
* **UserApiService** : communique avec le backend.

Cette organisation facilite les tests, la réutilisation et l'évolution de l'application.

---

# Les avantages

* **Composants plus légers** : ils se concentrent sur l'affichage et les interactions.
* **Centralisation de la logique métier** : plus facile à maintenir et à faire évoluer.
* **Découplage** : les composants ne dépendent pas directement des services ou de la stratégie de gestion d'état.
* **Réutilisation** : plusieurs composants peuvent partager une même Facade.
* **Tests simplifiés** : il est facile de remplacer la Facade par un mock dans les tests des composants.

---

# Les limites

Une Facade n'est pas toujours nécessaire. Pour une application simple avec quelques composants, elle peut ajouter une couche d'abstraction inutile. Elle devient particulièrement pertinente lorsque :

* plusieurs composants partagent le même état ;
* une fonctionnalité coordonne plusieurs services ;
* la logique métier devient importante ;
* vous utilisez un store (Signals Store, NgRx, etc.) ou souhaitez pouvoir en changer sans impacter les composants.

En pratique, sur un projet Angular moderne (Angular 20+ et donc Angular 22), une architecture **Component → Facade → Store → API** est souvent un bon compromis : les composants restent très simples, la logique métier est centralisée dans les Facades, et le mécanisme de gestion d'état peut évoluer sans nécessiter de modifications dans l'interface utilisateur.
