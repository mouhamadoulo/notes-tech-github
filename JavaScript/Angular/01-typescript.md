# TypeScript pour Développeur Angular

> Objectif : maîtriser TypeScript de manière professionnelle pour développer des applications Angular modernes, maintenables, robustes et fortement typées.

---

# Sommaire

1. Pourquoi TypeScript ?
2. Les types primitifs
3. Inférence de type
4. Any, Unknown, Never
5. Arrays et Tuples
6. Enums
7. Literal Types
8. Union Types
9. Intersection Types
10. Type Aliases
11. Interfaces
12. Classes
13. Modificateurs d'accès
14. Héritage
15. Classes abstraites
16. Interfaces vs Classes
17. Generics
18. Fonctions
19. Paramètres avancés
20. Fonctions fléchées
21. Typage des objets
22. Optional Chaining
23. Nullish Coalescing
24. Assertions de type
25. Guards de type
26. Type Narrowing
27. Utility Types
28. Decorators
29. Modules
30. Namespaces
31. Async / Await
32. Typage des Promises
33. Typage RxJS
34. TypeScript avancé
35. Types conditionnels
36. Mapped Types
37. keyof
38. typeof
39. Indexed Access Types
40. Bonnes pratiques Angular

---

# 1. Pourquoi TypeScript ?

JavaScript est dynamique.

```javascript
let age = 25;
age = "bonjour";
```

Aucune erreur.

TypeScript ajoute :

- Typage statique
- Autocomplétion
- Refactoring sécurisé
- Détection précoce des erreurs

```typescript
let age: number = 25;

age = "bonjour"; // Erreur
```

---

# 2. Types primitifs

## string

```typescript
let firstName: string = "John";
```

## number

```typescript
let age: number = 30;
```

## boolean

```typescript
let isAdmin: boolean = true;
```

## bigint

```typescript
let population: bigint = 1000000000n;
```

## symbol

```typescript
const id: symbol = Symbol("id");
```

---

# 3. Inférence de type

TypeScript déduit automatiquement le type.

```typescript
let firstName = "John";
```

Equivalent à :

```typescript
let firstName: string = "John";
```

---

# 4. Any, Unknown, Never

## Any

À éviter.

```typescript
let value: any = "hello";

value = 10;
value = true;
```

---

## Unknown

Version sécurisée de any.

```typescript
let value: unknown;

value = "hello";
value = 10;
```

Impossible d'utiliser directement :

```typescript
value.toUpperCase(); // Erreur
```

Il faut vérifier :

```typescript
if (typeof value === "string") {
  console.log(value.toUpperCase());
}
```

---

## Never

Fonction qui ne retourne jamais.

```typescript
function throwError(message: string): never {
  throw new Error(message);
}
```

---

# 5. Arrays

```typescript
const users: string[] = ["John", "Jane"];
```

ou

```typescript
const users: Array<string> = ["John", "Jane"];
```

---

## Array d'objets

```typescript
const products: {
  id: number;
  name: string;
}[] = [
  {
    id: 1,
    name: "Laptop"
  }
];
```

---

# 6. Tuples

Ordre fixe.

```typescript
let user: [number, string];

user = [1, "John"];
```

---

# 7. Enums

## Enum classique

```typescript
enum UserRole {
  ADMIN,
  USER,
  GUEST
}
```

Valeurs :

```typescript
UserRole.ADMIN // 0
UserRole.USER  // 1
```

---

## Enum string

```typescript
enum Status {
  SUCCESS = "SUCCESS",
  ERROR = "ERROR"
}
```

Très utilisé dans Angular.

---

# 8. Literal Types

```typescript
let direction: "left" | "right";

direction = "left";
direction = "right";
```

Impossible :

```typescript
direction = "top";
```

---

# 9. Union Types

```typescript
let value: string | number;

value = "hello";
value = 10;
```

---

Exemple Angular :

```typescript
function display(id: string | number) {
  console.log(id);
}
```

---

# 10. Intersection Types

Fusion de types.

```typescript
type Person = {
  name: string;
};

type Employee = {
  salary: number;
};

type Worker = Person & Employee;
```

Résultat :

```typescript
const worker: Worker = {
  name: "John",
  salary: 3000
};
```

---

# 11. Type Alias

```typescript
type User = {
  id: number;
  name: string;
};
```

Utilisation :

```typescript
const user: User = {
  id: 1,
  name: "John"
};
```

---

# 12. Interfaces

Très utilisées dans Angular.

