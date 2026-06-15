# Communication Angular ↔ Backend Java (Spring Boot)

> Objectif : maîtriser tout ce qu'un développeur Angular doit savoir pour communiquer efficacement avec un backend Java (principalement Spring Boot), depuis les bases jusqu'aux pratiques avancées utilisées en entreprise.

---

# 1. Architecture générale

## Vue d'ensemble

```text
+--------------------+
|      Angular       |
|   Frontend SPA     |
+---------+----------+
          |
          | HTTP / HTTPS
          |
+---------v----------+
|    Spring Boot     |
|   API REST Java    |
+---------+----------+
          |
          |
+---------v----------+
|     Database       |
| PostgreSQL/MySQL   |
+--------------------+
```

Le frontend Angular :

- affiche les données
- gère l'interface utilisateur
- envoie des requêtes HTTP

Le backend Java :

- contient la logique métier
- valide les données
- accède à la base de données
- renvoie des réponses JSON

---

# 2. Le format d'échange : JSON

## Backend Java

```java
public class UserDto {

    private Long id;
    private String firstname;
    private String lastname;

}
```

Réponse HTTP :

```json
{
  "id": 1,
  "firstname": "John",
  "lastname": "Doe"
}
```

Angular reçoit automatiquement ce JSON sous forme d'objet JavaScript.

---

# 3. HttpClient : la base de tout

## Import

Dans Angular moderne :

```typescript
bootstrapApplication(AppComponent, {
  providers: [
    provideHttpClient()
  ]
});
```

Ou dans un module :

```typescript
imports: [
  HttpClientModule
]
```

---

# 4. Créer un Service API

Bonne pratique :

```text
components
services
models
```

Exemple :

```text
src/app
    ├── models
    ├── services
    └── components
```

---

# 5. Création d'un modèle

```typescript
export interface User {
    id: number;
    firstname: string;
    lastname: string;
}
```

---

# 6. Premier appel GET

## Backend Java

```java
@GetMapping("/users")
public List<UserDto> getUsers() {
    return userService.findAll();
}
```

---

## Angular Service

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  private apiUrl = 'http://localhost:8080/api/users';

  constructor(private http: HttpClient) {}

  getUsers(): Observable<User[]> {
    return this.http.get<User[]>(this.apiUrl);
  }
}
```

---

# 7. Consommer dans un composant

```typescript
@Component({
  selector: 'app-users'
})
export class UsersComponent {

  users: User[] = [];

  constructor(private userService: UserService) {}

  ngOnInit(): void {

    this.userService.getUsers()
      .subscribe(users => {
        this.users = users;
      });

  }
}
```

---

# 8. Comprendre les Observables

Angular utilise RxJS.

```typescript
Observable<User[]>
```

Une Observable :

- peut émettre plusieurs valeurs
- est asynchrone
- est annulable

---

## Exemple

```typescript
this.userService.getUsers()
  .subscribe({
      next: users => console.log(users),
      error: err => console.error(err),
      complete: () => console.log('terminé')
  });
```

---

# 9. POST : création

## Backend Java

```java
@PostMapping
public UserDto create(@RequestBody UserDto dto) {
    return userService.create(dto);
}
```

---

## Angular

```typescript
createUser(user: User): Observable<User> {

    return this.http.post<User>(
        this.apiUrl,
        user
    );

}
```

---

## Utilisation

```typescript
const newUser: User = {
    id: 0,
    firstname: 'John',
    lastname: 'Doe'
};

this.userService.createUser(newUser)
    .subscribe(result => {
        console.log(result);
    });
```

---

# 10. PUT : mise à jour

## Backend

```java
@PutMapping("/{id}")
public UserDto update(
        @PathVariable Long id,
        @RequestBody UserDto dto) {

    return userService.update(id, dto);
}
```

---

## Angular

```typescript
updateUser(
    id: number,
    user: User
): Observable<User> {

    return this.http.put<User>(
        `${this.apiUrl}/${id}`,
        user
    );
}
```

---

# 11. DELETE

## Backend

```java
@DeleteMapping("/{id}")
public void delete(@PathVariable Long id) {
    userService.delete(id);
}
```

---

## Angular

```typescript
deleteUser(id: number): Observable<void> {

    return this.http.delete<void>(
        `${this.apiUrl}/${id}`
    );

}
```

---

# 12. Les Headers HTTP

Exemple :

```typescript
const headers = new HttpHeaders({
    'Content-Type': 'application/json'
});
```

Utilisation :

```typescript
this.http.post(
    this.apiUrl,
    user,
    { headers }
);
```

---

# 13. Paramètres de requête

## Backend

```java
@GetMapping
public List<UserDto> search(
        @RequestParam String lastname) {

    return service.search(lastname);
}
```

---

## Angular

```typescript
const params = new HttpParams()
    .set('lastname', 'Doe');

