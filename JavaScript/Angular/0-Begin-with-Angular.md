# 🔷 <ins>Qu’est-ce qu’Angular ?</ins>
Angular est un framework front-end open source développé par Google, basé sur TypeScript.
Il permet de créer des applications web dynamiques, robustes et maintenables, notamment des Single Page Applications (SPA).

## Installation
Pour installer Angular sur sa machine de manière globale , on aura besoin : 
- d'installer Node (version LTS)
- Node inclut NPM le gestionnaire de packages, et avec on tape la commande : 
```
npm install -g @angular/cli
```


# 🧱 Concepts fondamentaux

## 1. Architecture par composants
Une application Angular est composée de composants.
Chaque composant contient :
- un template HTML
- une logique TypeScript
- un style CSS/SCSS

```
@Component({
  selector: 'app-example',
  templateUrl: './example.component.html',
  styleUrls: ['./example.component.scss']
})
export class ExampleComponent {}

```

## 2. Modules (NgModules)
Les modules organisent l’application en blocs fonctionnels.
- AppModule est le module racine
- Permettent de regrouper composants, services, pipes, etc.

```
@NgModule({
  declarations: [],
  imports: [],
  providers: [],
  bootstrap: [AppComponent]
})
export class AppModule {}

```

## 3. Data Binding
Angular propose plusieurs types de liaison de données :
|Type	|Syntaxe	|Description|
|-------|-----------|-----------|
|Interpolation	|{{ value }}	|Affichage de données|
|Property Binding	|[property]	|Données vers le DOM|
|Event Binding	|(event)	|Événements utilisateur|
|Two-way Binding	|[(ngModel)]	|Liaison bidirectionnelle|

## 4. Directives
Elles modifient le comportement ou l’affichage du DOM.
- **Structurelles :** *ngIf, *ngFor, *ngSwitch
- **Attributs :** ngClass, ngStyle

```
<div *ngIf="isVisible">Visible</div>
```

## 5. Services et Injection de dépendances
Les services contiennent la logique métier et sont injectés dans les composants.
```
@Injectable({ providedIn: 'root' })
export class ExampleService {}
```

Angular utilise un système d’injection de dépendances puissant et centralisé.

## 6. Routing
Angular permet de créer des applications multi-pages sans rechargement.
```
const routes: Routes = [
  { path: 'home', component: HomeComponent }
];
```

- Navigation dynamique
- Lazy loading possible

## 7. Formulaires
Deux approches principales :
- Template-driven forms
- Reactive forms (recommandées)
```
this.form = new FormGroup({
  name: new FormControl('')
});
```

## 8. Observables & RxJS
Angular repose fortement sur RxJS pour la gestion asynchrone.
- Requêtes HTTP
- Événements
- Streams de données
```
this.http.get(url).subscribe(data => {});
```

# 🛠️ Angular CLI
Outil en ligne de commande pour :
- Créer des projets
- Générer des composants/services
- Lancer un serveur local
- Builder l’application
```
ng new my-app
ng serve
ng generate component my-component
```
## 🔧 Installation & configuration
```
npm install -g @angular/cli
```
Installe Angular CLI globalement.
```
ng version
```
Affiche la version d’Angular et des dépendances.

## 🆕 Création de projet
```
ng new nom-du-projet
```
Crée un nouveau projet Angular (avec routing et styles au choix).

```
npx @angular/cli@12 new mon-projet-angular12
```
Créer un projet Angular dans une version spécifique (ici Angular 12).
A noter que il faudra aussi installer une version de node compatible à la version Angular utilisée

```
cd nom-du-projet
ng serve
```
Lance le serveur de développement
➡️ qui est accessible sur http://localhost:4200

## Options commandes ng

