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
