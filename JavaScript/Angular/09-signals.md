> Les signals en Angular (introduits officiellement à partir d’Angular 16) représentent une nouvelle manière de gérer 
> l’état réactif, plus simple et plus performante que les approches précédentes.

# 🔷 1. C’est quoi un signal ?
Un signal est une variable réactive qui :
- stocke une valeur
- notifie automatiquement Angular quand elle change
- déclenche une mise à jour du DOM (sans zone.js obligatoire)

**Exemple simple :**
```typescript
import { signal } from '@angular/core';

const count = signal(0);

// lire la valeur
console.log(count());

// modifier la valeur
count.set(1);

// update basé sur l'ancien état
count.update(v => v + 1);
```

👉 Important : on lit un signal comme une fonction ```count()```.

# 🔷 2. Réactivité dérivée
Tu peux créer des valeurs dépendantes avec ```computed``` :
```typescript
import { computed } from '@angular/core';

const count = signal(2);
const double = computed(() => count() * 2);

console.log(double()); // 4
```

👉 ```double``` se met à jour automatiquement quand ```count``` change.

# 🔷 3. Effets (side effects)
```typescript
import { effect } from '@angular/core';

effect(() => {
  console.log("Count changed:", count());
});
```

👉 C’est équivalent à un abonnement automatique.

# 🔴 4. Avant les signals : comment on faisait ?
Avant Angular 16, on utilisait surtout :

### 1. RxJS (Observables)
```typescript
import { BehaviorSubject } from 'rxjs';

const count$ = new BehaviorSubject(0);

count$.next(1);

count$.subscribe(v => console.log(v));
```

👍 **Avantages :**
- puissant (streams, async, operators)
- standard dans Angular

👎 **Inconvénients :**
- verbeux
- difficile à lire pour du state simple
- nécessite unsubscribe (risque de memory leaks)

### 2. Change Detection classique (Zone.js)
Angular reposait sur Zone.js pour détecter les changements automatiquement :
- chaque événement déclenche un cycle global
- Angular vérifie tout le template

👉 **Problèmes :**
- peu performant à grande échelle
- difficile à optimiser

### 3. Input / Output + services
Beaucoup de code impératif :
```typescript
@Input() data: number;
@Output() change = new EventEmitter<number>();
```

👉 Communication parfois lourde et complexe.

# ⚖️ 5. Comparaison claire
| Critère      | Avant (RxJS / Zone.js) | Signals       |
| ------------ | ---------------------- | ------------- |
| Simplicité   | ❌ complexe           | ✅ très simple |
| Réactivité   | Observable             | fine-grained  |
| Performance  | moyenne                | excellente    |
| Boilerplate  | élevé                  | très faible   |
| Memory leaks | possible               | non           |
| Lecture code | difficile              | claire        |

# 🧠 6. Différence conceptuelle clé
Avant :<br>
👉 push global + async streams
- Angular vérifie tout
- RxJS pousse des valeurs

Avec signals :<br>
👉 réactivité fine-grained (comme React signals / SolidJS)
- Angular sait exactement quoi mettre à jour
- pas de re-render global

# 🚀 7. Quand utiliser quoi ?
✅ Signals → pour :
- state local (composants)
- UI simple et synchrone
- formulaires, compteurs, toggles

✅ RxJS → toujours utile pour :
- appels HTTP
- streams async (websocket, events)
- transformations complexes

👉 En pratique : on mélange les deux

# 🔥 8. Exemple comparaison concrète
❌ Avant (RxJS)
```typescript
count$ = new BehaviorSubject(0);

increment() {
  this.count$.next(this.count$.value + 1);
}
```

✅ Avec signal
```typescript
count = signal(0);

increment() {
  this.count.update(v => v + 1);
}
```
👉 beaucoup plus lisible.



# 🧩 Conclusion
Les signals marquent un changement majeur dans Angular :
- moins de RxJS pour le state simple
- meilleure performance
- code plus lisible
- base pour un Angular sans Zone.js


# les Signals en Angular (v18, v19, v20+)

## 1. Introduction

Les **Signals** sont le système de réactivité moderne d'Angular. Un signal est un conteneur de valeur qui **notifie automatiquement** ses consommateurs lorsque cette valeur change. Cela permet à Angular de savoir précisément *quoi* re-calculer et *quoi* re-rendre, sans dépendre de `Zone.js`.