### 🔹 ng new — créer un projet Angular
```
ng new mon-projet [options]
```
**Options courantes**
|Option	|Utilité|
|-------|-------|
|--routing	|Ajoute automatiquement le module de routing|
|--style=scss	|Définit le préprocesseur CSS (css, scss, sass, less)|
|--standalone	|Crée un projet basé sur les composants standalone|
|--skip-tests	|Ne génère pas les fichiers .spec.ts|
|--strict	|Active le mode strict TypeScript (recommandé)|
|--prefix=app	|Préfixe des sélecteurs de composants|
|--skip-install	|N’installe pas les dépendances npm|

📌Exemple :
```
ng new blog --routing --style=scss --strict
```

### 🔹 ng generate (ng g) — générer du code
```
ng g component header [options]
```
**Options générales**
|Option	|Utilité|
|-------|-------|
|--skip-tests	|Ne crée pas de fichiers de test|
|--inline-style	|Met le CSS dans le .ts|
|--inline-template	|Met le HTML dans le .ts|
|--standalone	|Génère un composant standalone|
|--flat	|Ne crée pas de dossier|

📌Exemple :
```
ng g component navbar --standalone
ng g service post --skip-tests
ng g module admin --routing
```

### 🔹 ng serve — lancer le serveur de dev
```
ng serve [options]
```
|Option	|Utilité|
|-------|-------|
|--open	|Ouvre le navigateur automatiquement|
|--port=4300	|Change le port|
|--host=0.0.0.0	|Accessible sur le réseau|
|--ssl	|Active HTTPS|
|--configuration=production	|Sert en mode production|

📌Exemple :
```
ng serve --open --port=4300
```

### 🔹 ng build — compiler l’application
```
ng build [options]
```
|Option	|Utilité|
|-------|-------|
|--configuration=production	|Build optimisé|
|--watch	|Rebuild automatique|
|--source-map	|Génère les source maps|
|--output-path=dist/app	|Dossier de sortie personnalisé|

📌Exemple :
```
ng build --configuration=production
```

### 🔹 ng test — lancer les tests
```
ng test [options]
```
|Option	|Utilité|
|-------|-------|
|--watch=false	|Lance une fois|
|--code-coverage	|Génère un rapport de couverture|

### 🔹 ng lint — analyser le code
```
ng lint
```

- Vérifie les règles ESLint
- Détecte les erreurs de style ou de bonnes pratiques

🔹 Options globales (ng)
|Option	|Utilité|
|-------|-------|
|--help	|Liste les options disponibles|
|--version	|Version Angular CLI|
|--verbose	|Affiche plus de détails|

```
ng serve --help
```

## ⚙️ Génération de fichiers (très utilisé)
```
ng generate component nom
```
ou raccourci :
```
ng g c nom
```
Commandes courantes :
```
ng g module nom
ng g service nom
ng g directive nom
ng g pipe nom
ng g guard nom
ng g interface nom
```
Exemple :
```
ng g c users/user-list
```

