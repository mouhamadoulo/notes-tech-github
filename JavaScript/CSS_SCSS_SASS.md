# CSS (Cascading Style Sheets) 
CSS est le langage standard pour styliser les pages web.

✔️Caractéristiques : 
- Syntaxe simple, sans variables ni fonctionnalités avancées (en version classique).
- Lu directement par les navigateurs (contrairement à SASS/SCSS qui doivent être compilés en CSS).
- Pas de logique (conditions, boucles…).

✔️Exemple CSS : 
```css
button {
  background-color: #3498db;
  color: white;
  padding: 10px 20px;
}

button:hover {
  background-color: #2980b9;
}
```


# SCSS (Sassy CSS) 
Version moderne de SASS, compatible avec la syntaxe CSS.

⚠️ Doit être compilé en CSS avant d’être utilisé.

✔️Caractéristiques :

=>**Ajoute des fonctionnalités avancées :**

- Variables
- Imbrication (nesting)
- Mixins
- Fonctions
- Partials & imports
- Inheritance (@extend)
- etc

=>**Syntaxe très proche de CSS → apprentissage facile.**

✔️ Exemple SCSS : 
```scss
// Variable
$primary-color: #3498db;

// Mixin
@mixin rounded($radius) {
  border-radius: $radius;
}

button {
  background-color: $primary-color;
  color: white;
  padding: 10px 20px;

  @include rounded(5px);

  &:hover {
    background-color: darken($primary-color, 10%);
  }
}
```

✔️ Ce que le compilateur génère en CSS : 
```css
button {
  background-color: #3498db;
  color: white;
  padding: 10px 20px;
  border-radius: 5px;
}
button:hover {
  background-color: #217dbb;
}
```


# SASS (Syntactically Awesome Style Sheets)
Ancienne syntaxe de SASS, basée sur une indentation stricte.

⚠️ Pas d’accolades {} ni de points-virgules ;

⚠️ Doit aussi être compilé en CSS.

✔️Caractéristiques : 
- Identique à SCSS en fonctionnalités
- Syntaxe plus concise
- Se rapproche du style Python (indentation importante)

✔️Exemple SASS : 
```sass
$primary-color: #3498db

@mixin rounded($radius)
  border-radius: $radius

button
  background-color: $primary-color
  color: white
  padding: 10px 20px
  @include rounded(5px)

  &:hover
    background-color: darken($primary-color, 10%)
```



# Différences principales (Résumé) 

| Caractéristique          | CSS      | SCSS          | SASS                     |
| ------------------------ |--------- | ------------  | -------------------------|
| Lu par le navigateur     | Oui      | Non           | Non                      |
| Compilation requise      | Non      | Oui           | Oui                      |
| Syntaxe                  | Standard | CSS amélioré  | Indenté, sans `{}` ni `;`|
| Variables                | Non      | Oui           | Oui                      |
| Nesting (imbrication)    | Non      | Oui           | Oui                      |
| Mixins                   | Non      | Oui           | Oui                      |
| Facilités de maintenance | Moyen    | Excellent     | Excellent                |
| Courbe d’apprentissage   | Faible   | Faible        | Moyenne                  |




# ✨ Exemple comparatif côte à côte 

👍 **CSS**
```css
.nav ul {
  list-style: none;
}
.nav ul li a {
  color: black;
}
.nav ul li a:hover {
  color: blue;
}
```

👍 **SCSS**
```scss
.nav {
  ul {
    list-style: none;

    li {
      a {
        color: black;

        &:hover {
          color: blue;
        }
      }
    }
  }
}
```

👍 **SASS**
```sass
.nav
  ul
    list-style: none
    li
      a
        color: black
        &:hover
          color: blue

```