this.http.get<User[]>(
    this.apiUrl,
    { params }
);
```

---

# 14. Gestion centralisée des URLs

## environment.ts

```typescript
export const environment = {
  apiUrl: 'http://localhost:8080/api'
};
```

---

## Service

```typescript
private apiUrl =
    `${environment.apiUrl}/users`;
```

---

# 15. Gestion des erreurs

## Backend

```java
throw new EntityNotFoundException();
```

---

## Angular

```typescript
this.userService.getUsers()
  .subscribe({
      next: users => {},
      error: err => {

          if(err.status === 404){
              console.log('Non trouvé');
          }

      }
  });
```

---

# 16. catchError

Très utilisé en entreprise.

```typescript
getUsers(): Observable<User[]> {

    return this.http.get<User[]>(this.apiUrl)
      .pipe(
          catchError(error => {

              console.error(error);

              return throwError(
                () => error
              );

          })
      );
}
```

---

# 17. DTO Backend ↔ Frontend

Ne jamais exposer directement les entités JPA.

## Mauvais

```java
@Entity
public class User
```

Retourner directement :

```java
return user;
```

---

## Bon

```java
public class UserDto
```

```java
return userDto;
```

---

# 18. Authentification JWT

Très fréquent.

## Login

```text
POST /auth/login
```

---

## Backend renvoie

```json
{
  "token": "eyJhbGc..."
}
```

---

## Angular

```typescript
login(credentials: LoginRequest) {

    return this.http.post<LoginResponse>(
        '/auth/login',
        credentials
    );

}
```

---

# 19. Stockage du token

```typescript
localStorage.setItem(
    'token',
    response.token
);
```

Lecture :

```typescript
const token =
    localStorage.getItem('token');
```

---

# 20. Interceptor JWT

Évite de répéter le token partout.

## Génération

```bash
ng generate interceptor auth
```

---

## Interceptor

```typescript
export const authInterceptor: HttpInterceptorFn =
(req, next) => {

    const token =
        localStorage.getItem('token');

    if(token){

        req = req.clone({
            setHeaders: {
                Authorization:
                  `Bearer ${token}`
            }
        });

    }

    return next(req);
};
```

---

## Enregistrement

```typescript
provideHttpClient(
    withInterceptors([
        authInterceptor
    ])
)
```

---

# 21. Spring Security + JWT

Backend :

```java
http
 .csrf(csrf -> csrf.disable())
 .authorizeHttpRequests(auth -> auth
      .requestMatchers("/auth/**")
      .permitAll()
      .anyRequest()
      .authenticated()
 );
```

---

# 22. CORS

Erreur classique :

```text
Access to fetch has been blocked by CORS
```

---

## Backend

```java
@Configuration
public class CorsConfig {

    @Bean
    public WebMvcConfigurer corsConfigurer() {

        return new WebMvcConfigurer() {

            @Override
            public void addCorsMappings(
                CorsRegistry registry) {

                registry.addMapping("/**")
                  .allowedOrigins(
                    "http://localhost:4200"
                  );

            }
        };
    }
}
```

---

# 23. Pagination

## Backend

```java
@GetMapping
public Page<UserDto> findAll(
        Pageable pageable) {
    return service.findAll(pageable);
}
```

---

## Appel Angular

```typescript
const params = new HttpParams()
    .set('page', 0)
    .set('size', 20);
```

---

## Réponse

```json
{
  "content": [],
  "totalElements": 100,
  "totalPages": 5
}
```

---

# 24. Interface de pagination

```typescript
export interface Page<T> {

    content: T[];

    totalElements: number;

    totalPages: number;

    number: number;

}
```

---

# 25. Upload de fichier

## Backend

```java
@PostMapping("/upload")
public void upload(
   @RequestParam MultipartFile file
){}
```

---

## Angular

```typescript
const formData =
    new FormData();

formData.append(
    'file',
    selectedFile
);
```

---

```typescript
this.http.post(
    '/upload',
    formData
);
```

---

# 26. Télécharger un fichier

## Backend

```java
@GetMapping("/download")
public ResponseEntity<Resource> download()
```

---

## Angular

```typescript
this.http.get(
    '/download',
    {
      responseType: 'blob'
    }
);
```

---

# 27. Appels parallèles

```typescript
forkJoin({
    users: this.userService.getUsers(),
    products: this.productService.getProducts()
})
.subscribe(result => {

    console.log(result.users);
    console.log(result.products);

});
```

---

# 28. switchMap

Indispensable.

Exemple :

```typescript
this.authService.login(data)
    .pipe(

        switchMap(response => {

            return this.userService
                .getProfile();

        })

    )
    .subscribe();
