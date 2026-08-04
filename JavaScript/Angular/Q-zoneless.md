En Angular, **Zoneless** (ou *zoneless change detection*) est un mode de fonctionnement où Angular **n'utilise plus `zone.js` pour détecter automatiquement les changements dans l'application**.

Pour comprendre pourquoi c'est intéressant, il faut d'abord voir comment Angular fonctionne habituellement.

## Avec `zone.js` (mode classique)

Par défaut, Angular utilise **`zone.js`** pour intercepter toutes les opérations asynchrones :

* clics utilisateur
* `setTimeout`
* `Promise`
* requêtes HTTP
* événements DOM
* etc.

À chaque fois qu'un de ces événements se termine, Angular exécute automatiquement une détection des changements (*change detection*) sur l'application.

Par exemple :

```typescript
@Component({
  template: `{{ count }}`
})
export class CounterComponent {
  count = 0;

  increment() {
    this.count++;
  }
}
```

Quand on clique sur un bouton :

```html
<button (click)="increment()">+</button>
```

Angular sait automatiquement qu'il faut mettre à jour le DOM.

C'est pratique, mais cela signifie qu'Angular lance souvent une détection complète, même si très peu de choses ont réellement changé.

---

## Le principe de Zoneless

En mode **Zoneless**, Angular **n'écoute plus tous les événements du navigateur**.

Il ne s'appuie plus sur `zone.js`.

À la place, Angular met à jour la vue uniquement lorsqu'il sait explicitement qu'une donnée a changé.

Cette approche est rendue possible grâce aux nouvelles APIs réactives d'Angular, notamment les **Signals**.

Exemple :

```typescript
count = signal(0);

increment() {
    this.count.update(v => v + 1);
}
```

Le template :

```html
{{ count() }}
```

Lorsque le signal est modifié, Angular connaît exactement les composants concernés et ne met à jour que ceux-là.

---

## Pourquoi supprimer `zone.js` ?

### 1. Meilleures performances

Sans Zone.js :

* moins de travail effectué après chaque événement ;
* moins de cycles de détection inutiles ;
* meilleure scalabilité sur les grosses applications.

---

### 2. Plus prévisible

Avec Zone.js, n'importe quel événement asynchrone peut provoquer une détection.

Avec Zoneless :

> Une mise à jour ne se produit que lorsqu'on modifie explicitement un état observé.

Le comportement devient plus facile à raisonner.

---

### 3. Bundle plus léger

`zone.js` n'est plus nécessaire.

On économise quelques kilo-octets et on retire une dépendance historique d'Angular.

---

## Comment Angular sait-il quand mettre à jour ?

Angular déclenche une mise à jour lorsqu'une API réactive est utilisée.

Par exemple :

### Signals

```typescript
user = signal({
    name: 'Alice'
});

this.user.set({
    name: 'Bob'
});
```

Le composant est automatiquement rafraîchi.

---

### AsyncPipe

```html
{{ user$ | async }}
```

Lorsque l'`Observable` émet une nouvelle valeur, Angular met à jour la vue, même sans `zone.js`.

---

### `markForCheck()`

Si on travaille avec des APIs non réactives :

```typescript
constructor(private cdr: ChangeDetectorRef) {}

update() {
    this.data = ...;
    this.cdr.markForCheck();
}
```

On peut indiquer explicitement qu'une mise à jour est nécessaire.

---

## Que se passe-t-il avec `setTimeout` ?

En mode classique :

```typescript
setTimeout(() => {
    this.count++;
}, 1000);
```

La vue est mise à jour automatiquement.

En mode Zoneless, ce n'est plus garanti.

Il faut utiliser un mécanisme réactif :

```typescript
count = signal(0);

setTimeout(() => {
    this.count.update(v => v + 1);
}, 1000);
```

Le signal notifie Angular.

---

## Et avec RxJS ?

Les `Observable` restent parfaitement utilisables.

Par exemple :

```typescript
users$ = this.http.get<User[]>('/api/users');
```

Dans le template :

```html
@for (user of users$ | async; track user.id) {
    {{ user.name }}
}
```

L'`AsyncPipe` déclenche les mises à jour nécessaires.

---

## Comment activer Zoneless ?

À partir des versions récentes d'Angular (le support est devenu stable dans les versions modernes), on peut fournir un détecteur de changements zoneless lors du bootstrap de l'application :

```typescript
bootstrapApplication(AppComponent, {
  providers: [
    provideZonelessChangeDetection()
  ]
});
```

Dans ce mode, `zone.js` n'est plus requis.

---

## Est-ce qu'il faut migrer ?

Cela dépend du projet.

**Zoneless est particulièrement adapté si :**

* vous utilisez beaucoup les **Signals** ;
* vous adoptez une architecture réactive moderne ;
* vous souhaitez optimiser les performances ;
* vous démarrez un nouveau projet Angular.

**La migration peut demander plus d'attention si :**

* l'application s'appuie fortement sur des bibliothèques qui supposent la présence de `zone.js` ;
* de nombreuses mises à jour de l'état reposent sur des effets de bord asynchrones sans mécanisme réactif.

## Résumé

| Avec `zone.js`                                              | Avec Zoneless                                                                                           |
| ----------------------------------------------------------- | ------------------------------------------------------------------------------------------------------- |
| Tous les événements sont interceptés                        | Aucun événement global n'est intercepté                                                                 |
| Détection automatique après presque chaque tâche asynchrone | Détection déclenchée uniquement par des sources connues (Signals, `AsyncPipe`, appels explicites, etc.) |
| Plus simple historiquement                                  | Plus explicite et plus prévisible                                                                       |
| Peut lancer des détections inutiles                         | Met à jour uniquement ce qui est nécessaire                                                             |
| Dépend de `zone.js`                                         | Ne dépend plus de `zone.js`                                                                             |

En pratique, **Zoneless représente l'évolution du modèle de réactivité d'Angular** : au lieu de détecter les changements "au cas où" après chaque opération asynchrone, Angular met à jour l'interface lorsqu'une source réactive (comme un Signal ou un `Observable` via `AsyncPipe`) indique qu'un état a réellement changé. Cela permet de réduire le travail effectué et d'améliorer les performances, en particulier sur les applications de grande taille.
