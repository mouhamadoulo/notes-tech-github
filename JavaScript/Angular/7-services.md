# Services et Injection de Dépendances (Dependency Injection)

---

# Introduction

L'un des concepts les plus importants d'Angular est le système de **Services** et **l'Injection de Dépendances (DI - Dependency Injection)**.

Un développeur Angular expérimenté passe souvent plus de temps à concevoir correctement ses services qu'à écrire ses composants.

Sans services :

- logique métier dispersée
- duplication de code
- faible maintenabilité
- difficulté de tests

Avec services + DI :

- code modulaire
- réutilisable
- testable
- découplé
- maintenable

---

# 1. Qu'est-ce qu'un Service ?

Un service est une classe TypeScript contenant une logique réutilisable indépendante de l'affichage.

Exemples :

- appels HTTP
- authentification
- gestion des utilisateurs
- gestion des produits
- stockage local
- calculs métier
- communication entre composants

---

## Mauvaise pratique

```typescript
@Component({...})
export class UserComponent {

  users = [];

  constructor(private http: HttpClient) {}

  loadUsers() {
    this.http.get('/api/users')
      .subscribe(users => {
        this.users = users;
      });
  }
}
```

Le composant fait :

- affichage
- récupération de données

Il a trop de responsabilités.

---

## Bonne pratique

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  constructor(private http: HttpClient) {}

  getUsers() {
    return this.http.get('/api/users');
  }
}
```

Puis :

```typescript
@Component({...})
export class UserComponent {

  users = [];

  constructor(private userService: UserService) {}

  ngOnInit() {
    this.userService.getUsers()
      .subscribe(users => {
        this.users = users;
      });
  }
}
```

Le composant ne gère plus que l'interface utilisateur.

---

# 2. Création d'un Service

CLI :

```bash
ng generate service services/user
```

ou

```bash
ng g s services/user
```

Angular génère :

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  constructor() { }

}
```

---

# 3. Le décorateur @Injectable()

```typescript
@Injectable()
export class UserService {}
```

Indique à Angular :

> Cette classe peut participer au système d'injection de dépendances.

---

Exemple :

```typescript
@Injectable()
export class UserService {

  constructor(
    private http: HttpClient
  ) {}

}
```

Angular comprend :

- UserService dépend de HttpClient
- il doit injecter HttpClient

---

# 4. Qu'est-ce que l'Injection de Dépendances ?

## Définition

La DI consiste à fournir automatiquement les dépendances dont une classe a besoin.

Sans DI :

```typescript
export class UserService {

  private http = new HttpClient();

}
```

Problèmes :

- couplage fort
- difficile à tester
- Angular perd le contrôle

---

Avec DI :

```typescript
export class UserService {

  constructor(
    private http: HttpClient
  ) {}

}
```

Angular fournit lui-même HttpClient.

---

# 5. Le conteneur d'injection (Injector)

Angular maintient un registre d'objets.

```text
Injector
│
├── HttpClient
├── Router
├── UserService
├── AuthService
└── ProductService
```

Quand Angular voit :

```typescript
constructor(
  private userService: UserService
)
```

Il cherche :

```text
UserService ?
```

puis :

```text
Injector -> trouvé
```

et l'injecte.

---

# 6. providedIn: 'root'

Le plus utilisé.

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {}
```

Signifie :

```text
Créer une instance globale unique
```

Singleton.

---

# 7. Singleton

Avec :

```typescript
providedIn: 'root'
```

Toute l'application partage la même instance.

```typescript
Component A
     │
     ▼
UserService
     ▲
     │
Component B
```

Même objet.

---

Exemple :

```typescript
@Injectable({
  providedIn: 'root'
})
export class CounterService {

  count = 0;

  increment() {
    this.count++;
  }
}
```

Component A :

```typescript
this.counter.increment();
```

Component B :

```typescript
console.log(this.counter.count);
```

Affiche :

```text
1
```

Car même instance.

---

# 8. Hiérarchie des Injectors

Angular possède plusieurs injectors.

```text
Root Injector
    │
    ├── Module Injector
    │
    └── Component Injector