```

---

# 29. Gestion du chargement

```typescript
loading = true;
```

```typescript
this.userService.getUsers()
    .pipe(
        finalize(() => {
            this.loading = false;
        })
    )
    .subscribe();
```

---

# 30. Gestion globale des erreurs

Interceptor :

```typescript
export const errorInterceptor:
HttpInterceptorFn =
(req, next) => {

    return next(req).pipe(

        catchError(error => {

            console.error(error);

            return throwError(
              () => error
            );

        })

    );

};
```

---

# 31. API REST propre

Convention recommandée :

```text
GET    /users
GET    /users/1

POST   /users

PUT    /users/1

DELETE /users/1
```

Éviter :

```text
/getUsers
/createUser
/deleteUser
```

---

# 32. Cache HTTP

```typescript
private users$?: Observable<User[]>;
```

```typescript
getUsers() {

    if(!this.users$){

        this.users$ =
          this.http.get<User[]>(this.apiUrl)
            .pipe(
              shareReplay(1)
            );

    }

    return this.users$;
}
```

---

# 33. Résolution de données avant navigation

Resolver :

```typescript
export const usersResolver:
ResolveFn<User[]> = () => {

    return inject(UserService)
      .getUsers();

};
```

---

Route :

```typescript
{
   path: 'users',
   component: UsersComponent,
   resolve: {
      users: usersResolver
   }
}
```

---

# 34. Typage fort

Toujours :

```typescript
Observable<User>
```

Jamais :

```typescript
Observable<any>
```

---

# 35. Architecture entreprise recommandée

```text
src
|
+-- core
|   |
|   +-- interceptors
|   +-- guards
|   +-- services
|
+-- shared
|   |
|   +-- models
|   +-- components
|
+-- features
|   |
|   +-- users
|   +-- products
|
+-- environments
```

---

# 36. Bonnes pratiques Angular + Spring Boot

## Toujours

✅ Utiliser DTO

✅ Utiliser HttpClient

✅ Utiliser Interceptor JWT

✅ Centraliser les URLs

✅ Gérer les erreurs

✅ Utiliser RxJS

✅ Utiliser les interfaces TypeScript

✅ Utiliser les environnements

✅ Utiliser la pagination

✅ Utiliser Spring Security

---

## Éviter

❌ any

❌ appels HTTP dans les templates

❌ logique métier dans les composants

❌ stockage du token dans les variables globales

❌ duplication des URLs

❌ subscribe imbriqués

Exemple mauvais :

```typescript
this.authService.login()
  .subscribe(login => {

      this.userService.getProfile()
        .subscribe(profile => {

        });

  });
```

Préférer :

```typescript
this.authService.login()
  .pipe(
      switchMap(() =>
          this.userService.getProfile()
      )
  )
  .subscribe();
```

---

# 37. Exemple complet d'un CRUD Angular

## Service

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  private apiUrl =
      `${environment.apiUrl}/users`;

  constructor(
      private http: HttpClient
  ) {}

  findAll() {
      return this.http.get<User[]>(
          this.apiUrl
      );
  }

  findById(id: number) {
      return this.http.get<User>(
          `${this.apiUrl}/${id}`
      );
  }

  create(user: User) {
      return this.http.post<User>(
          this.apiUrl,
          user
      );
  }

  update(
      id: number,
      user: User
  ) {
      return this.http.put<User>(
          `${this.apiUrl}/${id}`,
          user
      );
  }

  delete(id: number) {
      return this.http.delete<void>(
          `${this.apiUrl}/${id}`
      );
  }
}
```

---

# Conclusion

Pour être considéré comme un développeur Angular confirmé sur la communication avec un backend Java Spring Boot, tu dois maîtriser :

1. HTTP (GET, POST, PUT, DELETE)
2. HttpClient
3. RxJS (Observable, pipe, switchMap, forkJoin)
4. DTO Backend
5. JWT
6. Interceptors
7. Gestion des erreurs
8. CORS
9. Pagination
10. Upload/Download de fichiers
11. Spring Security
12. Architecture Angular scalable
13. Typage fort TypeScript
14. Cache et optimisation réseau
15. Bonnes pratiques REST

Une fois ces concepts maîtrisés, tu es capable de développer la majorité des applications professionnelles Angular + Spring Boot utilisées en entreprise.
