# La Programmation Asynchrone en JavaScript

## 🚀 1. Pourquoi la programmation asynchrone existe en JavaScript ?
JavaScript est monothread = il n’exécute qu’une seule chose à la fois.
```
=> Si une opération est lente (lecture de fichier, appel API, requête BDD), elle bloquerait tout le programme.
```
L’asynchrone sert à :<br>
✔️ ne pas bloquer le programme<br>
✔️ exécuter des tâches en arrière-plan<br>
✔️ continuer le code pendant que d’autres opérations se terminent


## 🧠 2. Le principe de l’Event Loop
La magie vient de l’event loop :
- Le code rapide se fait dans le thread principal
- Le code lent (I/O, timers, requêtes réseau) est délégué à des API de l’environnement (Node, navigateur)
- Quand c’est terminé, le résultat revient dans une file d’attente
- Le moteur JS traite la tâche quand il est libre
```
📌 C’est comme demander une pizza : tu la commandes, tu n’attends pas devant le four, tu continues ta vie, et quand c’est prêt on t’appelle.
```

## 🪢 3. Les Callbacks (1ère façon de faire de l’asynchrone)
Un callback est une fonction passée en argument à une autre fonction.<br>
Il est exécuté quand la tâche asynchrone est terminée.<br>
**Exemple :** 
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

❌ **Problème : Callback Hell**<br>
Lorsque les callbacks s’enchaînent :<br>
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
```
C'est très dur à lire, maintenir, tester → Callback Hell.
```


## 🔮 4. Les Promises (solution moderne)
Une Promise est un objet qui représente un résultat futur :
- **pending** → en attente
- **resolved** → réussite
- **rejected** → échec

**Exemple simple :**
```js
function getUser() {
   return new Promise((resolve, reject) => {
     setTimeout(() => resolve({ id: 1, name: "Alice" }), 1000);
   });
}

getUser()
  .then(user => console.log(user))
  .catch(err => console.error(err));
```
👍 **Avantages**<br>
- Plus lisible
- Chaining possible
- Gestion d’erreur centralisée


## 🌀 5. Async/Await (la syntaxe la plus moderne)
Async/Await est juste une façon plus simple d’utiliser les Promises.<br>
**Exemple :**
```js
async function run() {
  try {
    const user = await getUser(); // la fonction attend mais sans bloquer le thread !
    console.log(user);
  } catch (err) {
    console.error(err);
  }
}
run();
```
**Pourquoi c'est mieux ?**<br>
	✔️ Code plus lisible<br>
	✔️ On écrit de l’asynchrone comme du "synchrone"<br>
	✔️ try/catch pour les erreurs



**Résumé de l'Evolution de l'Asynchrone**
```
CALLBACKS  →  PROMISES  →  ASYNC/AWAIT
(2009)         (2015)         (2017)
```

| Méthode | Avantages | Inconvénients |
| --- | --- | --- |
| **Callbacks** | Simple, natif | Callback hell, difficile à maintenir |
| **Promises** | Chaining, .catch() | Code un peu verbeux |
| **Async/Await** | Lisible, simple, try/catch | Il faut bien gérer plusieurs awaits |


🎯 **En une phrase :**<br> 
	👉 Callbacks = Je te rappelle quand c’est prêt<br>
	👉 Promises = Je te promets que je te donne la réponse plus tard<br>
	👉 Async/Await = Attends la réponse mais sans bloquer l’application