Après avoir collecté les retours des RFC et itéré sur l'implémentation, Angular a promu `signal`, `computed`, `input` et les view queries au statut stable, puis `effect`, `linkedSignal` et `toSignal` ont été déclarés stables en v20.

### Carte des versions

| API | Statut |
|-----|--------|
| `signal`, `computed`, `input`, view queries | Stables depuis v17–v19 |
| `effect`, `linkedSignal`, `toSignal` | Stables depuis **v20** |
| `resource`, `rxResource`, `httpResource` | Expérimentales (introduites en **v19**, refondues en **v20**) |

> Ce tutoriel cible Angular **18+**, avec une emphase sur les nouveautés **v19/v20**.

---

## 2. `signal()` — l'état réactif de base

Un `WritableSignal` se lit en **appelant** la fonction (`count()`), et s'écrit avec `set()` ou `update()`.

```typescript
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-counter',
  template: `
    <p>Valeur : {{ count() }}</p>
    <button (click)="increment()">+1</button>
    <button (click)="reset()">Reset</button>
  `,
})
export class CounterComponent {
  // Création d'un signal initialisé à 0
  count = signal(0);

  increment() {
    // update() reçoit la valeur actuelle et retourne la nouvelle
    this.count.update(current => current + 1);
  }

  reset() {
    // set() remplace directement la valeur
    this.count.set(0);
  }
}
```

**Points clés :**

- Dans le template, on écrit `count()` (avec les parenthèses) : la lecture *abonne* le template au signal.
- `set(valeur)` → remplacement direct.
- `update(fn)` → transformation basée sur l'ancienne valeur.

### Signals avec objets et égalité

Par défaut, Angular utilise `Object.is` pour comparer l'ancienne et la nouvelle valeur. Pour les objets, créez toujours une **nouvelle référence** (immutabilité) :

```typescript
const user = signal({ name: 'Alice', age: 30 });

// ❌ Ne déclenche PAS de mise à jour (même référence)
user().age = 31;

// ✅ Nouvelle référence → notification correcte
user.update(u => ({ ...u, age: 31 }));
```

Vous pouvez fournir une fonction d'égalité personnalisée :

```typescript
const data = signal([1, 2, 3], {
  equal: (a, b) => a.length === b.length, // comparaison sur la longueur
});
```

---

## 3. `computed()` — état dérivé (lecture seule)

Un `computed` calcule une valeur à partir d'autres signals. Il est **paresseux** (calculé seulement à la lecture) et **mémoïsé** (recalculé uniquement si une dépendance change).

```typescript
import { Component, signal, computed } from '@angular/core';

@Component({
  selector: 'app-cart',
  template: `
    <p>Quantité : {{ quantity() }}</p>
    <p>Prix unitaire : {{ unitPrice() }} €</p>
    <p><strong>Total : {{ total() }} €</strong></p>
    <button (click)="quantity.update(q => q + 1)">Ajouter</button>
  `,
})
export class CartComponent {
  quantity = signal(1);
  unitPrice = signal(9.99);

  // total se recalcule automatiquement quand quantity OU unitPrice change
  total = computed(() => this.quantity() * this.unitPrice());
}
```

**Caractéristiques :**

- Un `computed` est **en lecture seule** : pas de `set()` ni `update()`.
- Le suivi des dépendances est **dynamique** : seules les dépendances réellement lues lors du dernier calcul sont prises en compte.

```typescript
const showAdvanced = signal(false);
const basic = signal('infos de base');
const advanced = signal('infos avancées');

const display = computed(() =>
  showAdvanced() ? advanced() : basic()
);
// Si showAdvanced() est false, modifier advanced() ne recalcule PAS display,
// car advanced() n'a pas été lu lors du dernier calcul.
```

---

## 4. `effect()` — réagir aux changements (effets de bord)

Un `effect` exécute du code **à chaque fois** qu'un de ses signals dépendants change. À utiliser pour : logs, synchronisation avec `localStorage`, appels à des API non réactives, etc.

```typescript
import { Component, signal, effect } from '@angular/core';

@Component({
  selector: 'app-theme',
  template: `<button (click)="toggle()">Changer de thème</button>`,
})
export class ThemeComponent {
  theme = signal<'light' | 'dark'>('light');

  constructor() {
    // S'exécute une fois immédiatement, puis à chaque changement de theme()
    effect(() => {
      document.body.dataset['theme'] = this.theme();
      console.log('Thème appliqué :', this.theme());
    });
  }

  toggle() {
    this.theme.update(t => (t === 'light' ? 'dark' : 'light'));
  }
}
```