```typescript
interface User {
  id: number;
  name: string;
}
```

---

## Extension

```typescript
interface Person {
  name: string;
}

interface Employee extends Person {
  salary: number;
}
```

---

# 13. Classes

```typescript
class User {
  name: string;

  constructor(name: string) {
    this.name = name;
  }
}
```

---

## Utilisation

```typescript
const user = new User("John");
```

---

# 14. Modificateurs d'accès

## Public

Par défaut.

```typescript
class User {
  public name: string;
}
```

---

## Private

```typescript
class User {
  private password: string;
}
```

Impossible depuis l'extérieur.

---

## Protected

Accessible dans les classes filles.

```typescript
class User {
  protected age: number;
}
```

---

## Readonly

```typescript
class User {
  readonly id: number = 1;
}
```

Impossible à modifier.

---

# 15. Héritage

```typescript
class Animal {
  move() {
    console.log("moving");
  }
}

class Dog extends Animal {
  bark() {
    console.log("woof");
  }
}
```

---

# 16. Classes abstraites

```typescript
abstract class Shape {
  abstract calculateArea(): number;
}
```

---

Implémentation :

```typescript
class Circle extends Shape {
  calculateArea(): number {
    return 10;
  }
}
```

---

# 17. Generics

Indispensable en Angular.

---

Sans generic :

```typescript
function identity(value: any) {
  return value;
}
```

---

Avec generic :

```typescript
function identity<T>(value: T): T {
  return value;
}
```

---

Utilisation :

```typescript
identity<string>("hello");
identity<number>(10);
```

---

# 18. Fonctions

```typescript
function add(a: number, b: number): number {
  return a + b;
}
```

---

# 19. Paramètres avancés

## Optionnel

```typescript
function greet(name?: string) {
  console.log(name);
}
```

---

## Valeur par défaut

```typescript
function greet(name = "Anonymous") {
  console.log(name);
}
```

---

# 20. Arrow Functions

```typescript
const add = (a: number, b: number): number => {
  return a + b;
};
```

---

Version courte :

```typescript
const add = (a: number, b: number) => a + b;
```

---

# 21. Typage des objets

```typescript
const user: {
  id: number;
  name: string;
} = {
  id: 1,
  name: "John"
};
```

---

# 22. Optional Chaining

Très utilisé dans Angular Templates.

```typescript
user?.address?.city
```

Equivalent :

```typescript
if (user && user.address) {
}
```

---

# 23. Nullish Coalescing

```typescript
const username = user.name ?? "Anonymous";
```

---

Différence avec ||

```typescript
0 || 100
```

Résultat :

```typescript
100
```

---

```typescript
0 ?? 100
```

Résultat :

```typescript
0
```

---

# 24. Type Assertions

```typescript
const input = document.getElementById("name") as HTMLInputElement;
```

---

Alternative :

```typescript
const input = <HTMLInputElement>document.getElementById("name");
```

---

# 25. Type Guards

```typescript
function print(value: string | number) {
  if (typeof value === "string") {
    console.log(value.toUpperCase());
  }
}
```

---

# 26. Type Narrowing

```typescript
function process(value: string | number) {
  if (typeof value === "string") {
    value.toUpperCase();
  }
}
```

TypeScript réduit automatiquement le type.

---

# 27. Utility Types

---

## Partial

```typescript
interface User {
  id: number;
  name: string;
}
```

```typescript
type UpdateUser = Partial<User>;
```

Résultat :

```typescript
{
  id?: number;
  name?: string;
}
```

---

## Required

```typescript
type UserRequired = Required<User>;
```

---

## Pick

```typescript
type UserPreview = Pick<User, "id" | "name">;
```

---

## Omit

```typescript
type UserWithoutId = Omit<User, "id">;
```

---

## Record

```typescript
type Users = Record<string, number>;
```

```typescript
const users: Users = {
  john: 1,
  jane: 2
};
```

---

# 28. Decorators

Fondamental pour Angular.

---

## Component

```typescript
@Component({
  selector: 'app-user',
  templateUrl: './user.component.html'
})
export class UserComponent {}
```

---

