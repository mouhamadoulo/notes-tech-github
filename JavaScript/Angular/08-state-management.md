# 🧠 Gestion d’état (State Management) en Angular (v18+)
Angular moderne (≥18) propose plusieurs approches pour gérer l’état d’une application : des solutions réactives natives (Signals) aux bibliothèques robustes comme NgRx ou NGXS.


# 1. 📦 Qu’est-ce que le “State Management” ?
La gestion d’état consiste à organiser et centraliser les données partagées d’une application.

**Exemple d’état :**
- utilisateur connecté
- panier e-commerce
- liste de tâches
- filtres de recherche
- données API

Sans gestion d’état → duplication, incohérences, bugs difficiles à tracer.


# 2. 🧱 Les concepts fondamentaux

## 🔹 State (état)
C’est la source de vérité de l’application.
```typescript
interface AppState {
  user: User | null;
  todos: Todo[];
  loading: boolean;
}
```
👉 Le state est immuable (on ne le modifie jamais directement).


## 🔹 Actions
Une action décrit un événement.

Exemple NgRx :
```typescript
import { createAction, props } from '@ngrx/store';

export const loadTodos = createAction('[Todo] Load Todos');

export const loadTodosSuccess = createAction(
  '[Todo] Load Success',
  props<{ todos: Todo[] }>()
);
```
👉 Une action = intention (“je veux charger les todos”)


## 🔹 Reducers
Un reducer est une fonction pure qui transforme le state.
```typescript
import { createReducer, on } from '@ngrx/store';

export const initialState: AppState = {
  user: null,
  todos: [],
  loading: false,
};

export const appReducer = createReducer(
  initialState,

  on(loadTodos, (state) => ({
    ...state,
    loading: true,
  })),

  on(loadTodosSuccess, (state, { todos }) => ({
    ...state,
    todos,
    loading: false,
  }))
);
```
👉 Règle importante :<br>
✔ pas de mutation<br>
✔ toujours retourner un nouvel objet


## 🔹 Selectors
Les selectors permettent d’extraire des morceaux du state.
```typescript
import { createSelector } from '@ngrx/store';

export const selectAppState = (state: any) => state.app;

export const selectTodos = createSelector(
  selectAppState,
  (state) => state.todos
);
```
👉 Avantages :<br>
- réutilisation
- performance (mémoïsation)
- séparation logique/UI

## 🔹 Effects
Les effects gèrent les effets secondaires (API, localStorage, etc.)
```typescript
import { Actions, createEffect, ofType } from '@ngrx/effects';
import { Injectable } from '@angular/core';
import { map, mergeMap } from 'rxjs/operators';

@Injectable()
export class TodoEffects {
  loadTodos$ = createEffect(() =>
    this.actions$.pipe(
      ofType(loadTodos),
      mergeMap(() =>
        this.todoService.getTodos().pipe(
          map((todos) => loadTodosSuccess({ todos }))
        )
      )
    )
  );

  constructor(
    private actions$: Actions,
    private todoService: TodoService
  ) {}
}
```
👉 Les effects remplacent la logique API dans les composants.

# 3. 🧰 Les outils de State Management

## 🟢 3.1 NgRx (le plus populaire enterprise)

👉 **NgRx**

**Architecture :**
- Store
- Actions
- Reducers
- Selectors
- Effects
  
**Exemple d’utilisation dans un composant :**
```typescript
@Component({...})
export class TodoComponent {
  todos$ = this.store.select(selectTodos);

  constructor(private store: Store) {}

  load() {
    this.store.dispatch(loadTodos());
  }
}
```
**Avantages :**
- très scalable
- pattern Redux strict
- idéal grandes apps

**Inconvénients :**
- verbose
- courbe d’apprentissage élevée

## 🟡 3.2 NGXS (plus simple)

👉 **NGXS**

**Exemple :**
```typescript
@State<TodoStateModel>({
  name: 'todos',
  defaults: {
    items: [],
  },
})
@Injectable()
export class TodoState {

  @Selector()
  static getTodos(state: TodoStateModel) {
    return state.items;
  }

  @Action(AddTodo)
  add(ctx: StateContext<TodoStateModel>, action: AddTodo) {
    const state = ctx.getState();
    ctx.setState({
      ...state,
      items: [...state.items, action.todo],
    });
  }
}
```

**Avantages :**
- plus simple que NgRx
- moins de boilerplate

**Inconvénients :**
- moins standard en entreprise

## 🟣 3.3 Angular Signals (nouvelle approche moderne)

👉 **Angular**<br>
Depuis Angular 16+, et encore plus en v18+, les Signals sont devenus une alternative native.

**Exemple simple :**
```typescript
import { signal, computed } from '@angular/core';

const todos = signal<Todo[]>([]);

const todoCount = computed(() => todos().length);
```

**Mise à jour du state :**
```typescript
todos.set([...todos(), newTodo]);
```
ou
```typescript
todos.update((list) => [...list, newTodo]);
```

🔥 **Exemple complet avec service**
```typescript
@Injectable({ providedIn: 'root' })
export class TodoStore {
  private todos = signal<Todo[]>([]);

  readonly todosList = this.todos.asReadonly();

  add(todo: Todo) {
    this.todos.update((list) => [...list, todo]);
  }

  remove(id: number) {
    this.todos.update((list) => list.filter(t => t.id !== id));
  }
}
```

🔥 **Dans un composant**
```typescript
@Component({...})
export class TodoComponent {
  constructor(public store: TodoStore) {}
}
```
```html
<ul>
  <li *ngFor="let todo of store.todosList()">
    {{ todo.title }}
  </li>
</ul>
```

**Avantages Signals :**
- natif Angular
- très performant
- simple
- moins de boilerplate

**Inconvénients :**
- encore jeune pour gros architectures complexes
- pas encore un standard enterprise comme NgRx

# 4. ⚖️ Comparaison rapide
| Solution | Complexité | Scénario idéal          |
| -------- | ---------- | ----------------------- |
| NgRx     | 🔴 élevée  | grandes apps enterprise |
| NGXS     | 🟠 moyenne | apps moyennes           |
| Signals  | 🟢 faible  | apps modernes Angular   |

# 5. 🧠 Comment choisir ?

👉 **Choisir NgRx si :**
- architecture complexe
- équipe large
- besoin de rigueur forte

👉 **Choisir NGXS si :**
- tu veux Redux mais simple

👉 **Choisir Signals si :**
- projet moderne Angular v18+
- tu veux simplicité + performance
- architecture légère à moyenne

# 6. 🚀 Bonnes pratiques
- ne jamais muter le state
- séparer UI / logique métier
- utiliser selectors au lieu d’accès direct
- éviter de sur-architecturer avec NgRx
- préférer Signals pour nouveaux projets simples

# 7. 🧩 Conclusion

Angular moderne propose 3 niveaux de gestion d’état :
- 🧱 NgRx → robuste et strict
- ⚡ NGXS → simplifié
- 🧠 Signals → natif et moderne
