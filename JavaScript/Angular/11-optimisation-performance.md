
# Optimisation des performances en Angular (versions > 18)

> Tutoriel complet et pratique pour Angular 19, 20 et 21. Chaque section explique **le problème**, **la solution** et **le pourquoi**, avec du code commenté.

## Table des matières

1. [Contexte : ce qui change après Angular 18](#1-contexte--ce-qui-change-après-angular-18)
2. [La stratégie `OnPush`](#2-la-stratégie-onpush)
3. [Les Signals : la nouvelle réactivité](#3-les-signals--la-nouvelle-réactivité)
4. [Le mode Zoneless](#4-le-mode-zoneless)
5. [Le nouveau control flow et `@for` avec `track`](#5-le-nouveau-control-flow-et-for-avec-track)
6. [Les vues différées : `@defer`](#6-les-vues-différées--defer)
7. [Lazy loading des routes et des composants](#7-lazy-loading-des-routes-et-des-composants)
8. [Optimisation des images : `NgOptimizedImage`](#8-optimisation-des-images--ngoptimizedimage)
9. [SSR et hydratation incrémentale](#9-ssr-et-hydratation-incrémentale)
10. [Virtual scrolling pour les longues listes](#10-virtual-scrolling-pour-les-longues-listes)
11. [Pipes purs et mémoïsation](#11-pipes-purs-et-mémoïsation)
12. [Analyse et réduction du bundle](#12-analyse-et-réduction-du-bundle)
13. [Checklist finale](#13-checklist-finale)

---

## 1. Contexte : ce qui change après Angular 18

À partir d'Angular 19/20/21, l'optimisation des performances repose sur trois piliers nouveaux par rapport aux versions précédentes :

| Pilier | Version de stabilisation | Bénéfice principal |
|--------|--------------------------|--------------------|
| **Signals** | Stables en v20 | Détection de changement granulaire, sans `Zone.js` |
| **Zoneless change detection** | Stable en v20.2 (`provideZonelessChangeDetection()`), **par défaut en v21** | Moins de cycles de détection, bundle plus léger |
| **Hydratation incrémentale** | v19+ | Meilleur *Time To Interactive* en SSR |

L'idée directrice : **dire explicitement à Angular quand et où le DOM doit changer**, au lieu de le laisser tout vérifier « au cas où ».

---

## 2. La stratégie `OnPush`

### Le problème

Par défaut (`ChangeDetectionStrategy.Default`), Angular re-vérifie **tout l'arbre de composants** à chaque évènement asynchrone. Sur une application complexe, c'est la première cause de lenteur.

### La solution

`OnPush` indique à Angular de ne re-vérifier un composant que dans 4 cas :

- la **référence** d'un `@Input()` change (d'où l'importance de l'immutabilité) ;
- un évènement provient du composant ou de l'un de ses enfants ;
- un `signal` lu dans le template change ;
- on appelle explicitement `markForCheck()`.

```typescript
import { ChangeDetectionStrategy, Component, input } from '@angular/core';

@Component({
  selector: 'app-user-card',
  changeDetection: ChangeDetectionStrategy.OnPush, // ← la clé de l'optimisation
  template: `
    <article>
      <h3>{{ user().name }}</h3>
      <p>{{ user().email }}</p>
    </article>
  `,
})
export class UserCardComponent {
  // input() (signal input) déclenche OnPush automatiquement quand la référence change
  user = input.required<{ name: string; email: string }>();
}
```

### Le piège de l'immutabilité

Avec `OnPush`, **muter** un objet ne déclenche rien, car la référence ne change pas. Il faut créer une nouvelle référence :

```typescript
// ❌ NE déclenche PAS la détection (même référence d'objet)
this.user.name = 'Alice';

// ✅ Déclenche la détection (nouvelle référence)
this.user = { ...this.user, name: 'Alice' };
```

> **Recommandation :** activez `OnPush` sur **tous** vos composants par défaut. Couplé aux Signals, c'est quasiment sans effort.

---

## 3. Les Signals : la nouvelle réactivité

Les Signals sont des variables réactives. Quand leur valeur change, **seuls les emplacements du template qui les utilisent** sont mis à jour — pas tout le composant.

### `signal`, `computed`, `effect`

```typescript
import { Component, signal, computed, effect } from '@angular/core';

@Component({
  selector: 'app-cart',
  changeDetection: ChangeDetectionStrategy.OnPush,
  template: `
    <p>Articles : {{ count() }}</p>
    <p>Total : {{ total() }} €</p>
    <button (click)="add()">Ajouter</button>
  `,
})
export class CartComponent {
  // 1. signal() : état source, modifiable
  private price = signal(10);
  count = signal(0);

  // 2. computed() : valeur DÉRIVÉE, recalculée uniquement si une dépendance change.
  //    Le résultat est mémoïsé : aucun recalcul si count et price n'ont pas bougé.
  total = computed(() => this.count() * this.price());

  constructor() {
    // 3. effect() : exécute un effet de bord quand une dépendance change.
    //    Utile pour logs, localStorage, analytics — JAMAIS pour dériver un état (utilisez computed).
    effect(() => console.log(`Total mis à jour : ${this.total()} €`));
  }

  add(): void {
    // update() reçoit l'ancienne valeur et renvoie la nouvelle (immutabilité garantie)
    this.count.update((c) => c + 1);
  }
}
```

### Pourquoi c'est plus rapide

Avec un `computed`, si vous lisez `total()` dix fois dans le template mais que ni `count` ni `price` n'ont changé, **le calcul n'est exécuté qu'une seule fois** puis mis en cache. Comparez à un getter classique `get total()` qui se ré-exécute à chaque cycle de détection.

```typescript
// ❌ Getter : recalculé à CHAQUE détection de changement
get total() { return this.count * this.price; }

// ✅ computed : recalculé UNIQUEMENT quand count ou price change
total = computed(() => this.count() * this.price());
```

### `linkedSignal` (Angular 19+)

Un état modifiable **mais** qui se réinitialise quand une source change — idéal pour une sélection liée à une liste rechargée :

```typescript
import { signal, linkedSignal } from '@angular/core';

const options = signal(['S', 'M', 'L']);

// choice est modifiable par l'utilisateur, mais se réinitialise
// automatiquement sur le 1er élément si la liste options change.
const choice = linkedSignal(() => options()[0]);

choice.set('L');          // l'utilisateur choisit L
options.set(['XL', 'XXL']); // la liste change → choice revient à 'XL'
```

### Convertir un Observable RxJS en Signal

Pour migrer progressivement sans tout réécrire :

```typescript
import { toSignal } from '@angular/core/rxjs-interop';

// data() est un signal qui reflète la dernière valeur émise par l'Observable
data = toSignal(this.http.get<User[]>('/api/users'), { initialValue: [] });
```

---

## 4. Le mode Zoneless

### Le problème de `Zone.js`

Historiquement, `Zone.js` « monkey-patche » `setTimeout`, les promesses, les évènements DOM, etc. À chaque opération asynchrone terminée, il prévient Angular que « quelque chose a peut-être changé », ce qui force une détection sur **tout l'arbre**. C'est imprécis, plus lourd en bundle et difficile à déboguer.

### La solution

En **Angular 20.2+**, `provideZonelessChangeDetection()` est stable. La détection ne se déclenche plus que sur des signaux explicites : écriture d'un signal, évènement de template, `async` pipe, navigation du routeur.

```typescript
// main.ts (Angular 20)
import { bootstrapApplication } from '@angular/platform-browser';
import {
  provideZonelessChangeDetection,
  provideBrowserGlobalErrorListeners,
} from '@angular/core';
import { AppComponent } from './app/app.component';

bootstrapApplication(AppComponent, {
  providers: [
    provideZonelessChangeDetection(),     // ← supprime la dépendance à Zone.js
    provideBrowserGlobalErrorListeners(), // recommandé en zoneless
  ],
});
```

### Étapes de migration

1. Remplacer `provideZoneChangeDetection({ eventCoalescing: true })` par `provideZonelessChangeDetection()`.
2. **Supprimer le polyfill `zone.js`** de la section `polyfills` dans `angular.json` (sinon le gain de bundle est perdu).
3. S'assurer que l'état piloté par le template passe par des **signals** ou l'`async` pipe.

> **Note version :** en **Angular 21+**, le mode zoneless est le **défaut** pour les nouveaux projets (`ng new`). Vérifiez simplement que `provideZoneChangeDetection` n'est utilisé nulle part.

### Piège fréquent

En zoneless, un `setTimeout` qui modifie une propriété de classe « normale » ne rafraîchira plus l'UI. Convertissez l'état en signal :

```typescript
// ❌ En zoneless, l'UI ne se met pas à jour
setTimeout(() => { this.message = 'Fini'; }, 1000);

// ✅ L'écriture du signal planifie la détection
private message = signal('');
setTimeout(() => this.message.set('Fini'), 1000);
```

---

## 5. Le nouveau control flow et `@for` avec `track`

Depuis Angular 17, le control flow intégré (`@if`, `@for`, `@switch`) remplace `*ngIf`, `*ngFor`, `*ngSwitch` (**dépréciés en v20**). Il est plus rapide et active le tree-shaking du moteur.

### `@for` et l'expression `track` (obligatoire)

```html
<!-- track identifie chaque élément de façon stable.
     Angular réutilise alors les nœuds DOM existants au lieu de tout reconstruire. -->
@for (product of products(); track product.id) {
  <app-product-card [product]="product" />
} @empty {
  <p>Aucun produit.</p>
}
```

### Pourquoi `track` est critique

Sans identité stable, modifier ou réordonner une liste force Angular à **détruire et recréer** tous les nœuds DOM. Avec `track product.id`, seuls les éléments réellement modifiés sont touchés.

```html
<!-- ❌ track $index : si on insère en tête, tous les index décalent → tout est re-rendu -->
@for (item of items(); track $index) { ... }

<!-- ✅ track item.id : identité stable, DOM réutilisé au maximum -->
@for (item of items(); track item.id) { ... }
```

> N'utilisez `track $index` que pour des listes **statiques** de primitives qui ne sont jamais réordonnées.

### Migration automatique

```bash
ng generate @angular/core:control-flow
```

---

## 6. Les vues différées : `@defer`

### Le problème

Charger tout le JavaScript d'une page d'un coup retarde l'interactivité. Certains composants (carte, graphique, commentaires) ne sont pas visibles immédiatement.

### La solution

`@defer` charge un bloc — **et son code JS** — de manière paresseuse, selon un déclencheur. C'est l'un des leviers les plus puissants pour le *Time To Interactive*.

```html
@defer (on viewport) {
  <!-- Ce composant et sa dépendance ne sont téléchargés
       que lorsque le bloc entre dans le viewport. -->
  <app-heavy-chart [data]="stats()" />
} @placeholder (minimum 500ms) {
  <!-- Affiché AVANT le déclenchement -->
  <div class="skeleton">Chargement du graphique…</div>
} @loading (after 100ms; minimum 1s) {
  <!-- Affiché PENDANT le téléchargement -->
  <app-spinner />
} @error {
  <p>Impossible de charger le graphique.</p>
}
```

### Les déclencheurs disponibles

| Déclencheur | Quand le bloc se charge |
|-------------|--------------------------|
| `on idle` | Quand le navigateur est inactif (défaut) |
| `on viewport` | Quand le bloc devient visible |
| `on interaction` | Au clic / focus de l'utilisateur |
| `on hover` | Au survol |
| `on timer(2s)` | Après un délai |
| `when condition()` | Quand une condition (signal) est vraie |

### Pré-chargement intelligent

On peut **télécharger à l'avance** sans afficher, pour une réactivité instantanée au moment voulu :

```html
<!-- Charge dès que le navigateur est libre, mais n'affiche qu'au survol -->
@defer (on hover; prefetch on idle) {
  <app-comments />
} @placeholder {
  <button>Voir les commentaires</button>
}
```

---

## 7. Lazy loading des routes et des composants

### Routes paresseuses (composants standalone)

```typescript
// app.routes.ts
import { Routes } from '@angular/router';

export const routes: Routes = [
  {
    path: 'dashboard',
    // loadComponent crée un chunk séparé, téléchargé seulement à la navigation
    loadComponent: () =>
      import('./dashboard/dashboard.component').then((m) => m.DashboardComponent),
  },
  {
    path: 'admin',
    // loadChildren pour un groupe entier de routes
    loadChildren: () => import('./admin/admin.routes').then((m) => m.ADMIN_ROUTES),
  },
];
```

### Pré-chargement des routes

Pour anticiper les navigations probables sans pénaliser le chargement initial :

```typescript
import { provideRouter, withPreloading, PreloadAllModules } from '@angular/router';

export const appConfig = {
  providers: [
    provideRouter(routes, withPreloading(PreloadAllModules)),
  ],
};
```

> Pour un contrôle plus fin, implémentez une `PreloadingStrategy` personnalisée qui ne précharge que les routes marquées `data: { preload: true }`.

---

## 8. Optimisation des images : `NgOptimizedImage`

### Le problème

Les images mal dimensionnées et non priorisées dégradent le *Largest Contentful Paint* (LCP), métrique clé des Core Web Vitals.

### La solution

La directive `NgOptimizedImage` applique automatiquement le lazy-loading, le bon `srcset`, et avertit en cas de mauvaise pratique.

```typescript
import { NgOptimizedImage } from '@angular/common';

@Component({
  selector: 'app-hero',
  imports: [NgOptimizedImage], // standalone : import direct
  template: `
    <!-- ngSrc remplace src. width/height OBLIGATOIRES (évite le layout shift). -->
    <img ngSrc="/assets/hero.jpg" width="1200" height="600" priority alt="Bannière" />

    <!-- Image hors écran : lazy-loading automatique (pas de priority) -->
    <img ngSrc="/assets/product.jpg" width="400" height="300" alt="Produit" />
  `,
})
export class HeroComponent {}
```

Points clés :

- `priority` sur l'image LCP (la plus grande visible au chargement) → préchargement immédiat.
- `width` et `height` obligatoires → réservation de l'espace, **zéro layout shift**.
- Lazy-loading automatique pour toutes les images sans `priority`.

---

## 9. SSR et hydratation incrémentale

### Hydratation classique

Le Server-Side Rendering renvoie du HTML pré-rendu (bon pour le SEO et le premier affichage). L'hydratation « réveille » ce HTML côté client **sans le reconstruire**.

```typescript
// app.config.ts
import { provideClientHydration, withEventReplay } from '@angular/platform-browser';

export const appConfig = {
  providers: [
    // withEventReplay() rejoue les clics survenus AVANT la fin de l'hydratation
    provideClientHydration(withEventReplay()),
  ],
};
```

### Hydratation incrémentale (Angular 19+)

Au lieu d'hydrater toute la page d'un coup, on hydrate **bloc par bloc**, à la demande — en réutilisant la syntaxe `@defer`. Cela réduit drastiquement le JS exécuté au démarrage.

```typescript
import { provideClientHydration, withIncrementalHydration } from '@angular/platform-browser';

export const appConfig = {
  providers: [
    provideClientHydration(withIncrementalHydration()),
  ],
};
```

```html
<!-- Rendu sur le serveur, puis hydraté UNIQUEMENT quand il devient visible -->
@defer (hydrate on viewport) {
  <app-product-reviews [productId]="id()" />
}
```

Résultat : le contenu est immédiatement visible et indexable (SSR), mais le JavaScript associé n'est exécuté que lorsque l'utilisateur en a réellement besoin.

---

## 10. Virtual scrolling pour les longues listes

### Le problème

Afficher 10 000 lignes crée 10 000 nœuds DOM → mémoire saturée et défilement saccadé.

### La solution

Le `cdk-virtual-scroll-viewport` du CDK ne rend que les éléments **visibles** (plus une petite marge).

```typescript
import { ScrollingModule } from '@angular/cdk/scrolling';

@Component({
  selector: 'app-big-list',
  imports: [ScrollingModule],
  template: `
    <!-- itemSize : hauteur d'une ligne en px. Le viewport doit avoir une hauteur fixe. -->
    <cdk-virtual-scroll-viewport itemSize="50" class="viewport">
      <div *cdkVirtualFor="let item of items; trackBy: trackById" class="row">
        {{ item.name }}
      </div>
    </cdk-virtual-scroll-viewport>
  `,
  styles: [`.viewport { height: 400px; } .row { height: 50px; }`],
})
export class BigListComponent {
  items = Array.from({ length: 10_000 }, (_, i) => ({ id: i, name: `Item ${i}` }));
  trackById = (_: number, item: { id: number }) => item.id;
}
```

Même avec 10 000 éléments, seuls ~10 nœuds DOM existent à un instant donné.

---

## 11. Pipes purs et mémoïsation

### Le problème

Appeler une **méthode** dans un template la ré-exécute à chaque cycle de détection.

```html
<!-- ❌ getDiscount() est appelée à CHAQUE détection de changement -->
<p>{{ getDiscount(product) }}</p>
```

### La solution : un pipe pur

Un pipe pur n'est ré-exécuté **que si ses arguments d'entrée changent**, exactement comme un `computed`.

```typescript
import { Pipe, PipeTransform } from '@angular/core';

@Pipe({
  name: 'discount',
  pure: true, // pure: true est le défaut — recalcul uniquement si l'argument change
})
export class DiscountPipe implements PipeTransform {
  transform(price: number, rate: number): number {
    return price * (1 - rate);
  }
}
```

```html
<!-- ✅ Recalculé seulement si product.price ou rate change -->
<p>{{ product.price | discount: 0.2 }}</p>
```

> Pour de la logique dérivée d'un état de composant, préférez un **`computed` signal** ; pour une transformation réutilisable dans le template, un **pipe pur**. Évitez les appels de méthode directs dans les templates.

---

## 12. Analyse et réduction du bundle

Angular 20 utilise un pipeline `esbuild` qui produit des builds plus rapides et un meilleur tree-shaking. Pour aller plus loin :

### Mesurer

```bash
# Génère les statistiques de build
ng build --configuration production --stats-json

# Analyse visuelle du contenu du bundle
npx source-map-explorer dist/mon-app/browser/*.js
```

### Définir des budgets de bundle

Dans `angular.json`, faites échouer le build si le bundle dépasse un seuil :

```json
{
  "budgets": [
    {
      "type": "initial",
      "maximumWarning": "500kb",
      "maximumError": "1mb"
    },
    {
      "type": "anyComponentStyle",
      "maximumWarning": "4kb"
    }
  ]
}
```

### Bonnes pratiques de bundle

- **Importer finement** : `import { debounce } from 'lodash-es'` plutôt que tout `lodash`.
- Préférer les **composants standalone** : meilleur tree-shaking que les `NgModule`.
- Mettre les fonctionnalités lourdes derrière `loadComponent` ou `@defer`.
- En zoneless, **retirer `zone.js`** du build (≈ plusieurs Ko économisés).

---

## 13. Checklist finale

| Optimisation | Action concrète |
|--------------|-----------------|
| Détection de changement | `OnPush` partout + Signals |
| Réactivité | `signal` / `computed` au lieu de getters et de méthodes de template |
| Zone.js | Migrer vers `provideZonelessChangeDetection()` (défaut en v21) |
| Listes | `@for` avec `track item.id` (jamais `$index` sur listes dynamiques) |
| Code à la demande | `@defer` selon le contexte de visibilité/interaction |
| Routes | `loadComponent` / `loadChildren` + préchargement ciblé |
| Images | `NgOptimizedImage` + `priority` sur l'image LCP |
| SSR | `provideClientHydration(withIncrementalHydration())` |
| Longues listes | `cdk-virtual-scroll-viewport` |
| Transformations | Pipes purs plutôt qu'appels de méthode |
| Bundle | Budgets `angular.json` + `source-map-explorer` + imports fins |

> **Principe à retenir :** la performance moderne en Angular consiste à **être explicite**. Dites à Angular *quoi* est réactif (signals), *quand* charger le code (`@defer`, lazy routes) et *où* le DOM change vraiment (`track`, `OnPush`). Moins de travail implicite = application plus rapide.