## 🧱 Build & déploiement
```
ng build
````
Compile le projet (dossier dist/).
```
ng build --configuration production
```
Build optimisé pour la production.

## 🧪 Tests
```
ng test
```
Lance les tests unitaires (Karma/Jasmine).
```
ng e2e
```
Lance les tests end-to-end.

## 🔍 Analyse & aide
```
ng help
```
Affiche l’aide générale.
```
ng generate --help
```
Liste toutes les options de génération.

## 🧼 Autres commandes utiles
```
ng lint
```
Analyse le code (si ESLint configuré).
```
ng update
```
Met à jour Angular et ses dépendances.

## ✅ Raccourcis à retenir absolument

|Action	|Commande|
|-------|--------|
|Créer un projet|	ng new|
|Lancer le serveur|	ng serve|
|Générer un composant|	ng g c|
|Générer un service|	ng g s|
|Build production|	ng build --configuration production|

# ✅ Avantages

- Structure claire et scalable
- TypeScript (typage fort)
- Framework complet (tout intégré)
- Maintenu par Google
- Excellent pour les projets complexes

# ⚠️ Inconvénients

- Courbe d’apprentissage élevée
- Verbosité importante
- Moins flexible que certains frameworks légers

# 📌 Cas d’usage

- Applications d’entreprise
- Dashboards complexes
- Applications à long terme
- Projets nécessitant une architecture solide

# 🔍 <ins>Top Repos Github</ins>

### 📦 Expense Tracker
Link : https://github.com/erickboyzo/expense-tracker
```
This project is great for beginners to start practicing application development. 
It allows users to keep track of their daily expenses and manage their budgets. 
Additionally, users can analyze their spending patterns with this application. 
```

### 📦 Soundnode
Link : https://github.com/Soundnode/soundnode-app
```
This is another Angular project that budding developers can build. 
Soundnode is a desktop music player for streaming music from SoundCloud without a web browser. 
It uses SoundCloud API to stream music, where you can easily create a playlist.
```

### 📦 Weather Forecast Application
Link : https://github.com/maksimgru/weather-app-angular
```
In this project, you will create a weather forecast application capable of displaying weather forecasts by city name. 
It will offer different types of weather updates based on the location entered. 
For better functionality, you can train your application with mock data available during the development process. 
```

### 📦 Notepad Application
Link : https://github.com/Devstackr/basic-notes-app-mean-stack
```
An excellent example of an Angular project suitable for beginners is a Notepad application. 
This app acts as a digital notebook that allows users to write and save notes conveniently. 
Once users enter text in the app, they can later view, edit, or delete these notes. 
```

### 📦 Fitness Tracker
Link : https://github.com/Gagandeep39/angular-fitness-tracker
```
A fitness tracker application collects data on users’ fitness-related activities such as cycling, swimming, running, etc. 
This application enables users to monitor their exercise routines and daily progress. 
The aim is to provide a user-friendly and interactive application that motivates users to engage in healthier lifestyles 
through regular fitness tracking. 
```

### 📦 URL Shortener
Link : https://github.com/thiaguimcavalcanti/url-shortener-angular-client
```
This is an engaging Angular project for mid-level developers. 
You can start it by leveraging Polr, an open-source application that uses MySQL as its primary database. 
This project aims to develop a URL shortener application that enhances user experience 
through a quick and responsive interface. 
```

### 📦 Currency Converter Application
Link : https://github.com/AndreaAnge/angular-currency-converter
```
This application, created with Angular, enables users to explore various currency rates, including pounds, dollars, 
rupees, and more. With this currency converter application, users will easily be able to estimate foreign travel 
expenses and education costs at an overseas university and calculate financial market values. 
```

### 📦 Blogging Application
Link : https://github.com/mehulk05/Angular-Blogging-App
```
With the help of Angular, you can develop a project for a blogging application. 
This application will enable users to create, edit, delete, and view blog posts. 
It can act as a content management system specialized for blogs. 
```

### 📦 Real-Time Chat Application
Link : https://github.com/scalablescripts/angular-chat
```
This is an interesting advanced-level Angular project based on the real-time chat feature widely used across businesses 
like e-commerce and service providers. In this real-time chat application project, you will develop a user interface 
through which the users can create accounts, interact with other users, and exchange messages. 
```

### 📦 E-Learning Platform
Link : https://github.com/cs-MohamedAyman/eLearning-Platforms
```
An e-learning platform is meant to provide access to online education and various professional courses. 
This Angular project involves creating a user-friendly interface to search for courses, check reviews and ratings, 
view the course content, and enroll in a course. 
```

### 📦 Game Analyser
Link : https://github.com/pnprog/goreviewpartner
```
Before launching a game in the public domain, it has to undergo testing to check all its functionalities, 
entertainment value, and clarity level of the user interface. 
This game analyzer project built on Angular has the potential to revolutionize modern-day gaming approaches. 
Designers and programmers will find it remarkably effortless to build games, collaborate with potential users for feedback, 
and implement necessary modifications.
```

### 📦 Social Media Platform
Link : https://github.com/maurobonfietti/social
```
Social media is everywhere. Constructing a social media platform can enhance your Angular project portfolio. 
A social media platform must have a smooth user interface that lets users produce, modify, upgrade, 
and delete posts and include features that enable users to like and comment on others’ posts. 
```

# <ins>Exemple d'applications à créer avec Angular (pour monter en compétences)</ins> 

## <1> Application To-Do avancée
**Objectif :** Découvrir les bases solides d’Angular <br>
**Fonctionnalités :**
- Ajouter / modifier / supprimer des tâches
- Marquer une tâche comme terminée
- Filtrage (toutes / en cours / terminées)
- Sauvegarde locale

**Concepts Angular maîtrisés :**
- Architecture Angular (modules, composants)
- Data binding (interpolation, property, event, two-way)
- Directives (*ngIf, *ngFor, ngClass)
- Services & injection de dépendances
- Gestion de l’état simple
- LocalStorage
- Bonnes pratiques de structure de projet

## <2> Application de gestion de contacts
**Objectif :** Approfondir la communication entre composants <br>
**Fonctionnalités :**
- Liste de contacts
- Fiche détaillée
- Ajout / édition / suppression
- Recherche en temps réel

**Concepts Angular maîtrisés :**
- @Input() / @Output()
- Composants parents/enfants
- Pipes personnalisés
- Forms template-driven
- Cycle de vie des composants
- Services partagés

## <3> Application de gestion de formulaires (Form Builder)
**Objectif :** Maîtriser les formulaires avancés <br>
**Fonctionnalités :**
- Création dynamique de formulaires
- Validation avancée
- Messages d’erreurs personnalisés
- Prévisualisation en temps réel

**Concepts Angular maîtrisés :**
- Reactive Forms
- FormGroup, FormControl, FormArray
- Validators personnalisés
- Formulaires dynamiques
- Détection de changements
- RxJS de base

## <4> Application de blog avec authentification
**Objectif :** Apprendre à connecter Angular à une API <br>
**Fonctionnalités :**
- Authentification (login / logout)
- CRUD articles
- Commentaires
- Permissions utilisateur

**Concepts Angular maîtrisés :**
- HttpClient
- Intercepteurs HTTP
- Guards (CanActivate)
- Routing avancé
- Lazy loading
- Gestion des tokens (JWT)
- Environnement (environment.ts)

## <5> Dashboard d’administration
**Objectif :** Construire une application professionnelle <br>
**Fonctionnalités :**
- Statistiques dynamiques
- Graphiques (charts)
- Tableau avec pagination, tri et filtres
- Gestion des rôles

**Concepts Angular maîtrisés :**
- Angular Material
- Data tables
- Composants réutilisables
- Layout responsive
- RxJS intermédiaire
- Performance & optimisation

## <6> Application de réservation (type Airbnb)
**Objectif :** Gérer des flux complexes <br>
**Fonctionnalités :**
- Recherche multi-critères
- Calendrier de réservation
- Paiement simulé
- Notifications

**Concepts Angular maîtrisés :**
- State management avancé
- Observables & Subjects
- Communication inter-modules
- Guards complexes
- UX avancée
- Gestion des erreurs globales

## <7> Application temps réel (Chat ou Notifications)
**Objectif :** Travailler le temps réel <br>
**Fonctionnalités :**
- Messages instantanés
- Indicateur de présence
- Notifications en direct

**Concepts Angular maîtrisés :**
- WebSockets
- RxJS avancé
- Async Pipe
- Gestion mémoire (unsubscribe)
- Performance en temps réel

## <8> Application entreprise avec NgRx
**Objectif :** Niveau expert Angular <br>
**Fonctionnalités :**
- Gestion d’état globale
- Synchronisation serveur
- Cache intelligent
- Offline mode

**Concepts Angular maîtrisés :**
- NgRx (Store, Actions, Reducers, Effects)
- Architecture scalable
- Immutabilité
- Tests unitaires NgRx
- Debug avancé

## <9> Micro-frontend Angular
**Objectif :** Architecture moderne <br>
**Fonctionnalités :**
- Applications indépendantes
- Partage de composants
- Déploiement séparé

**Concepts Angular maîtrisés :**
- Module Federation
- Lazy loading avancé
- Communication inter-apps
- CI/CD frontend
- Architecture micro-frontend

## <10> Projet full test & qualité
**Objectif :** Qualité professionnelle <br>
**Fonctionnalités :**
- Tests unitaires
- Tests e2e
- Analyse de performance

**Concepts Angular maîtrisés :**
- Jasmine / Karma / Jest
- Cypress / Playwright
- Tests de composants
- Tests de services
- Code coverage

## 🔥 Bonus – Projet “réel”
**👉 Clone une application existante :**
- Trello
- Spotify (front uniquement)
- Google Calendar
- Notion

# <ins>Exemples de projets d’entreprise où Angular et Java Spring Boot cohabitent</ins>

## 1️.Système d’information bancaire (Banque / Assurance)

### 🔷 Contexte
Portail bancaire interne + applications clients (gestion de comptes, crédits, KYC, sinistres, etc.)

### 🔷 Rôle d’Angular
**Frontend SPA pour :**
- conseillers bancaires
- back-office (gestion clients, contrats)
- parfois clients finaux

**Gestion :**
- formulaires complexes
- tableaux, filtres, dashboards
- règles de validation UI

**Sécurité côté front (guards, rôles)**

### 🔷 Rôle de Spring Boot
**Backend métier :**
- gestion des comptes
- calculs financiers
- règles de conformité
- orchestration des processus

**Exposition d’API REST**

**Connexion :**
- bases de données (Oracle, PostgreSQL)
- systèmes legacy (SOAP, mainframe)

**Sécurité (OAuth2, JWT)**

### 🔷 Communication
```
Angular (HTTP/JSON)
        ↓
