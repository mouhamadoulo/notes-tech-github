# Déboguer une Application Angular

## Introduction

Le débogage (debugging) est une compétence essentielle pour tout développeur Angular. Une application Angular moderne combine :

* TypeScript
* Templates HTML
* RxJS
* Services HTTP
* Injection de dépendances
* Routing
* Gestion d'état

Les erreurs peuvent apparaître à plusieurs niveaux. Ce guide présente les techniques professionnelles pour identifier et corriger efficacement les problèmes.

---

# 1. Préparer son environnement de débogage

## Lancer Angular en mode développement

```bash
ng serve
```

Ou :

```bash
ng serve --open
```

Le mode développement fournit :

* Source maps
* Messages d'erreur détaillés
* Stack traces complètes
* Angular DevTools

---

## Installer Angular DevTools

Extension officielle :

* Chrome
* Edge

Fonctionnalités :

* Inspection des composants
* Analyse des performances
* Visualisation de l'arbre Angular
* Détection des cycles de changement

---

# 2. Utiliser les outils de développement du navigateur

## Ouvrir les DevTools

Chrome :

```text
F12
```

ou

```text
Ctrl + Shift + I
```

---

## Onglet Console

La console affiche :

```text
ERROR TypeError: Cannot read properties of undefined
```

Exemple :

```typescript
user.name
```

Si :

```typescript
user = undefined;
```

Erreur :

```text
Cannot read properties of undefined
```

Correction :

```typescript
user?.name
```

ou

```typescript
@if (user) {
  {{ user.name }}
}
```

---

# 3. Utiliser console.log intelligemment

## Mauvaise pratique

```typescript
console.log(data);
console.log(data);
console.log(data);
console.log(data);
```

Impossible de savoir d'où vient le log.

---

## Bonne pratique

```typescript
console.log('[UserService]', data);
```

Ou :

```typescript
console.log('[DashboardComponent] user loaded', user);
```

---

## Exemple complet

```typescript
loadUser() {
  console.log('Début chargement utilisateur');

  this.userService.getUser().subscribe(user => {
    console.log('Utilisateur reçu', user);
  });

  console.log('Fin méthode');
}
```

Sortie :

```text
Début chargement utilisateur
Fin méthode
Utilisateur reçu
```

Cela permet de comprendre l'asynchronisme.

---

# 4. Utiliser les Breakpoints

## Ajouter un breakpoint

Dans Chrome :

1. Sources
2. Fichier TypeScript
3. Cliquer sur le numéro de ligne

Exemple :

```typescript
saveUser() {
  const payload = this.form.value;
  this.http.post('/api/users', payload);
}
```

Breakpoint :

```typescript
const payload = this.form.value;
```

L'exécution s'arrête avant la ligne.

---

## Inspection des variables

Une fois arrêté :

```typescript
payload
```

Affiche :

```json
{
  "name": "John",
  "email": "john@mail.com"
}
```

---

# 5. Utiliser le mot-clé debugger

## Exemple

```typescript
saveUser() {
  debugger;

  const payload = this.form.value;
}
```

Lorsque les DevTools sont ouverts :

```typescript
debugger;
```

agit comme un breakpoint.

---

# 6. Déboguer les erreurs HTTP

## Service Angular

```typescript
getUsers() {
  return this.http.get<User[]>('/api/users');
}
```

---

## Observer la requête

Ouvrir :

```text
Network
```

Puis :

```text
Fetch/XHR
```

Vous pouvez voir :

* URL
* Headers
* Payload
* Status code
* Réponse

---

## Exemple d'erreur 404

```text
GET /api/user
404 Not Found
```

Le backend attend :

```text
/api/users
```

Correction :

```typescript
return this.http.get('/api/users');
```

---

## Exemple d'erreur 500

```text
500 Internal Server Error
```

Vérifier :

```text
Response
```

pour lire le message du serveur.

---

# 7. Déboguer les Observables RxJS

## Problème fréquent

```typescript
this.userService.getUsers();
```

Aucun résultat.

Pourquoi ?

---

## Observable non souscrit

```typescript
getUsers() {
  return this.http.get('/api/users');
}
```

Puis :

```typescript
this.userService.getUsers();
```

Le flux n'est jamais exécuté.

---

## Correction

```typescript
this.userService.getUsers()
  .subscribe(users => {
    console.log(users);
  });
```

---

## Utiliser tap pour déboguer

```typescript
this.userService.getUsers()
  .pipe(
    tap(users => console.log(users))
  )
  .subscribe();
```

Très utile dans les pipelines RxJS.

---

## Pipeline complexe

```typescript
this.http.get('/api/users')
  .pipe(
    tap(data => console.log('1', data)),

    map(users => users.filter(u => u.active)),

    tap(data => console.log('2', data)),

    take(1)
  )
  .subscribe();
```

Chaque étape est visible.

---

# 8. Déboguer les formulaires Angular

## Formulaire réactif

```typescript
form = this.fb.group({
  email: ['', Validators.required]
});
```

---

## Vérifier l'état du formulaire

```typescript
console.log(this.form.valid);
console.log(this.form.errors);
console.log(this.form.value);
```

---

## Vérifier un champ

```typescript
console.log(
  this.form.get('email')?.errors
);
```

