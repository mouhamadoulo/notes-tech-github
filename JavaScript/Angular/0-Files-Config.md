Dans un projet Angular, ces fichiers sont principalement des **fichiers de configuration du projet**, de l'éditeur, du compilateur TypeScript, du gestionnaire de dépendances et des outils de qualité de code. Voici leur rôle et comment les configurer.

---

## `.editorconfig`

### Rôle

Définit des règles communes de formatage pour les éditeurs de code (VS Code, IntelliJ, WebStorm, etc.).

Il évite qu'un développeur utilise des tabulations alors qu'un autre utilise des espaces, ou que les fins de ligne diffèrent.

### Exemple de configuration Angular classique

```ini
# EditorConfig
root = true

[*]
charset = utf-8
indent_style = space
indent_size = 2
insert_final_newline = true
trim_trailing_whitespace = true

[*.ts]
quote_type = single

[*.json]
indent_size = 2

[*.md]
trim_trailing_whitespace = false
```

### À retenir

* Angular utilise généralement **2 espaces**
* UTF-8
* Une nouvelle ligne à la fin des fichiers

---

# `.gitignore`

### Rôle

Indique à Git quels fichiers ne doivent pas être versionnés.

Par exemple :

* dépendances npm (`node_modules`)
* fichiers générés
* fichiers temporaires
* fichiers d'environnement contenant des secrets

### Exemple Angular

```gitignore
# Dependencies
node_modules/

# Build
dist/
out-tsc/

# Angular cache
.angular/

# Environment files
src/environments/*.local.ts

# IDE
.vscode/
.idea/

# Logs
npm-debug.log
yarn-debug.log

# OS
.DS_Store
Thumbs.db
```

### Vérification

Pour voir ce que Git ignore :

```bash
git status --ignored
```

---

# `.prettierrc`

### Rôle

Configuration de Prettier, un outil qui reformate automatiquement le code.

Il garantit que toute l'équipe écrit du code avec le même style.

### Exemple Angular

```json
{
  "singleQuote": true,
  "semi": true,
  "printWidth": 100,
  "tabWidth": 2,
  "trailingComma": "all"
}
```

Explication :

| Option          | Effet                        |
| --------------- | ---------------------------- |
| `singleQuote`   | utilise `'` au lieu de `"`   |
| `semi`          | ajoute les `;`               |
| `printWidth`    | longueur maximale des lignes |
| `tabWidth`      | indentation                  |
| `trailingComma` | virgules finales             |

Installation :

```bash
npm install --save-dev prettier
```

Puis :

```bash
npx prettier --write .
```

---

# `README.md`

### Rôle

Documentation du projet.

Il contient généralement :

* description du projet
* installation
* commandes disponibles
* architecture
* règles de contribution

Exemple :

````md
# Mon application Angular

## Installation

```bash
npm install
````

## Lancement

```bash
ng serve
```

## Build production

```bash
ng build --configuration production
```

````

---

# `angular.json`

### Rôle
C'est **le fichier central de configuration Angular CLI**.

Il configure :

- nom du projet
- build
- assets
- styles globaux
- scripts externes
- environnements
- tests

Exemple simplifié :

```json
{
  "projects": {
    "mon-app": {
      "architect": {
        "build": {
          "options": {
            "outputPath": "dist/mon-app",
            "index": "src/index.html",
            "main": "src/main.ts",
            "styles": [
              "src/styles.css"
            ],
            "assets": [
              "src/assets"
            ]
          }
        }
      }
    }
  }
}
````

---

## Ajouter une librairie CSS

Exemple avec Bootstrap :

Installation :

```bash
npm install bootstrap
```

Puis dans `angular.json` :

```json
"styles": [
  "node_modules/bootstrap/dist/css/bootstrap.min.css",
  "src/styles.css"
]
```

---

## Ajouter un script externe

Exemple :

```json
"scripts": [
  "node_modules/jquery/dist/jquery.min.js"
]
```

---

# `package.json`

### Rôle

Décrit le projet npm.

Il contient :

* dépendances
* scripts
* version du projet
* configuration npm

