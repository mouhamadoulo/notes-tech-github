# La Programmation Asynchrone en JavaScript

## 🚀 1. Pourquoi la programmation asynchrone existe en JavaScript ?

JavaScript est **monothread** : il n’exécute qu’une seule instruction à la fois.

```text
=> Si une opération est lente (lecture de fichier, appel API, requête BDD),
elle bloquerait tout le programme.
```

L’asynchrone sert à :

- ne pas bloquer le programme
- exécuter des tâches en arrière-plan
- continuer le code pendant que d’autres opérations se terminent

### Sans asynchrone

```mermaid
flowchart LR
    A[Début] --> B[Lecture fichier 5 sec]
    B --> C[Suite du programme]
```

⚠️ Pendant les 5 secondes, tout est bloqué.

### Avec asynchrone

```mermaid
flowchart LR
    A[Début] --> B[Lancement lecture fichier]
    B --> C[Suite du programme]
    B -.-> D[Lecture terminée]
    D --> E[Traitement du résultat]
```

Le programme continue pendant que l'opération se déroule en arrière-plan.

---

# 🧠 2. Le principe de l’Event Loop

La magie vient de l'**Event Loop** :

* Le code rapide s'exécute dans le thread principal
* Les tâches lentes sont déléguées à l'environnement (Node.js ou navigateur)
* Une fois terminées, elles sont placées dans une file d'attente
* L'Event Loop les réinjecte quand le thread est libre

## Architecture simplifiée

```mermaid
flowchart TD

    JS[Thread JavaScript]

    WEB[Web APIs / Node APIs<br/>Timers - Réseau - Fichiers]

    QUEUE[Callback Queue]

    LOOP[Event Loop]

    JS --> WEB
    WEB --> QUEUE
    LOOP --> QUEUE
    LOOP --> JS
```

## Exemple avec `setTimeout`

```js
console.log("A");

setTimeout(() => {
  console.log("B");
}, 0);

console.log("C");
```

### Ordre réel d'exécution

```mermaid
sequenceDiagram
    participant JS as Call Stack
    participant API as Timer API
    participant Queue as Callback Queue

    JS->>JS: console.log("A")
    JS->>API: setTimeout(...)
    JS->>JS: console.log("C")

    API-->>Queue: callback terminé

    Queue-->>JS: console.log("B")
```

Résultat :

```text
A
C
B
```

📌 Même avec `0ms`, le callback ne passe qu'après la fin du code courant.

---

# 🪢 3. Les Callbacks (1ère façon de faire de l’asynchrone)

Un callback est une fonction passée en argument à une autre fonction.

Il est exécuté lorsque la tâche asynchrone est terminée.

## Exemple

```js
function getUser(callback) {
  setTimeout(() => {
    callback(null, { id: 1, name: "Alice" });
  }, 1000);
}

getUser((err, user) => {
  if (err) return console.error(err);

  console.log(user);
});
```

### Diagramme d'exécution

```mermaid
sequenceDiagram
    participant App
    participant Timer

    App->>Timer: getUser()
    Timer-->>App: callback(user)
    App->>App: console.log(user)
```

---

## ❌ Le Callback Hell

Lorsque plusieurs opérations dépendent les unes des autres :

```js
doA(() => {
  doB(() => {
    doC(() => {
      doD(() => {
        console.log("Enfer !");
      });
    });
  });
});
```

### Représentation visuelle

```mermaid
graph TD
    A[doA]
    A --> B[doB]
    B --> C[doC]
    C --> D[doD]
    D --> E[console.log]
```

Cela devient :

* difficile à lire
* difficile à tester
* difficile à maintenir

---

# 🔮 4. Les Promises (solution moderne)

Une Promise représente une valeur qui sera disponible plus tard.

## Cycle de vie d'une Promise

```mermaid
stateDiagram-v2

    [*] --> Pending

    Pending --> Fulfilled : resolve()
    Pending --> Rejected : reject()

    Fulfilled --> [*]
    Rejected --> [*]
```

### États