### Nettoyage (cleanup)

L'effet peut enregistrer une fonction de nettoyage, appelée avant la ré-exécution suivante et à la destruction :

```typescript
effect((onCleanup) => {
  const id = setInterval(() => console.log(this.theme()), 1000);
  onCleanup(() => clearInterval(id)); // évite les fuites mémoire
});
```

**À savoir :**

- Un `effect` créé dans le constructeur est automatiquement détruit avec le composant.
- **Important :** un `effect` n'est **pas** fait pour propager de l'état entre signals. Préférez `computed` ou `linkedSignal` pour cela. Réservez `effect` aux véritables effets de bord.
- En v18, écrire dans un signal depuis un effect nécessitait `allowSignalWrites: true` ; cette option a été retirée par la suite — c'est désormais autorisé mais déconseillé.

---

## 5. Les Signal Inputs (`input()`)

Remplaçant moderne du décorateur `@Input()`, `input()` expose les entrées du composant **sous forme de signals**.

```typescript
import { Component, input } from '@angular/core';

@Component({
  selector: 'app-greeting',
  template: `<h2>Bonjour {{ name() }} ({{ upperName() }})</h2>`,
})
export class GreetingComponent {
  // input optionnel avec valeur par défaut
  name = input<string>('Inconnu');

  // input obligatoire : erreur si non fourni par le parent
  userId = input.required<number>();

  // input avec transformation (alias possible aussi)
  count = input(0, { transform: (v: string | number) => Number(v) });

  // On peut dériver directement depuis un input
  upperName = computed(() => this.name().toUpperCase());
}
```

Utilisation par le parent :

```html
<app-greeting [name]="'Alice'" [userId]="42" />
```

**Avantages :** les inputs sont des signals → utilisables directement dans `computed`, `effect`, etc. sans `ngOnChanges`.

---

## 6. `model()` — liaison bidirectionnelle (two-way binding)

`model()` crée un input **modifiable** par l'enfant, qui notifie automatiquement le parent (équivalent moderne du `[(ngModel)]` perso).

```typescript
import { Component, model } from '@angular/core';

@Component({
  selector: 'app-toggle',
  template: `
    <button (click)="value.set(!value())">
      {{ value() ? 'ON' : 'OFF' }}
    </button>
  `,
})
export class ToggleComponent {
  value = model(false); // crée un input "value" + un output "valueChange"
}
```

Le parent utilise la syntaxe banana-in-a-box :

```html
<app-toggle [(value)]="isEnabled" />
```

Ici, `isEnabled` (un signal du parent) reste synchronisé dans les deux sens.

---

## 7. `output()` — émettre des événements

`output()` est le pendant fonctionnel de `@Output()` (il n'est pas un signal, mais s'inscrit dans la même API moderne).

```typescript
import { Component, output } from '@angular/core';

@Component({
  selector: 'app-search',
  template: `<input (input)="onInput($event)" placeholder="Rechercher..." />`,
})
export class SearchComponent {
  search = output<string>();

  onInput(event: Event) {
    const value = (event.target as HTMLInputElement).value;
    this.search.emit(value); // émet vers le parent
  }
}
```

```html
<app-search (search)="onSearch($event)" />
```

---

## 8. Les Signal Queries (`viewChild`, `contentChild`)

Les requêtes de vue/contenu sont disponibles sous forme de signals (stables depuis v19).

```typescript
import { Component, viewChild, viewChildren, ElementRef, afterNextRender } from '@angular/core';

@Component({
  selector: 'app-form',
  template: `
    <input #email />
    <button #btn>Valider</button>
    <button #btn>Annuler</button>
  `,
})
export class FormComponent {
  // viewChild requis → signal non-nullable
  emailRef = viewChild.required<ElementRef>('email');

  // viewChildren → signal d'un tableau readonly
  buttons = viewChildren<ElementRef>('btn');

  constructor() {
    afterNextRender(() => {
      this.emailRef().nativeElement.focus();
      console.log('Nombre de boutons :', this.buttons().length);
    });
  }
}
```

> Lisez ces requêtes après le rendu (`afterNextRender` / `afterRenderEffect`), car l'élément n'existe pas avant.

