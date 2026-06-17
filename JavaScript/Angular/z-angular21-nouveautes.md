Si tu vises un niveau **senior Angular 21**, il ne suffit plus de connaître les composants, services et RxJS. Angular a beaucoup évolué vers une approche **Signals-first**, **Zoneless** et **SSR/hydratation**. ([angular.fr][1])

Voici les concepts avancés que je considère prioritaires.

# 1. Signals (indispensable)

Maîtriser :

* `signal()`
* `computed()`
* `effect()`
* `linkedSignal()`
* `resource()` et `httpResource()`
* Interop RxJS ↔ Signals (`toSignal()`, `toObservable()`)

Exemple :

```ts
count = signal(0);

doubleCount = computed(() => count() * 2);

effect(() => {
  console.log(doubleCount());
});
```

Points avancés :

* éviter les effets inutiles
* granularité des mises à jour
* architecture "signals-first"
* remplacer les patterns RxJS complexes lorsque c'est pertinent

Angular pousse fortement cette approche depuis plusieurs versions. ([Angular][2])

---

# 2. Zoneless Angular

Angular 21 fait du mode sans `zone.js` le comportement par défaut pour les nouveaux projets. ([angular.fr][1])

À maîtriser :

```ts
provideZonelessChangeDetection()
```

Comprendre :

* pourquoi ZoneJS existait
* comment les Signals remplacent une partie du mécanisme
* détection de changements explicite
* impacts sur les bibliothèques tierces

Questions fréquentes en entretien :

* différence ZoneJS vs Zoneless
* gains de performances
* migration d'une application existante

---

# 3. Change Detection avancée

Même si les Signals simplifient beaucoup les choses :

* stratégie `OnPush`
* cycles de détection
* marquage manuel
* rendu granulaire

Savoir expliquer :

```ts
ChangeDetectionStrategy.OnPush
```

et pourquoi elle reste pertinente avec les Signals. Angular envisage même d'aller vers OnPush par défaut. ([Angular][2])

---

# 4. Contrôle de flux moderne

Ne plus utiliser systématiquement :

```html
*ngIf
*ngFor
```

Mais :

```html
@if (user()) {
  <profile />
}

@for (item of items(); track item.id) {
  ...
}
```

Maîtriser :

* `@if`
* `@for`
* `@switch`
* `@let`
* tracking performant

Ces syntaxes sont devenues le standard moderne Angular. ([LIFERAY UI | Makes the Difference][3])

---

# 5. Standalone Architecture

Aujourd'hui, beaucoup de projets Angular 21 n'utilisent quasiment plus les NgModules.

Savoir construire une application avec :

```ts
standalone: true
```

et :

```ts
bootstrapApplication()
```

Comprendre :

* lazy loading standalone
* organisation feature-based
* providers locaux

---

# 6. Injection de dépendances avancée

Au-delà de :

```ts
constructor(private api: ApiService) {}
```

Maîtriser :

```ts
const api = inject(ApiService);
```

ainsi que :

* InjectionToken
* providers hiérarchiques
* environment providers
* scopes d'injection

---

# 7. SSR + Hydration

Très demandé en entreprise.

À connaître :

* rendu serveur
* SEO
* hydration
* event replay
* route-level rendering

Angular a énormément investi sur ces sujets. ([Angular][2])

Questions typiques :

* différence CSR / SSR
* quand utiliser SSR
* impacts sur les performances

---

# 8. Deferred Loading

Très important pour les performances.

```html
@defer {
  <heavy-component />
}
```

Déclenchement :

```html
@defer (on viewport)
```

ou

```html
@defer (on idle)
```

Permet de réduire drastiquement le JavaScript chargé au démarrage. ([angular.fr][1])

---

# 9. State Management moderne

Savoir quand utiliser :

* Signals seuls
* Services + Signals
* NgRx
* Signal Store

Une compétence senior est surtout de savoir **quand ne pas utiliser NgRx**.

Architecture fréquente :

```txt
Local UI State
      ↓
Signals

Business State
      ↓
Signal Store / NgRx

Backend
      ↓
HttpClient
```

---

# 10. Signal Forms

Nouvelle API de formulaires basée sur les Signals. Elle est encore en évolution mais clairement stratégique pour l'avenir du framework. ([angular.fr][1])

À surveiller :

```ts
const loginForm = form(model);
```

Comprendre :

* différences avec Reactive Forms
* migration progressive
* validation réactive

---

# 11. Testing moderne

Angular 21 adopte désormais **Vitest** comme environnement de test principal. ([angular.fr][1])

À maîtriser :

* unit tests
* component tests
* mocking
* tests Signals
* tests SSR

---

# 12. Optimisation des performances

Un senior Angular doit savoir mesurer :

* bundle size
* lazy loading
* hydration
* Lighthouse
* Web Vitals

Outils :

* Angular DevTools
* profiler
* analyse du change detection

---

# Roadmap d'apprentissage Angular 21

Je conseillerais cet ordre :

1. Standalone Components
2. Signals (`signal`, `computed`, `effect`)
3. `inject()`
4. Nouveau contrôle de flux (`@if`, `@for`)
5. OnPush + Change Detection
6. RxJS ↔ Signals
7. Deferred Loading
8. SSR + Hydration
9. Zoneless
10. State Management (Signal Store / NgRx)
11. Signal Forms
12. Architecture avancée et performance

Si tu maîtrises réellement ces 12 sujets avec des projets concrets, tu es déjà dans le niveau attendu pour des postes Angular senior en 2026.

[1]: https://angular.fr/news/angular21?utm_source=chatgpt.com "Nouveautés d'Angular 21 (19 novembre 2025) | Angular"
[2]: https://angular.dev/roadmap?utm_source=chatgpt.com "Roadmap • Angular"
[3]: https://liferayui.com/whats-new-angular-21/?utm_source=chatgpt.com "What’s New in Angular 21: Zoneless by Default, Signal, Vitest, and Modern Template Syntax | LIFERAY UI"