API REST Spring Boot
        ↓
Base de données / SI legacy

```
- REST + JSON
- Authentification via JWT (souvent Keycloak)
- Appels asynchrones (RxJS côté Angular)

## 2.Plateforme e-commerce B2B / B2C
### 🔷 Contexte
Gestion de catalogues, commandes, stocks, paiements, clients professionnels.

### 🔷 Rôle d’Angular
**Application web riche :**
- navigation rapide (SPA)
- gestion du panier
- tunnel de commande
- dashboards admin

**Internationalisation (i18n)**

**SEO parfois via Angular Universal**

### 🔷 Rôle de Spring Boot
**Microservices :**
- commandes
- paiements
- produits
- facturation

**Logique métier critique**

**Gestion des transactions**

**Intégration paiement (PSP)**

### 🔷 Communication
**REST pour la majorité des échanges**

**Parfois :**
- GraphQL (catalogue)
- WebSockets (suivi commande temps réel)

**Événements :**
- Kafka / RabbitMQ entre services Spring Boot

## 3.Application RH / SIRH (Grand groupe)
### 🔷 Contexte
Gestion des employés, congés, notes de frais, évaluations.

### 🔷 Rôle d’Angular
**Interface unique pour :**
- employés
- managers
- RH

**Composants très modulaires**

**Formulaires longs et complexes**

**UX riche (drag & drop, modales)**

### 🔷 Rôle de Spring Boot
**Gestion :**
- workflow métier (validation congés)
- règles RH
- reporting

**Exposition d’API sécurisées**

**Batchs (exports, imports)**

### 🔷 Communication
- REST + JSON
- Pagination / tri / filtres côté backend
- Gestion des rôles (ADMIN, MANAGER, USER)

## 4.Plateforme de supervision / monitoring industriel
### 🔷 Contexte
Suivi d’équipements, capteurs IoT, alertes temps réel.

### 🔷 Rôle d’Angular
- Dashboards temps réel
- Graphiques dynamiques
- Cartographie
- Rafraîchissement sans rechargement

### 🔷 Rôle de Spring Boot
- Agrégation de données
- Calculs en temps réel
- Détection d’anomalies
- Exposition des données métier

### 🔷 Communication
- REST (données historiques)
- WebSocket / SSE (temps réel)
- Authentification forte

## 5.ERP / Outil interne de gestion
### 🔷 Contexte
Gestion finance, achats, stocks, logistique.

### 🔷 Rôle d’Angular
- UI modulaire par domaine
- Navigation fluide
- Réutilisation de composants
- Accès multi-rôles

### 🔷 Rôle de Spring Boot
- Couche métier centrale
- Transactions complexes
- Accès base de données
- Intégration avec d’autres ERP

### 🔷 Communication
- API REST versionnées (/api/v1)
- DTO pour échanges
- Validation côté backend

## 🔧 Architecture type Angular + Spring Boot
```
[ Navigateur ]
      |
      |  HTTP / HTTPS
      v