---

## 9. `linkedSignal()` — état dérivé **modifiable** (v19/v20)

Problème résolu : un `computed` est en lecture seule. Mais parfois on veut un état qui **dérive** d'une source **tout en restant modifiable** (ex. l'option sélectionnée dans une liste qui peut changer).

Avant `linkedSignal`, gérer un état dépendant d'autres signals nécessitait souvent des contournements complexes, car `computed()` est en lecture seule.

### Forme simple

```typescript
import { signal, linkedSignal } from '@angular/core';

const options = signal(['Rouge', 'Vert', 'Bleu']);

// selected dérive de options, MAIS reste modifiable par l'utilisateur
const selected = linkedSignal(() => options()[0]);

selected.set('Bleu');   // ✅ choix manuel autorisé
options.set(['A', 'B']); // ⟳ réinitialise selected à 'A' (re-dérivation)
```

Quand `options` change, `selected` est **réinitialisé** par la fonction de calcul. Entre-temps, l'utilisateur peut le modifier librement.

### Forme avancée (`source` + `computation`)

Permet d'accéder à la **valeur précédente** pour une logique de préservation intelligente :

```typescript
const choices = signal(['Alice', 'Bob', 'Carol']);

const picked = linkedSignal({
  source: choices,
  computation: (newChoices, previous) => {
    // On garde l'ancienne sélection si elle existe toujours
    const stillThere = newChoices.find(c => c === previous?.value);
    return stillThere ?? newChoices[0];
  },
});
```

`previous` contient `{ source, value }` : l'ancienne source et l'ancienne valeur calculée.

---

## 10. La Resource API — données asynchrones (v19/v20)

`resource()` connecte une **opération asynchrone** (souvent un `fetch`) à des signals : dès qu'un signal de paramètre change, le `loader` se ré-exécute.

> ⚠️ API **expérimentale**. Le paramètre s'appelait `request` en v19 et a été renommé `params` en v20. Les exemples ci-dessous suivent la syntaxe v20.

```typescript
import { Component, signal, resource } from '@angular/core';

interface User { id: number; name: string; }

@Component({
  selector: 'app-user',
  template: `
    <input type="number" (input)="setId($event)" />

    @if (userResource.isLoading()) {
      <p>Chargement…</p>
    } @else if (userResource.error()) {
      <p>Erreur : {{ userResource.error() }}</p>
    } @else {
      <p>Utilisateur : {{ userResource.value()?.name }}</p>
    }

    <button (click)="userResource.reload()">Recharger</button>
  `,
})
export class UserComponent {
  userId = signal(1);

  userResource = resource({
    // params : recalculé à chaque changement de userId()
    params: () => ({ id: this.userId() }),

    // loader : reçoit les params + un AbortSignal pour annuler la requête précédente
    loader: async ({ params, abortSignal }): Promise<User> => {
      const res = await fetch(`https://api.example.com/users/${params.id}`, {
        signal: abortSignal,
      });
      if (!res.ok) throw new Error('Requête échouée');
      return res.json();
    },
  });

  setId(event: Event) {
    this.userId.set(Number((event.target as HTMLInputElement).value));
  }
}
```

**Signals exposés par une resource :**

| Signal / méthode | Rôle |
|------------------|------|
| `value()` | La donnée chargée |
| `status()` | `'idle' \| 'loading' \| 'resolved' \| 'error'` … |
| `isLoading()` | Booléen de chargement |
| `error()` | L'erreur éventuelle |
| `reload()` | Relance manuellement le loader |

> Depuis Angular 20, il n'est plus permis d'accéder à `value()` lorsque la resource est en état d'erreur. Vérifiez donc `error()` avant de lire la valeur.

### `rxResource()` — pour un loader basé sur RxJS

Si votre source est un `Observable` (ex. `HttpClient`) :

```typescript
import { rxResource } from '@angular/core/rxjs-interop';
import { inject } from '@angular/core';
import { HttpClient } from '@angular/common/http';

http = inject(HttpClient);

userResource = rxResource({
  params: () => ({ id: this.userId() }),
  stream: ({ params }) => this.http.get<User>(`/api/users/${params.id}`),
});
```

### `httpResource()` — le plus concis (v20)

`httpResource` enveloppe `HttpClient` dans une API réactive basée sur les signals.

```typescript
import { httpResource } from '@angular/common/http';