Résultat :

```json
{
  "required": true
}
```

---

## Observer les changements

```typescript
this.form.valueChanges.subscribe(value => {
  console.log(value);
});
```

---

# 9. Déboguer le Change Detection

## Symptôme

La variable change :

```typescript
this.counter++;
```

Mais l'écran ne se met pas à jour.

---

## Cas fréquent : code hors zone Angular

```typescript
setTimeout(() => {
  this.counter++;
}, 1000);
```

Fonctionne généralement.

Mais certaines bibliothèques externes peuvent contourner Angular.

---

## Vérification

```typescript
constructor(
  private cd: ChangeDetectorRef
) {}
```

Puis :

```typescript
this.cd.detectChanges();
```

Si l'affichage revient :

```text
problème de change detection
```

---

# 10. Déboguer le Routing

## Vérifier la route active

```typescript
constructor(private router: Router) {}
```

```typescript
console.log(this.router.url);
```

---

## Observer les changements

```typescript
this.router.events.subscribe(event => {
  console.log(event);
});
```

---

## Erreur fréquente

```typescript
this.router.navigate(['/users']);
```

Route inexistante :

```typescript
{
  path: 'user',
  component: UserComponent
}
```

Correction :

```typescript
this.router.navigate(['/user']);
```

---

# 11. Déboguer les Guards

## Exemple

```typescript
canActivate(): boolean {
  return false;
}
```

La page ne s'ouvre jamais.

---

## Ajouter des traces

```typescript
canActivate(): boolean {

  console.log('Guard exécuté');

  return false;
}
```

Puis :

```typescript
console.log('Utilisateur connecté');
console.log(isLogged);
```

---

# 12. Déboguer les Services

## Vérifier l'injection

```typescript
constructor(
  private userService: UserService
) {}
```

Erreur :

```text
NullInjectorError
```

Cause :

```typescript
providers: []
```

ou service mal déclaré.

---

## Vérifier l'instance

```typescript
console.log(this.userService);
```

---

# 13. Déboguer les erreurs de template

## Exemple

```html
{{ user.name }}
```

Erreur :

```text
Cannot read properties of undefined
```

---

## Solution

```html
{{ user?.name }}
```

---

## Avec les nouvelles directives Angular

```html
@if (user) {
  {{ user.name }}
}
```

Plus robuste.

---

# 14. Déboguer les performances

## Angular DevTools Profiler

Permet de voir :

* Temps de rendu
* Détection de changements
* Composants lents

---

## Exemple problématique

```html
<li *ngFor="let item of items">
```

Liste :

```typescript
10000 éléments
```

À chaque détection :

```text
Re-rendu complet
```

---

## Optimisation

```html
<li *ngFor="let item of items; trackBy: trackById">
```

```typescript
trackById(index: number, item: User) {
  return item.id;
}
```

---

# 15. Déboguer avec les Source Maps

## Pourquoi ?

Le navigateur exécute :

```javascript
main.js
```

Mais vous voulez voir :

```typescript
user.component.ts
```

Angular génère automatiquement :

```text
Source Maps
```

Vous pouvez donc :

* mettre des breakpoints TypeScript
* inspecter les variables réelles
* suivre la pile d'appels

---

# 16. Étude de cas complète

## Bug signalé

```text
La liste des utilisateurs ne s'affiche plus.
```

---

## Étape 1 : Vérifier la console

Erreur :

```text
Cannot read properties of undefined
```

---

## Étape 2 : Localiser

Template :

```html
{{ user.name }}
```

---

## Étape 3 : Vérifier les données

```typescript
console.log(user);
```

Résultat :

```typescript
undefined
```

---

## Étape 4 : Vérifier la requête HTTP

Network :

```text
404 Not Found
```

---

## Étape 5 : Vérifier l'URL

Code :

```typescript
/api/user
```

Backend :

```typescript
/api/users
```

---

## Étape 6 : Corriger

```typescript
return this.http.get('/api/users');
```

---

## Étape 7 : Sécuriser le template

```html
{{ user?.name }}
```

---

## Résultat

* Plus d'erreur HTTP
* Plus d'erreur template
* Données affichées correctement

---

# Checklist de Débogage Angular

Lorsqu'un bug survient :

1. Vérifier la Console.
2. Vérifier l'onglet Network.
3. Reproduire systématiquement le bug.
4. Ajouter des logs ciblés.
5. Utiliser des breakpoints.
6. Inspecter les Observables.
7. Vérifier les formulaires.
8. Vérifier le routing.
9. Vérifier les guards.
10. Vérifier le Change Detection.
11. Utiliser Angular DevTools.
12. Corriger puis retester.

---

# Conclusion

Le débogage Angular repose principalement sur quatre outils :

1. **Chrome DevTools** (Console, Sources, Network)
2. **Angular DevTools**
3. **Breakpoints et debugger**
4. **Logs ciblés dans les composants, services et flux RxJS**

La méthode la plus efficace consiste à suivre un processus systématique :

```text
Erreur → Reproduction → Inspection → Isolation → Correction → Validation
```

Avec cette approche, même les bugs complexes impliquant RxJS, les requêtes HTTP, le routing ou le Change Detection deviennent beaucoup plus faciles à diagnostiquer et à corriger.