[ Angular SPA ]
      |
      | REST / JSON / JWT
      v
[ Spring Boot API ]
      |
      | JPA / JDBC
      v
[ Base de données ]
```

## 🔐 Sécurité typique
**Angular :**
- stockage token (memory / session)
- guards de routes

**Spring Boot :**
- OAuth2 / JWT
- rôles & permissions
- filtrage backend

## 🧠 Pourquoi ce duo est si utilisé en entreprise
✅ Angular : structuré, robuste, idéal gros projets<br>
✅ Spring Boot : performant, mature, standard entreprise<br>
✅ Séparation claire frontend / backend<br>
✅ Scalabilité (microservices)<br>
✅ Écosystème énorme<br>

# ✅ <ins>Best Practices Angular (2026)</ins>

## 🧱 Architecture
- Architecture **feature-based** (par domaine métier, pas technique)
- `core/` → services globaux, interceptors, guards
- `shared/` → composants/pipes/directives réutilisables
- `features/` → modules métier
- Respecter le **Single Responsibility Principle**
- Éviter les dépendances circulaires

## 📦 Organisation du code
- 1 composant = 1 dossier
- Nommage clair :
  - `*.component.ts`
  - `*.service.ts`
  - `*.model.ts`
  - `*.enum.ts`

## ⚙️ Composants
- Utiliser `ChangeDetectionStrategy.OnPush`
- Favoriser les composants **présentationnels (dumb)**
- Passer les données via `@Input()` / `@Output()`
- Éviter la logique métier dans les composants
- Limiter la taille (<300 lignes)

## 🔁 RxJS & gestion des flux
- Utiliser les **Observables partout**
- Préférer `async pipe` au `subscribe()`
- Gérer les unsubscribe (`takeUntil`, `destroyRef`)
- Éviter les nested subscriptions

## 🧠 State Management
- Simple → services + RxJS
- Complexe → NgRx / Signals
- Ne jamais stocker l’état dans les composants

## 🌐 HTTP & API
- Centraliser les appels API dans des services
- Utiliser des **HttpInterceptors** (auth, erreurs)
- Typage strict des réponses (interfaces)

## 🛡️ Sécurité
- Utiliser des **Guards** (`CanActivate`)
- Gérer les tokens via interceptor
- Éviter `localStorage` pour données sensibles

## 🚀 Performance
- Lazy loading des modules
- `trackBy` dans `*ngFor`
- Éviter les calculs lourds dans templates
- Utiliser `OnPush`

## 🎯 Forms
- Préférer **Reactive Forms**
- Validation côté client + serveur
- Validators custom centralisés

## 🧪 Tests
- Tester services et logique métier
- Mock des dépendances
- Utiliser Jest ou Jasmine

## 🎨 UI / UX
- Pas de logique complexe dans HTML
- Utiliser un design system (Angular Material…)
- Séparer UI et logique

## 🧰 Qualité de code
- ESLint + Prettier
- Typage strict (`strict: true`)
- Alias imports (`@app`, `@shared`)

## 🔄 CI/CD
- Build prod optimisé (`ng build --prod`)
- Lint + tests dans pipeline
- Versioning propre (Git flow ou trunk-based)

## 📚 Bonnes habitudes
- Documenter le code complexe
- Refactor régulier
- Supprimer le code mort
- Faire des code reviews

## 🧠 Règle d’or
> Composants simples + logique dans services + RxJS propre = app scalable