Exemple :

```json
{
  "name": "mon-app",
  "version": "1.0.0",

  "scripts": {
    "start": "ng serve",
    "build": "ng build",
    "test": "ng test"
  },

  "dependencies": {
    "@angular/core": "^20.0.0"
  },

  "devDependencies": {
    "typescript": "^5.8.0"
  }
}
```

---

## Les commandes importantes

Démarrer :

```bash
npm start
```

Installer une dépendance :

```bash
npm install nom-package
```

Ajouter une dépendance de développement :

```bash
npm install nom-package --save-dev
```

---

# `package-lock.json`

### Rôle

Généré automatiquement par npm.

Il verrouille les versions exactes des dépendances.

Exemple :

`package.json`

```json
"rxjs": "^7.8.0"
```

Le `^` autorise des mises à jour.

Le `package-lock.json` garde par exemple :

```
rxjs 7.8.1
```

Ainsi toute l'équipe installe exactement la même version.

### Ne pas modifier manuellement.

À versionner dans Git :

```
git add package-lock.json
```

---

# `tsconfig.json`

### Rôle

Configuration globale de TypeScript.

Angular compile les fichiers `.ts` grâce à lui.

Exemple :

```json
{
  "compilerOptions": {
    "strict": true,
    "target": "ES2022",
    "module": "ES2022",
    "moduleResolution": "bundler"
  }
}
```

---

Options importantes :

| Option   | Rôle                                         |
| -------- | -------------------------------------------- |
| `strict` | active les vérifications TypeScript avancées |
| `target` | version JavaScript générée                   |
| `module` | système de modules                           |
| `paths`  | alias d'import                               |

Exemple d'alias :

```json
{
 "compilerOptions": {
   "paths": {
     "@app/*": [
       "src/app/*"
     ]
   }
 }
}
```

Après :

```ts
import { User } from '@app/models/user';
```

---

# `tsconfig.app.json`

### Rôle

Configuration TypeScript spécifique à l'application Angular.

Il hérite généralement de `tsconfig.json`.

Exemple :

```json
{
  "extends": "./tsconfig.json",

  "compilerOptions": {
    "outDir": "./out-tsc/app"
  },

  "files": [
    "src/main.ts"
  ],

  "include": [
    "src/**/*.d.ts"
  ]
}
```

Il concerne :

```
src/
 ├── app/
 ├── main.ts
 └── components
```

---

# `tsconfig.spec.json`

### Rôle

Configuration TypeScript pour les tests.

Les fichiers concernés :

```
*.spec.ts
```

Exemple :

```json
{
  "extends": "./tsconfig.json",

  "compilerOptions": {
    "outDir": "./out-tsc/spec",
    "types": [
      "jasmine"
    ]
  },

  "include": [
    "src/**/*.spec.ts"
  ]
}
```

Utilisé quand tu fais :

```bash
ng test
```

---

# Organisation typique d'un projet Angular

```
mon-projet/
│
├── src/
│   ├── app/
│   ├── assets/
│   ├── environments/
│   ├── main.ts
│   └── styles.css
│
├── angular.json        → configuration Angular CLI
├── package.json        → dépendances + scripts
├── package-lock.json   → versions exactes npm
├── tsconfig.json       → TypeScript global
├── tsconfig.app.json   → TypeScript application
├── tsconfig.spec.json  → TypeScript tests
├── .editorconfig       → règles éditeur
├── .prettierrc         → formatage automatique
├── .gitignore          → fichiers ignorés Git
└── README.md           → documentation
```

---

## Configuration recommandée pour un projet Angular professionnel

Minimum conseillé :

✅ `.editorconfig` → règles communes
✅ `.prettierrc` → formatage automatique
✅ `strict: true` dans TypeScript
✅ `package-lock.json` versionné
✅ `node_modules` ignoré
✅ `README.md` maintenu
✅ scripts npm propres dans `package.json`

Ces fichiers ne contiennent généralement pas de logique métier : ils définissent surtout **comment le projet est construit, testé et maintenu par l'équipe**.
