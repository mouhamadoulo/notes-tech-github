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
