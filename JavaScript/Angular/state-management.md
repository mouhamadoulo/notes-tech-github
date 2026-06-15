# 🧠 Tutoriel complet : Gestion d’état (State Management) en Angular (v18+)
Angular moderne (≥18) propose plusieurs approches pour gérer l’état d’une application : des solutions réactives natives (Signals) aux bibliothèques robustes comme NgRx ou NGXS.
---

# 1. 📦 Qu’est-ce que le “State Management” ?
La gestion d’état consiste à organiser et centraliser les données partagées d’une application.

**Exemple d’état :**
- utilisateur connecté
- panier e-commerce
- liste de tâches
- filtres de recherche
- données API

Sans gestion d’état → duplication, incohérences, bugs difficiles à tracer.
---

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
---

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
---

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

---

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

---