## Injectable

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {}
```

---

## Input

```typescript
@Input()
name!: string;
```

---

## Output

```typescript
@Output()
saved = new EventEmitter<void>();
```

---

# 29. Modules ES

Export :

```typescript
export class UserService {}
```

Import :

```typescript
import { UserService } from './user.service';
```

---

# 30. Async Await

```typescript
async function loadUser() {
  const response = await fetch('/api/users');

  return response.json();
}
```

---

# 31. Promises

```typescript
Promise<User>
```

Exemple :

```typescript
async getUser(): Promise<User> {
  return user;
}
```

---

# 32. RxJS Typé

Très important dans Angular.

```typescript
getUsers(): Observable<User[]> {
  return this.http.get<User[]>('/api/users');
}
```

---

Typage du HttpClient :

```typescript
this.http.get<User[]>('/api/users');
```

---

# 33. Interfaces Angular

DTO Backend

```typescript
export interface UserDto {
  id: number;
  firstName: string;
  lastName: string;
}
```

---

ViewModel

```typescript
export interface UserVm {
  fullName: string;
}
```

---

Transformation :

```typescript
const vm: UserVm = {
  fullName: `${dto.firstName} ${dto.lastName}`
};
```

---

# 34. keyof

```typescript
interface User {
  id: number;
  name: string;
}
```

```typescript
type UserKeys = keyof User;
```

Résultat :

```typescript
"id" | "name"
```

---

# 35. typeof

```typescript
const user = {
  id: 1,
  name: "John"
};
```

```typescript
type User = typeof user;
```

---

# 36. Indexed Access Types

```typescript
interface User {
  id: number;
  name: string;
}
```

```typescript
type UserName = User["name"];
```

Résultat :

```typescript
string
```

---

# 37. Mapped Types

```typescript
type ReadonlyUser = {
  readonly [K in keyof User]: User[K];
};
```

---

# 38. Types Conditionnels

```typescript
type IsString<T> =
  T extends string
    ? true
    : false;
```

---

Exemple :

```typescript
type Result = IsString<string>;
```

Résultat :

```typescript
true
```

---

# 39. Generics avancés

```typescript
interface ApiResponse<T> {
  data: T;
  success: boolean;
}
```

---

Exemple :

```typescript
interface User {
  id: number;
  name: string;
}
```

```typescript
const response: ApiResponse<User> = {
  data: {
    id: 1,
    name: "John"
  },
  success: true
};
```

---

# 40. TypeScript + Angular : Architecture Professionnelle

## DTO

```typescript
export interface UserDto {
  id: number;
  firstName: string;
  lastName: string;
}
```

---

## Service

```typescript
@Injectable({
  providedIn: 'root'
})
export class UserService {

  constructor(
    private http: HttpClient
  ) {}

  getUsers(): Observable<UserDto[]> {
    return this.http.get<UserDto[]>(
      '/api/users'
    );
  }
}
```

---

## Component

```typescript
@Component({
  selector: 'app-users',
  templateUrl: './users.component.html'
})
export class UsersComponent {

  users: UserDto[] = [];

  constructor(
    private userService: UserService
  ) {}

  ngOnInit(): void {
    this.userService
      .getUsers()
      .subscribe(users => {
        this.users = users;
      });
  }
}
```

---

# Les 15 Concepts TypeScript les Plus Importants en Angular

1. Interfaces
2. Type Aliases
3. Generics
4. Observable<T>
5. HttpClient<T>
6. Union Types
7. Optional Chaining
8. Nullish Coalescing
9. Decorators
10. Utility Types
11. keyof
12. Partial
13. Omit
14. Record
15. Async/Await

---

# Ce qu'un développeur Angular Senior maîtrise absolument

✅ Interfaces

✅ Types complexes

✅ Generic Types

✅ RxJS Typé

✅ HttpClient<T>

✅ Type Guards

✅ Utility Types

✅ Mapped Types

✅ Conditional Types

✅ Architecture DTO/ViewModel

✅ Decorators Angular

✅ Injection de dépendances

✅ Strict Mode TypeScript

```json
{
  "compilerOptions": {
    "strict": true
  }
}
```

---

# Règle d'Or

Dans Angular moderne :

- Toujours typer les réponses HTTP.
- Toujours utiliser des interfaces pour les DTO.
- Éviter `any`.
- Préférer `unknown` à `any`.
- Utiliser les generics partout où cela apporte de la sécurité.
- Activer `strict`.
- Créer des ViewModels distincts des DTO backend.
- Exploiter pleinement l'inférence TypeScript plutôt que de sur-annoter.

Si vous maîtrisez réellement tous les concepts de ce document et savez les appliquer dans Angular avec RxJS, HttpClient, Services, Components, Guards, Interceptors et Forms, vous possédez déjà l'essentiel du niveau TypeScript attendu d'un développeur Angular Senior.
