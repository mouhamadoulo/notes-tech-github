Voici un **tutoriel complet et structuré** sur la “trinité du web” : **HTML5 + CSS3 + JavaScript**. L’objectif est de te donner une vision claire de leur rôle, comment ils s’articulent, et des exemples concrets pour construire une page web moderne.

---

# 🧠 1. Comprendre la trinité du web

Une page web repose sur 3 technologies complémentaires :

* **HTML5** → structure (le squelette)
* **CSS3** → style (l’apparence)
* **JavaScript** → comportement (l’interactivité)

👉 Image mentale :

* HTML = maison en briques
* CSS = peinture + décoration
* JS = électricité + automatisation

---

# 🧱 2. HTML5 : la structure

## 🔹 Rôle de HTML

HTML sert à organiser le contenu :

* titres
* paragraphes
* images
* liens
* sections

---

## 🔹 Exemple de base HTML5

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Ma première page</title>
</head>
<body>

  <h1>Bienvenue sur mon site</h1>
  <p>Ceci est un paragraphe.</p>

</body>
</html>
```

---

## 🔹 Les balises essentielles

### 🧩 Structure sémantique HTML5

```html
<header>En-tête</header>
<nav>Menu</nav>
<main>Contenu principal</main>
<section>Section</section>
<article>Article indépendant</article>
<footer>Pied de page</footer>
```

👉 Pourquoi c’est important ?

* meilleur SEO (Google)
* code plus lisible
* accessibilité

---

## 🔹 Exemple complet HTML structuré

```html
<header>
  <h1>Mon blog</h1>
</header>

<nav>
  <a href="#">Accueil</a>
  <a href="#">Articles</a>
</nav>

<main>
  <article>
    <h2>Premier article</h2>
    <p>Contenu de l'article...</p>
  </article>
</main>

<footer>
  © 2026 Mon site
</footer>
```

---

# 🎨 3. CSS3 : le style

## 🔹 Rôle de CSS

CSS permet de :

* changer les couleurs
* gérer les positions
* créer des animations
* rendre responsive

---

## 🔹 Comment lier CSS à HTML

```html
<link rel="stylesheet" href="style.css">
```

---

## 🔹 Exemple CSS de base

```css
body {
  font-family: Arial;
  background-color: #f0f0f0;
}

h1 {
  color: blue;
}
```

---

## 🔹 Sélecteurs CSS

### 🎯 Par balise

```css
p {
  color: red;
}
```

### 🎯 Par classe

```css
.box {
  background-color: yellow;
}
```

HTML :

```html
<div class="box">Contenu</div>
```

### 🎯 Par ID

```css
#title {
  font-size: 30px;
}
```

---

## 🔹 Box model (très important)

Chaque élément = boîte :

```
margin → border → padding → contenu
```

Exemple :

```css
.box {
  margin: 20px;
  padding: 10px;
  border: 2px solid black;
}
```

---

## 🔹 Flexbox (indispensable)

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
}
```

HTML :

```html
<div class="container">
  <div>Bloc 1</div>
  <div>Bloc 2</div>
</div>
```

---

## 🔹 Responsive design

```css
@media (max-width: 600px) {
  body {
    background-color: lightblue;
  }
}
```

---

## 🔥 Animation simple CSS

```css
.box {
  transition: transform 0.3s;
}

.box:hover {
  transform: scale(1.2);
}
```

---

# ⚙️ 4. JavaScript : l’interactivité

## 🔹 Rôle de JavaScript

JavaScript permet de :

* modifier le HTML dynamiquement
* répondre aux clics
* créer des apps web

---

## 🔹 Lier JS à HTML

```html
<script src="script.js"></script>
```

---

## 🔹 Exemple simple

```js
console.log("Bonjour le monde");
```

---

## 🔹 Manipuler le DOM

### 🎯 Sélectionner un élément

```js
const title = document.querySelector("h1");
```

### 🎯 Modifier du texte

```js
title.textContent = "Nouveau titre";
```

---

## 🔹 Exemple interactif (clic bouton)

HTML :

```html
<button id="btn">Clique-moi</button>
<p id="text">Hello</p>
```

JS :

```js
const btn = document.querySelector("#btn");
const text = document.querySelector("#text");

btn.addEventListener("click", () => {
  text.textContent = "Bouton cliqué !";
});
```

---

## 🔹 Variables

```js
let age = 20;
const nom = "Alex";
```

---

## 🔹 Conditions

```js
if (age >= 18) {
  console.log("Adulte");
} else {
  console.log("Mineur");
}
```

---

## 🔹 Boucles

```js
for (let i = 0; i < 5; i++) {
  console.log(i);
}
```

---

## 🔹 Fonctions

```js
function addition(a, b) {
  return a + b;
}

console.log(addition(2, 3));
```

---

# 🧩 5. Projet complet (HTML + CSS + JS)

## 🎯 Objectif : un bouton qui change le style

---

## 🧱 HTML

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8">
  <title>Mini projet</title>
  <link rel="stylesheet" href="style.css">
</head>
<body>

  <h1 id="title">Page interactive</h1>

  <button id="btn">Changer couleur</button>

  <script src="script.js"></script>
</body>
</html>
```

---

## 🎨 CSS

```css
body {
  text-align: center;
  font-family: Arial;
}

.dark {
  background-color: black;
  color: white;
}
```

---

## ⚙️ JS

```js
const btn = document.querySelector("#btn");
const body = document.body;

btn.addEventListener("click", () => {
  body.classList.toggle("dark");
});
```

👉 Résultat :

* clic → mode sombre / clair

---

# 🚀 6. Comment les 3 travaillent ensemble

| Technologie | Rôle      | Exemple      |
| ----------- | --------- | ------------ |
| HTML        | Structure | `<h1>`       |
| CSS         | Style     | `color: red` |
| JS          | Action    | clic bouton  |

---

# 🧠 7. Conseils importants

## ✔️ Bonnes pratiques

* séparer HTML / CSS / JS
* nommer clairement classes et variables
* utiliser Flexbox / Grid
* écrire du code lisible

## ❌ erreurs fréquentes

* tout mettre dans HTML
* ne pas structurer le CSS
* manipuler le DOM sans logique

---

# 📈 8. Pour aller plus loin

Si tu veux progresser après ça :

* apprendre **CSS Grid**
* apprendre **Fetch API (AJAX)**
* frameworks : React / Vue
* projets : To-do list, mini jeu, portfolio

---

# 🔥 Si tu veux continuer

Je peux te faire :

* un **cours niveau débutant → avancé**
* un **projet complet type site pro**
* un **jeu en JavaScript (snake, quiz, etc.)**
* ou une **roadmap pour devenir développeur web**

Dis-moi 👍