// La requête se relance automatiquement quand userId() change
userResource = httpResource<User>(() => `/api/users/${this.userId()}`);
```

---

## 11. Interopérabilité avec RxJS

Les signals et les `Observable` cohabitent grâce à deux utilitaires de `@angular/core/rxjs-interop`.

### `toSignal()` — Observable → Signal

```typescript
import { Component, inject } from '@angular/core';
import { toSignal } from '@angular/core/rxjs-interop';
import { interval } from 'rxjs';

@Component({
  selector: 'app-clock',
  template: `<p>Ticks : {{ ticks() }}</p>`,
})
export class ClockComponent {
  // initialValue évite un état undefined avant la première émission
  ticks = toSignal(interval(1000), { initialValue: 0 });
}
```

`toSignal` gère automatiquement la souscription **et** le désabonnement à la destruction du composant.

### `toObservable()` — Signal → Observable

```typescript
import { toObservable } from '@angular/core/rxjs-interop';
import { signal, effect } from '@angular/core';
import { debounceTime, switchMap } from 'rxjs';

query = signal('');
query$ = toObservable(this.query); // émet à chaque changement de query()

results$ = this.query$.pipe(
  debounceTime(300),
  switchMap(q => this.search(q)),
);
```

C'est l'idéal pour réutiliser les opérateurs RxJS (`debounceTime`, `switchMap`…) sur une source signal.

---

## 12. Zoneless : l'objectif final des Signals

Angular 20 apporte le support de l'architecture zoneless ; le CLI permet désormais de créer un projet sans Zone.js. Sans `Zone.js`, c'est la lecture des signals dans les templates qui pilote la détection de changements.

```typescript
import { bootstrapApplication } from '@angular/platform-browser';
import { provideZonelessChangeDetection } from '@angular/core';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, {
  providers: [
    provideZonelessChangeDetection(), // plus de Zone.js
  ],
});
```

En mode zoneless, **l'état réactif doit passer par des signals** pour que la vue se mette à jour de façon fiable.

---

## 13. Bonnes pratiques

```typescript
// ✅ Dériver avec computed plutôt que dupliquer l'état
total = computed(() => this.price() * this.qty());

// ❌ Anti-pattern : utiliser un effect pour propager de l'état
effect(() => this.total.set(this.price() * this.qty())); // évitez !

// ✅ Immutabilité pour les objets/tableaux
this.items.update(list => [...list, newItem]);

// ✅ Réserver effect() aux vrais effets de bord (DOM, logs, storage…)
effect(() => localStorage.setItem('theme', this.theme()));
```

**Règles d'or :**

1. **`computed` pour dériver**, pas `effect`.
2. **Immutabilité** systématique sur objets et tableaux.
3. **`linkedSignal`** quand un état dérivé doit aussi rester modifiable.
4. **`resource` / `httpResource`** pour l'asynchrone, plutôt qu'un `effect` + `fetch` manuel.
5. **`toSignal` / `toObservable`** pour ponter avec RxJS sans gérer les souscriptions à la main.

---

## 14. Tableau récapitulatif des API

| API | Type | Usage | Modifiable |
|-----|------|-------|:----------:|
| `signal()` | État source | Valeur de base réactive | ✅ |
| `computed()` | État dérivé | Calcul mémoïsé | ❌ |
| `linkedSignal()` | État dérivé | Dérive mais reste éditable | ✅ |
| `effect()` | Effet de bord | Réagir aux changements | — |
| `input()` | Entrée | Props du composant | ❌ |
| `model()` | Entrée/sortie | Two-way binding | ✅ |
| `output()` | Sortie | Émettre des événements | — |
| `viewChild()` / `contentChild()` | Requête | Accès aux éléments | ❌ |
| `resource()` / `httpResource()` | Asynchrone | Charger des données | (via `value`) |
| `toSignal()` / `toObservable()` | Interop RxJS | Pont signals ↔ Observables | — |

---

## 15. Pour aller plus loin

- Documentation officielle : `https://angular.dev/guide/signals`
- Guide async : `https://angular.dev/guide/signals/resource`
- Guide `linkedSignal` : `https://angular.dev/guide/signals/linked-signal`

> Les API `resource`, `rxResource` et `httpResource` étant encore **expérimentales**, vérifiez les notes de version avant un usage en production, car leur signature peut évoluer.