* **Pending** → en attente
* **Fulfilled** → réussite
* **Rejected** → échec

---

## Exemple simple

```js
function getUser() {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve({ id: 1, name: "Alice" });
    }, 1000);
  });
}

getUser()
  .then(user => console.log(user))
  .catch(err => console.error(err));
```

### Flux d'exécution

```mermaid
flowchart TD
    A[getUser()] --> B[Promise Pending]

    B --> C[resolve()]
    B --> D[reject()]

    C --> E[then()]
    D --> F[catch()]
```

---

## Chaining

Une Promise peut être chaînée :

```js
getUser()
  .then(user => getOrders(user.id))
  .then(orders => getInvoice(orders))
  .then(invoice => console.log(invoice))
  .catch(err => console.error(err));
```

### Visualisation

```mermaid
flowchart LR
    A[getUser]
    B[getOrders]
    C[getInvoice]
    D[Affichage]

    A --> B
    B --> C
    C --> D
```

👍 Avantages :

* Plus lisible
* Chaînage naturel
* Gestion d'erreur centralisée

---

# 🌀 5. Async/Await (la syntaxe la plus moderne)

Async/Await repose entièrement sur les Promises.

## Exemple

```js
async function run() {
  try {
    const user = await getUser();

    console.log(user);
  } catch (err) {
    console.error(err);
  }
}

run();
```

---

## Que fait réellement `await` ?

```mermaid
sequenceDiagram
    participant JS as Fonction async
    participant Promise
    participant EventLoop

    JS->>Promise: await getUser()

    Promise-->>EventLoop: En attente

    EventLoop-->>JS: Promise résolue

    JS->>JS: Reprise de l'exécution
```

Important :

```text
await ne bloque PAS JavaScript.
Il suspend uniquement la fonction courante.
```

---

## Plusieurs await

### Exécution séquentielle

```js
const user = await getUser();
const orders = await getOrders();
```

```mermaid
flowchart LR
    A[getUser]
    --> B[getOrders]
```

Temps total :

```text
Temps = A + B
```

---

### Exécution parallèle

```js
const [user, orders] = await Promise.all([
  getUser(),
  getOrders()
]);
```

```mermaid
flowchart TD

    A[getUser]
    B[getOrders]

    A --> C[Promise.all]
    B --> C

    C --> D[Résultat final]
```

Temps total :

```text
Temps = max(A,B)
```

🚀 Beaucoup plus performant lorsque les tâches sont indépendantes.

---

# 📊 Résumé de l'évolution

```mermaid
timeline
    title Evolution de l'asynchrone JavaScript

    2009 : Callbacks
         : Fonction rappel

    2015 : Promises
         : Chaînage
         : Gestion erreurs

    2017 : Async/Await
         : Syntaxe moderne
         : Lisibilité maximale
```

---

# ⚖️ Comparatif

| Méthode         | Avantages                    | Inconvénients                     |
| --------------- | ---------------------------- | --------------------------------- |
| **Callbacks**   | Simple, natif                | Callback Hell                     |
| **Promises**    | Chaining, `.catch()`         | Plus verbeux                      |
| **Async/Await** | Lisible, simple, `try/catch` | Attention aux `await` séquentiels |

---

# 🎯 À retenir

```mermaid
mindmap
  root((Asynchrone JS))
    Callbacks
      Callback Hell
    Promises
      then
      catch
    Async/Await
      await
      try/catch
    Event Loop
      Queue
      Timers
      I/O
```

### En une phrase

👉 **Callbacks** = « Je te rappelle quand c'est prêt »
👉 **Promises** = « Je te promets une réponse plus tard »
👉 **Async/Await** = « J'attends le résultat sans bloquer l'application »

---

## 🏆 Règle moderne à retenir

```text
Event Loop
      ↓
Promises
      ↓
Async/Await
      ↓
Promise.all() quand plusieurs tâches sont indépendantes
```

C'est aujourd'hui la manière la plus utilisée et recommandée pour écrire du JavaScript asynchrone en production.
