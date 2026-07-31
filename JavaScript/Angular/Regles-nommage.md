Avec Angular 22 (comme Angular récent en général), les règles de nommage suivent les conventions Angular CLI. Quand tu utilises `ng generate` (`ng g`), Angular applique automatiquement ces conventions.

## 1. Composants (`component`)

### Commande

```bash
ng generate component nom-du-composant
```

ou raccourci :

```bash
ng g c nom-du-composant
```

### Convention de nommage

✅ **kebab-case** (minuscules avec tirets)

Exemples :

```bash
ng g c task-list
```

génère :

```
task-list/
├── task-list.component.ts
├── task-list.component.html
├── task-list.component.scss
└── task-list.component.spec.ts
```

Classe TypeScript :

```typescript
export class TaskListComponent {}
```

Selector :

```typescript
selector: 'app-task-list'
```

Utilisation :

```html
<app-task-list></app-task-list>
```

---

### Mauvais exemples

❌ CamelCase :

```bash
ng g c TaskList
```

Résultat possible :

```
task-list.component.ts
```

mais ce n'est pas la convention.

❌ PascalCase :

```bash
ng g c TaskListComponent
```

Ne mets jamais `Component` dans le nom :

```bash
ng g c task-list-component
```

Sinon tu obtiens :

```typescript
export class TaskListComponentComponent {}
```

---

# 2. Services (`service`)

### Commande

```bash
ng generate service nom-du-service
```

ou :

```bash
ng g s nom-du-service
```

Convention :

✅ **kebab-case**

Exemples :

```bash
ng g s task
```

génère :

```
task.service.ts
task.service.spec.ts
```

Classe :

```typescript
export class TaskService {}
```

---

Exemple :

```bash
ng g s services/task
```

Résultat :

```
src/app/services/
└── task.service.ts
```

---

# 3. Interfaces / Models

Angular CLI :

```bash
ng g interface models/task
```

Convention :

```
task.ts
```

Pas :

```
task.interface.ts
```

Classe :

```typescript
export interface Task {
  id:number;
  title:string;
  completed:boolean;
}
```

---

# 4. Guards

Commande :

```bash
ng g guard auth
```

Convention :

```
auth.guard.ts
```

Classe :

```typescript
export class AuthGuard {}
```

---

# 5. Pipes

Commande :

```bash
ng g pipe shared/currency
```

Résultat :

```
currency.pipe.ts
```

Classe :

```typescript
export class CurrencyPipe implements PipeTransform
```

---

# 6. Directives

Commande :

```bash
ng g directive shared/highlight
```

Résultat :

```
highlight.directive.ts
```

Classe :

```typescript
export class HighlightDirective {}
```

---

# 7. Structure recommandée Angular 22 (feature-based)

Pour un projet professionnel Angular 22 standalone, évite un dossier global `components/`.

Privilégie :

```
src/app/
│
├── core/
│   ├── services/
│   ├── guards/
│   ├── interceptors/
│
├── shared/
│   ├── components/
│   ├── pipes/
│   ├── directives/
│
├── features/
│   │
│   └── tasks/
│       ├── components/
│       │   ├── task-list/
│       │   │   └── task-list.component.ts
│       │   │
│       │   └── task-detail/
│       │       └── task-detail.component.ts
│       │
│       ├── services/
│       │   └── task.service.ts
│       │
│       ├── models/
│       │   └── task.ts
│       │
│       └── pages/
│           └── tasks-page/
│               └── tasks-page.component.ts
│
└── app.routes.ts
```

Commandes associées :

### Composant feature

```bash
ng g c features/tasks/components/task-list
```

Résultat :

```
features/tasks/components/task-list/
└── task-list.component.ts
```

---

### Service feature

```bash
ng g s features/tasks/services/task
```

Résultat :

```
features/tasks/services/task.service.ts
```

---

# 8. Convention pour les noms de fichiers

| Type      | Nom fichier              | Classe               |
| --------- | ------------------------ | -------------------- |
| Component | `task-list.component.ts` | `TaskListComponent`  |
| Service   | `task.service.ts`        | `TaskService`        |
| Model     | `task.ts`                | `Task`               |
| Interface | `payment-message.ts`     | `PaymentMessage`     |
| Pipe      | `date-format.pipe.ts`    | `DateFormatPipe`     |
| Guard     | `auth.guard.ts`          | `AuthGuard`          |
| Directive | `highlight.directive.ts` | `HighlightDirective` |

---

# 9. Angular 22 : utiliser les standalone components

Aujourd'hui :

```bash
ng g c features/tasks/components/task-list --standalone
```

ou configure par défaut :

```bash
ng config schematics.@schematics/angular:component.standalone true
```

Ensuite :

```bash
ng g c task-list
```

générera directement :

```typescript
@Component({
  selector: 'app-task-list',
  standalone: true,
  imports: [],
  templateUrl: './task-list.component.html'
})
export class TaskListComponent {}
```

---

# 10. Règle simple à retenir

Pour Angular 22 :

```
Commande CLI → kebab-case
Classe TS → PascalCase
Variable → camelCase
Constante → UPPER_CASE
```

Exemple complet :

```bash
ng g c payment-message-list
ng g s payment-message
ng g interface models/payment-message
```

donne :

```
payment-message-list.component.ts
PaymentMessageListComponent

payment-message.service.ts
PaymentMessageService

payment-message.ts
PaymentMessage
```

Pour ton projet **payment-messages Angular 22**, je te conseillerais exactement cette convention avec une architecture **feature-first + standalone + signals**.
