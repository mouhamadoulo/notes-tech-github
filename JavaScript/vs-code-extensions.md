# Les extensions VS Code indispensables JavaScript-Angular-Node.js

Si tu développes principalement en **JavaScript**, **Angular** et **Node.js**, l'objectif n'est pas d'installer 100 extensions mais de construire un environnement :

* rapide ;
* cohérent ;
* automatisé ;
* capable de détecter les erreurs avant même l'exécution ;
* optimisé pour la productivité.

---

# 1. Les extensions absolument indispensables

## 1. ESLint

Extension officielle :

[ESLint pour VS Code](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint&utm_source=chatgpt.com)

### Pourquoi ?

ESLint analyse ton code en temps réel et détecte :

* erreurs de syntaxe ;
* variables inutilisées ;
* mauvaises pratiques ;
* problèmes de qualité.

Exemple :

```js
const user = "John";

console.log(users);
```

ESLint signalera immédiatement :

```txt
users is not defined
```

### Configuration recommandée

Dans `settings.json`

```json
{
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  }
}
```

À chaque sauvegarde :

```txt
CTRL + S
```

→ correction automatique.

---

## Prettier

Extension :

[Prettier - Code Formatter](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode&utm_source=chatgpt.com)

### Pourquoi ?

Elle uniformise le code :

Avant :

```js
const user={name:"John",age:25}
```

Après :

```js
const user = {
  name: "John",
  age: 25,
};
```

### Configuration

```json
{
  "editor.formatOnSave": true,
  "editor.defaultFormatter": "esbenp.prettier-vscode"
}
```

---

# 2. Angular : les extensions indispensables

## Angular Language Service

Extension officielle :