```

---

# 9. Fournir un Service dans un composant

```typescript
@Component({
  selector: 'app-user',
  providers: [UserService]
})
export class UserComponent {}
```

Chaque instance du composant reçoit son propre service.

---

Exemple

```typescript
@Component({
  providers: [CounterService]
})
```

Chaque composant aura :

```text
CounterService #1
CounterService #2
CounterService #3
```

---

# 10. Différence Root vs Component Provider

## Root

```typescript
@Injectable({
  providedIn: 'root'
})
```

Instance unique.

---

## Component

```typescript
@Component({
  providers: [UserService]
})
```

Nouvelle instance.

---

# 11. Injection dans un composant

```typescript
constructor(
  private userService: UserService
) {}
```

Angular injecte automatiquement le service.

---

# 12. Injection dans un autre service

Service dans service.

```typescript
@Injectable({
  providedIn: 'root'
})
export class AuthService {

  constructor(
    private http: HttpClient
  ) {}

}
```

Puis :

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  constructor(
    private authService: AuthService
  ) {}

}
```

Angular résout toute la chaîne.

---

# 13. Service HTTP Réel

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  private api = '/api/users';

  constructor(
    private http: HttpClient
  ) {}

  getAll() {
    return this.http.get<User[]>(this.api);
  }

  getById(id: number) {
    return this.http.get<User>(
      `${this.api}/${id}`
    );
  }

  create(user: User) {
    return this.http.post(
      this.api,
      user
    );
  }

  update(id: number, user: User) {
    return this.http.put(
      `${this.api}/${id}`,
      user
    );
  }

  delete(id: number) {
    return this.http.delete(
      `${this.api}/${id}`
    );
  }
}
```

---

# 14. Service comme Store Local

```typescript
@Injectable({
  providedIn: 'root'
})
export class CartService {

  items: Product[] = [];

  add(product: Product) {
    this.items.push(product);
  }

  getItems() {
    return this.items;
  }
}
```

Partagé dans toute l'application.

---

# 15. Communication entre composants via Service

Très fréquent.

---

Service :

```typescript
@Injectable({
  providedIn: 'root'
})
export class NotificationService {

  notification$ = new Subject<string>();

}
```

---

Émetteur :

```typescript
this.notificationService.notification$
  .next('Produit ajouté');
```

---

Récepteur :

```typescript
this.notificationService.notification$
  .subscribe(message => {
    console.log(message);
  });
```

---

# 16. Injection avec inject()

Depuis Angular moderne :

```typescript
const router = inject(Router);
```

Au lieu de :

```typescript
constructor(
  private router: Router
) {}
```

---

Exemple :

```typescript
@Injectable({
  providedIn: 'root'
})
export class AuthService {

  private router = inject(Router);

}
```

---

# 17. Injection dans un Functional Guard

```typescript
export const authGuard: CanActivateFn = () => {

  const auth = inject(AuthService);

  return auth.isLoggedIn();

};
```

---

# 18. Injection Token

Pour injecter des valeurs simples.

---

Création :

```typescript
export const API_URL =
  new InjectionToken<string>('API_URL');
