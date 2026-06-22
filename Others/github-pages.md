### 🧩 C’est quoi **GitHub Pages** ?

GitHub Pages est un service gratuit de GitHub qui permet d’héberger des sites web **statiques** directement depuis un dépôt GitHub.

En clair : tu pushes du code (HTML/CSS/JS) dans un repo → GitHub le transforme en site web accessible via une URL.

---

## 🚀 Ce que tu peux déployer sur GitHub Pages

GitHub Pages sert uniquement pour du **statique**, donc :

### ✅ OK

* Sites HTML/CSS/JS simples
* Portfolio
* Blog statique (ex: Jekyll, Astro en build statique)
* Landing pages
* Docs de projet

### ❌ Pas OK

* Backend (Node server, Python Flask, PHP…)
* Bases de données
* APIs côté serveur

👉 Par contre tu peux connecter des APIs externes (ex: OpenWeather, Firebase, etc.)

---

# 🧪 On va créer un mini projet complet + déploiement

## 🎯 Objectif

Une page simple :

* titre
* bouton
* compteur de clics (JavaScript)

---

# 📁 1. Structure du projet

Crée un dossier :

```
my-github-page/
│── index.html
│── style.css
│── script.js
```

---

# 🧾 2. Code du projet

## `index.html`

```html
<!DOCTYPE html>
<html lang="fr">
<head>
  <meta charset="UTF-8" />
  <title>Ma première page GitHub Pages</title>
  <link rel="stylesheet" href="style.css" />
</head>
<body>
  <div class="container">
    <h1>🚀 Mon site GitHub Pages</h1>
    <p>Bienvenue sur mon premier site déployé !</p>

    <button id="btn">Clique moi</button>
    <p id="counter">0 clics</p>
  </div>

  <script src="script.js"></script>
</body>
</html>
```

---

## `style.css`

```css
body {
  font-family: Arial, sans-serif;
  background: #0f172a;
  color: white;
  display: flex;
  justify-content: center;
  align-items: center;
  height: 100vh;
}

.container {
  text-align: center;
}

button {
  padding: 10px 20px;
  font-size: 16px;
  cursor: pointer;
  margin-top: 10px;
}
```

---

## `script.js`

```javascript
let count = 0;

const btn = document.getElementById("btn");
const counter = document.getElementById("counter");

btn.addEventListener("click", () => {
  count++;
  counter.textContent = `${count} clics`;
});
```

---

# 🌐 3. Mise sur GitHub

## Étape 1 : créer un repo

Va sur GitHub :

* Clique **New repository**
* Nom : `my-github-page`
* Public ✔️
* Create repository

---

## Étape 2 : push ton code

Dans ton dossier :

```bash
git init
git add .
git commit -m "first site"
git branch -M main
git remote add origin https://github.com/TON_USERNAME/my-github-page.git
git push -u origin main
```

---

# ⚙️ 4. Activer GitHub Pages

Dans ton repo GitHub :

1. Va dans **Settings**
2. Clique **Pages**
3. Section “Build and deployment”
4. Source :

   * Branch : `main`
   * Folder : `/ (root)`
5. Clique **Save**

---

# 🌍 5. Ton site est en ligne !

Après ~30 secondes tu auras une URL du type :

```
https://ton-username.github.io/my-github-page/
```

---

# 🔥 Bonus : comment ça fonctionne vraiment

GitHub Pages :

* prend ton repo
* détecte `index.html`
* sert les fichiers comme un serveur web
* ne compile rien (sauf frameworks configurés type React/Vite)

---

# 🧠 Aller plus loin

Tu peux upgrader vers :

* React + Vite (build statique)
* Astro (ultra rapide)
* Jekyll (blog)
* Portfolio pro

---

# Si tu veux continuer

Je peux te montrer :

* comment déployer un **site React sur GitHub Pages**
* comment faire un **portfolio pro complet**
* comment ajouter un **nom de domaine personnalisé**
* ou comment créer un **blog automatique**

Dis-moi 👍