[Angular Language Service](https://marketplace.visualstudio.com/items?itemName=Angular.ng-template&utm_source=chatgpt.com)

### Ce qu'elle apporte

Dans les templates HTML :

```html
<button (click)="saveUser()">
```

VS Code comprend :

* les composants ;
* les services ;
* les pipes ;
* les directives ;
* les types TypeScript.

Tu obtiens :

* autocomplétion ;
* navigation ;
* détection d'erreurs.

---

## Angular Snippets

Extension :

[Angular Snippets (Version 17)](https://marketplace.visualstudio.com/items?itemName=johnpapa.Angular2&utm_source=chatgpt.com)

### Exemples

Tape :

```txt
a-component
```

Puis TAB

```ts
import { Component } from '@angular/core';

@Component({
  selector: 'app-example',
  templateUrl: './example.component.html'
})
export class ExampleComponent {}
```

Gain énorme sur les projets Angular volumineux.

---

# 3. Git : indispensable pour tout développeur

## GitLens

Extension :

[GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens&utm_source=chatgpt.com)

### Pourquoi ?

Dans un fichier :

```ts
this.userService.save();
```

GitLens affiche :

```txt
Modifié par Pierre il y a 3 jours
```

Tu peux :

* voir l'historique ;
* comparer les versions ;
* naviguer dans les commits.

Très utile en équipe.

---

# 4. Productivité JavaScript / TypeScript

## Error Lens

Extension :

[Error Lens](https://marketplace.visualstudio.com/items?itemName=usernamehw.errorlens&utm_source=chatgpt.com)

### Sans Error Lens

L'erreur apparaît en bas.

### Avec Error Lens

L'erreur apparaît directement :

```js
console.log(users);
            ^^^^^
```

Tu la vois immédiatement.

---

## Import Cost

Extension :

[Import Cost](https://marketplace.visualstudio.com/items?itemName=wix.vscode-import-cost&utm_source=chatgpt.com)

Exemple :

```ts
import _ from "lodash";
```

Affiche :

```txt
69 KB
```

Très utile pour surveiller le poids du bundle Angular.

---

## Path Intellisense

Extension :

[Path Intellisense](https://marketplace.visualstudio.com/items?itemName=christian-kohler.path-intellisense&utm_source=chatgpt.com)

Quand tu écris :

```js
import User from "./
```

VS Code propose automatiquement :

```js
import User from "./models/User";
```

---

# 5. Développement Node.js

## npm Intellisense

Extension :

[npm Intellisense](https://marketplace.visualstudio.com/items?itemName=christian-kohler.npm-intellisense&utm_source=chatgpt.com)

Exemple :

```js
import ex
```

Proposition :

```js
import express from "express";
```

---

## REST Client

Extension :

[REST Client](https://marketplace.visualstudio.com/items?itemName=humao.rest-client&utm_source=chatgpt.com)

### Pourquoi ?

Tester les APIs sans ouvrir Postman.

Fichier :

```http
GET http://localhost:3000/users
```

Puis :

```txt
Send Request
```

Résultat directement dans VS Code.

Excellent pour Node.js.

---

# 6. Docker (si tu fais du backend moderne)

## Docker

Extension officielle :

[Docker Extension for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-docker&utm_source=chatgpt.com)

Permet :

* voir les containers ;
* lancer les containers ;
* arrêter les containers ;
* consulter les logs.

Indispensable si tu utilises :

* NestJS ;
* Node.js ;
* PostgreSQL ;
* Redis ;
* MongoDB.

---

# 7. Bases de données

## SQLTools

Extension :

[SQLTools](https://marketplace.visualstudio.com/items?itemName=mtxr.sqltools&utm_source=chatgpt.com)

Compatible :

* PostgreSQL ;
* MySQL ;
* MariaDB ;
* SQL Server ;
* SQLite.

Tu peux exécuter des requêtes directement dans VS Code.

---

## MongoDB for VS Code

Extension :

[MongoDB for VS Code](https://marketplace.visualstudio.com/items?itemName=mongodb.mongodb-vscode&utm_source=chatgpt.com)

Très pratique pour :

* visualiser les collections ;
* exécuter des requêtes ;
* inspecter les documents.

---

# 8. Intelligence artificielle

## GitHub Copilot

Extension :

[GitHub Copilot](https://github.com/features/copilot?utm_source=chatgpt.com)

Très utile pour :

* Angular ;
* Node.js ;
* TypeScript ;
* Tests unitaires.

Exemple :

```ts
function calculateVat(price)
```

Copilot peut générer toute la fonction.

---

## ChatGPT VS Code

Extension officielle :

[OpenAI](https://openai.com?utm_source=chatgpt.com)

Pratique pour :

* expliquer du code ;
* générer des tests ;
* refactoriser ;
* documenter.

---

# 9. Les extensions que je recommande aux développeurs seniors

## Todo Tree

[Todo Tree](https://marketplace.visualstudio.com/items?itemName=Gruntfuggly.todo-tree&utm_source=chatgpt.com)

Repère :

```js
// TODO
// FIXME
// BUG
```

dans tout le projet.

---

## Code Spell Checker

[Code Spell Checker](https://marketplace.visualstudio.com/items?itemName=streetsidesoftware.code-spell-checker&utm_source=chatgpt.com)

Évite les fautes :

```ts
getCustumer()
```

au lieu de

```ts
getCustomer()
```

---

## Better Comments

[Better Comments](https://marketplace.visualstudio.com/items?itemName=aaron-bond.better-comments&utm_source=chatgpt.com)

Coloration :

```js
// TODO
// !
```

Très pratique sur les gros projets.

---

# 10. Configuration VS Code idéale pour Angular / Node.js

Dans `settings.json`

```json
{
  "editor.formatOnSave": true,
  "editor.codeActionsOnSave": {
    "source.fixAll.eslint": true
  },

  "editor.tabSize": 2,

  "files.autoSave": "onFocusChange",

  "editor.minimap.enabled": false,

  "editor.inlineSuggest.enabled": true,

  "editor.bracketPairColorization.enabled": true,

  "explorer.compactFolders": false,

  "typescript.updateImportsOnFileMove.enabled": "always",

  "javascript.updateImportsOnFileMove.enabled": "always"
}
```

---

# Mon stack VS Code personnel recommandé (le "Top 15")

Si je devais configurer une machine Angular/Node.js aujourd'hui, j'installerais dans cet ordre :

1. ESLint
2. Prettier
3. Angular Language Service
4. GitLens
5. Error Lens
6. Import Cost
7. Path Intellisense
8. npm Intellisense
9. REST Client
10. Docker
11. SQLTools
12. MongoDB for VS Code
13. GitHub Copilot
14. Todo Tree
15. Better Comments

Avec seulement ces 15 extensions, tu couvres déjà plus de 95 % des besoins d'un développeur JavaScript / Angular / Node.js professionnel.