```

---

Provider :

```typescript
providers: [
  {
    provide: API_URL,
    useValue: 'https://api.monsite.com'
  }
]
```

---

Injection :

```typescript
constructor(
  @Inject(API_URL)
  private apiUrl: string
) {}
```

---

# 19. useClass

Permet de remplacer une implémentation.

```typescript
providers: [
  {
    provide: LoggerService,
    useClass: BetterLoggerService
  }
]
```

---

# 20. useValue

```typescript
providers: [
  {
    provide: API_URL,
    useValue: 'http://localhost:3000'
  }
]
```

---

# 21. useExisting

Alias.

```typescript
providers: [
  {
    provide: OldLoggerService,
    useExisting: LoggerService
  }
]
```

---

# 22. useFactory

Création dynamique.

```typescript
providers: [
  {
    provide: API_URL,
    useFactory: () => {

      return environment.production
        ? 'https://prod.com'
        : 'http://localhost';

    }
  }
]
```

---

# 23. Multi Providers

Plusieurs implémentations.

```typescript
providers: [
  {
    provide: MY_TOKEN,
    useValue: 'A',
    multi: true
  },
  {
    provide: MY_TOKEN,
    useValue: 'B',
    multi: true
  }
]
```

Résultat :

```typescript
['A', 'B']
```

---

# 24. Optional Injection

```typescript
constructor(
  @Optional()
  private logger?: LoggerService
) {}
```

Si absent :

```typescript
undefined
```

Pas d'erreur.

---

# 25. Self()

Recherche uniquement localement.

```typescript
constructor(
  @Self()
  private service: UserService
) {}
```

---

# 26. SkipSelf()

Ignore l'injector courant.

```typescript
constructor(
  @SkipSelf()
  private service: UserService
) {}
```

---

# 27. Host()

Recherche jusqu'au composant hôte.

```typescript
constructor(
  @Host()
  private service: UserService
) {}
```

---

# 28. Cycle de dépendances

Problème :

```typescript
AuthService
   ↓
UserService
   ↓
AuthService
```

Erreur :

```text
Circular Dependency
```

---

Exemple dangereux

```typescript
AuthService
constructor(
  private userService: UserService
)
```

et

```typescript
UserService
constructor(
  private authService: AuthService
)
```

À éviter.

---

# 29. Bonnes Pratiques

## Un service = une responsabilité

Mauvais :

```typescript
UserService
```

qui fait :

- auth
- panier
- produits
- logs

---

Bon :

```text
AuthService
UserService
CartService
ProductService
LoggerService
```

---

## Ne pas stocker de logique métier dans les composants

Mauvais :

```typescript
@Component(...)
```

avec 500 lignes.

---

Bon :

```typescript
@Component(...)
```

30 lignes

```typescript
UserService
```

300 lignes

---

## Toujours typer les retours

Mauvais :

```typescript
getUsers() {
  return this.http.get('/users');
}
```

---

Bon :

```typescript
getUsers(): Observable<User[]> {
  return this.http.get<User[]>('/users');
}
```

---

# 30. Exemple Professionnel Complet

```typescript
@Injectable({
  providedIn: 'root'
})
export class AuthService {

  private http = inject(HttpClient);

  private tokenKey = 'token';

  login(email: string, password: string) {

    return this.http.post<AuthResponse>(
      '/api/login',
      {
        email,
        password
      }
    );

  }

  saveToken(token: string) {

    localStorage.setItem(
      this.tokenKey,
      token
    );

  }

  getToken() {

    return localStorage.getItem(
      this.tokenKey
    );

  }

  isLoggedIn() {

    return !!this.getToken();

  }

  logout() {

    localStorage.removeItem(
      this.tokenKey
    );

  }

}
```

Utilisation :

```typescript
@Component({...})
export class LoginComponent {

  constructor(
    private auth: AuthService
  ) {}

  login() {

    this.auth.login(
      this.email,
      this.password
    )
    .subscribe(response => {

      this.auth.saveToken(
        response.token
      );

    });

  }

}
```

---

# Ce qu'un développeur Angular senior doit absolument maîtriser

✅ Création de services

✅ @Injectable()

✅ providedIn: 'root'

✅ Singleton

✅ Hiérarchie des injectors

✅ Injection via constructor

✅ Injection via inject()

✅ Injection de service dans service

✅ HttpClient dans les services

✅ RxJS dans les services

✅ Communication entre composants

✅ InjectionToken

✅ useClass

✅ useValue

✅ useFactory

✅ useExisting

✅ Multi Providers

✅ Optional / Self / SkipSelf / Host

✅ Gestion des scopes d'injection

✅ Éviter les dépendances circulaires

✅ Architecture orientée services

---

# Résumé Mental à Retenir

```text
Component
    │
    ▼
Service
    │
    ▼
HttpClient / API
```

Le composant affiche.

Le service contient la logique.

L'injector fournit les dépendances.

C'est l'un des piliers fondamentaux de l'architecture Angular moderne.
