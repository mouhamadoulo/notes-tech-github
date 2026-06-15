# Cours Magistral RxJS & Programmation Réactive en Angular

> Niveau : Développeur Angular professionnel
>
> Objectif : Comprendre profondément RxJS, la programmation réactive et leur utilisation moderne dans Angular.

---

# Sommaire

1. Introduction à la programmation réactive
2. Pourquoi RxJS existe
3. Observable : le cœur de RxJS
4. Observer
5. Subscription
6. Flux synchrones vs asynchrones
7. Création d'Observables
8. Les opérateurs RxJS
9. Les Subjects
10. Gestion d'état avec RxJS
11. RxJS dans Angular
12. HttpClient et RxJS
13. Async Pipe
14. Gestion des formulaires réactifs
15. Combinaison de flux
16. Gestion des erreurs
17. Désabonnement propre
18. Bonnes pratiques Angular modernes
19. Cas d'usage avancés
20. Pièges fréquents
21. Architecture RxJS en entreprise

---

# 1. Introduction à la programmation réactive

La programmation réactive consiste à :

> Déclarer comment réagir lorsqu'une donnée change.

Au lieu de demander constamment :

```ts
const user = getUser();
```

On s'abonne aux changements :

```ts
user$.subscribe(user => {
  console.log(user);
});
```

L'application devient pilotée par les événements.

---

# 2. Pourquoi RxJS existe

Dans une application Angular :

- appels HTTP
- WebSocket
- clics utilisateurs
- formulaires
- routing
- timers
- signaux temps réel

sont tous des flux de données.

RxJS permet de :

- représenter ces flux
- les transformer
- les filtrer
- les combiner
- les annuler

---

# 3. Observable : le cœur de RxJS

Un Observable est une source de données dans le temps.

Exemple :

```ts
const observable$ = new Observable(observer => {
  observer.next('Angular');
  observer.next('React');
  observer.next('Vue');

  observer.complete();
});
```

Consommation :

```ts
observable$.subscribe(value => {
  console.log(value);
});
```

Affichage :

```text
Angular
React
Vue
```

---

# 4. Observer

L'observer reçoit les événements.

```ts
observable$.subscribe({
  next: value => console.log(value),
  error: err => console.error(err),
  complete: () => console.log('Terminé')
});
```

Les trois canaux :

```text
next
error
complete
```

---

# 5. Subscription

subscribe retourne un objet Subscription.

```ts
const sub = observable$.subscribe();
```

Désabonnement :

```ts
sub.unsubscribe();
```

Très important pour éviter les fuites mémoire.

---

# 6. Flux synchrones vs asynchrones

## Synchrone

```ts
of(1, 2, 3).subscribe(console.log);
```

Sortie immédiate :

```text
1
2
3
```

---

## Asynchrone

```ts
interval(1000).subscribe(console.log);
```

Sortie :

```text
0
1
2
3
...
```

---

# 7. Création d'Observables

## of

```ts
of('Angular');
```

---

## from

Array :

```ts
from([1, 2, 3]);
```

---

Promise :

```ts
from(fetch('/api/users'));
```

---

## interval

```ts
interval(1000);
```

---

## timer

```ts
timer(3000);
```

---

## Observable personnalisé

```ts
const stream$ = new Observable(observer => {

  setTimeout(() => {
    observer.next('Hello');
  }, 1000);

});
```

---

# 8. Les opérateurs RxJS

Le véritable pouvoir de RxJS.

---

# pipe()

Permet d'enchaîner les opérateurs.

```ts
observable$.pipe(
  operator1(),
  operator2(),
  operator3()
);
```

---

# map

Transformation.

```ts
of(1, 2, 3).pipe(
  map(x => x * 10)
);
```

Résultat :

```text
10
20
30
```

---

# filter

```ts
of(1, 2, 3, 4, 5).pipe(
  filter(x => x % 2 === 0)
);
```

Résultat :

```text
2
4
```

---

# tap

Effets secondaires.

```ts
users$.pipe(
  tap(users => console.log(users))
);
```

Ne modifie jamais la donnée.

---

# take

```ts
interval(1000).pipe(
  take(3)
);
```

Résultat :

```text
0
1
2
```

Puis complète.

---

# skip

```ts
of(1,2,3,4).pipe(
  skip(2)
);
```

Résultat :

```text
3
4
```

---

# distinctUntilChanged

Évite les doublons consécutifs.

```ts
of(1,1,2,2,3).pipe(
  distinctUntilChanged()
);
```

Résultat :

```text
1
2
3
```

---

# debounceTime

Très utilisé pour les champs de recherche.

```ts
search$.pipe(
  debounceTime(300)
);
```

Attend 300 ms sans saisie.

---

# throttleTime

```ts
click$.pipe(
  throttleTime(1000)
);
```

Limite les événements.

---

# 9. Les Subjects

Observable + Observer.

---

# Subject

```ts
const subject = new Subject<number>();

subject.subscribe(v => console.log('A', v));
subject.subscribe(v => console.log('B', v));

subject.next(1);
```

Résultat :

```text
A 1
B 1
```

---

# BehaviorSubject

Conserve la dernière valeur.

```ts
const user$ = new BehaviorSubject(null);
```

---

Valeur initiale obligatoire.

```ts
const count$ = new BehaviorSubject(0);
```

---

Lecture valeur courante :

```ts
count$.value;
```

---

# ReplaySubject

Rejoue plusieurs valeurs.

```ts
const subject = new ReplaySubject(3);
```

Conserve les 3 dernières.

---

# AsyncSubject

Émet uniquement la dernière valeur.

```ts
const subject = new AsyncSubject();
```

Après :

```ts
complete();
```

---

# 10. Gestion d'état avec RxJS

Pattern classique :

```ts
@Injectable()
export class UserStore {

  private userSubject =
    new BehaviorSubject<User | null>(null);

  user$ = this.userSubject.asObservable();

  setUser(user: User) {
    this.userSubject.next(user);
  }

}
```

Utilisation :

```ts
this.userStore.user$
  .subscribe(user => console.log(user));
```

---

# 11. RxJS dans Angular

Angular est construit autour de RxJS.

Exemples :

```ts
HttpClient
Router
Forms
Events
WebSockets
```

Tout renvoie des Observables.

---

# 12. HttpClient et RxJS

Service :

```ts
@Injectable()
export class UserService {

  constructor(
    private http: HttpClient
  ) {}

  getUsers() {
    return this.http.get<User[]>(
      '/api/users'
    );
  }

}
```

---

Composant :

```ts
users$ = this.userService.getUsers();
```

Template :

```html
@for(user of users$ | async; track user.id) {
  <p>{{ user.name }}</p>
}
```

---

# Pourquoi Angular utilise des Observables pour HTTP ?

Parce qu'on peut :

- annuler
- transformer
- combiner
- relancer

les requêtes.

---

# 13. Async Pipe

Mauvaise pratique :

```ts
ngOnInit() {
  this.service.getUsers()
    .subscribe(users => {
      this.users = users;
    });
}
```

---

Bonne pratique :

```ts
users$ = this.service.getUsers();
```

Template :

```html
{{ users$ | async | json }}
```

Angular gère :

```text
subscribe
unsubscribe
```

automatiquement.

---

# 14. Reactive Forms

Angular Forms utilise RxJS.

---

# valueChanges

```ts
this.form.valueChanges
  .subscribe(console.log);
```

---

# statusChanges

```ts
this.form.statusChanges
  .subscribe(console.log);
```

---

# Recherche temps réel

```ts
this.searchControl.valueChanges.pipe(

  debounceTime(300),

  distinctUntilChanged(),

  switchMap(query =>
    this.api.search(query)
  )

).subscribe();
```

Pattern ultra classique.

---

# 15. Combinaison de flux

---

# combineLatest

```ts
combineLatest([
  user$,
  settings$
]);
```

Émet lorsque les deux ont une valeur.

---

Exemple :

```ts
combineLatest([
  user$,
  permissions$
]).subscribe(
  ([user, permissions]) => {

  }
);
```

---

# forkJoin

Équivalent :

```ts
Promise.all()
```

```ts
forkJoin({
  users: this.api.users(),
  roles: this.api.roles()
});
```

Attend tout.

---

# zip

Synchronise émission par émission.

```ts
zip(
  observable1,
  observable2
);
```

---

# merge

Fusionne.

```ts
merge(
  click$,
  keyboard$
);
```

---

# concat

Exécute séquentiellement.

```ts
concat(
  req1$,
  req2$
);
```

---

# 16. Les opérateurs de flattening

Sujet fondamental.

---

# mergeMap

Tout exécuter en parallèle.

```ts
users$.pipe(
  mergeMap(user =>
    this.api.getOrders(user.id)
  )
);
```

---

# concatMap

Séquentiel.

```ts
orders$.pipe(
  concatMap(order =>
    save(order)
  )
);
```

---

# switchMap

Annule l'ancien flux.

```ts
search$.pipe(
  switchMap(query =>
    api.search(query)
  )
);
```

Recherche temps réel.

---

Utilisateur tape :

```text
a
an
ang
angu
angular
```

Les anciennes requêtes sont annulées.

---

# exhaustMap

Ignore tant qu'une requête est en cours.

```ts
loginClick$.pipe(
  exhaustMap(() =>
    authService.login()
  )
);
```

Empêche le double clic.

---

# Résumé

| Opérateur | Comportement |
|------------|-------------|
| mergeMap | parallèle |
| concatMap | séquentiel |
| switchMap | annule précédent |
| exhaustMap | ignore nouveau |

---

# 17. Gestion des erreurs

---

# catchError

```ts
this.api.getUsers().pipe(

  catchError(error => {

    console.error(error);

    return of([]);

  })

);
```

---

# retry

```ts
this.api.getUsers().pipe(
  retry(3)
);
```

---

# retryWhen

Personnalisation avancée.

```ts
retryWhen(...)
```

---

# finalize

```ts
.pipe(
  finalize(() => {
    this.loading = false;
  })
)
```

Toujours exécuté.

---

# 18. Désabonnement propre

---

# Ancienne méthode

```ts
private destroy$ =
  new Subject<void>();

ngOnInit() {

  this.user$
    .pipe(
      takeUntil(this.destroy$)
    )
    .subscribe();

}

ngOnDestroy() {

  this.destroy$.next();

  this.destroy$.complete();

}
```

---

# Angular moderne (16+)

```ts
import {
  takeUntilDestroyed
} from '@angular/core/rxjs-interop';
```

```ts
constructor() {

  this.user$
    .pipe(
      takeUntilDestroyed()
    )
    .subscribe();

}
```

Recommandé.

---

# 19. Partage de requêtes HTTP

Problème :

```ts
const users$ =
  this.http.get('/users');
```

Deux abonnements :

```ts
users$.subscribe();
users$.subscribe();
```

Deux appels HTTP.

---

Solution :

```ts
users$ = this.http.get('/users').pipe(

  shareReplay(1)

);
```

Un seul appel.

---

# 20. Architecture RxJS professionnelle

Service :

```ts
private usersSubject =
  new BehaviorSubject<User[]>([]);

users$ =
  this.usersSubject.asObservable();
```

Chargement :

```ts
loadUsers() {

  this.http.get<User[]>(
    '/users'
  ).subscribe(users => {

    this.usersSubject.next(users);

  });

}
```

---

Consommation :

```ts
users$ = this.store.users$;
```

---

Template :

```html
@for(user of users$ | async; track user.id) {

}
```

---

# 21. Pattern ViewModel (VM$)

Très utilisé dans les grosses applications Angular.

```ts
vm$ = combineLatest({

  users: this.users$,

  loading: this.loading$,

  selectedUser:
    this.selectedUser$

});
```

Template :

```html
@if(vm$ | async; as vm) {

  @if(vm.loading) {
    Chargement...
  }

  @for(user of vm.users; track user.id) {
    {{ user.name }}
  }

}
```

Un seul Observable pour la vue.

---

# 22. Cold Observable vs Hot Observable

## Cold

Chaque abonnement relance la source.

```ts
http.get('/users');
```

Chaque subscribe :

```text
nouvelle requête
```

---

## Hot

Source partagée.

```ts
BehaviorSubject
Subject
ReplaySubject
```

Tous les abonnés partagent le flux.

---

# 23. RxJS et Angular Signals

Depuis Angular 16+.

Conversion Observable → Signal :

```ts
import {
  toSignal
} from '@angular/core/rxjs-interop';
```

```ts
usersSignal =
  toSignal(users$);
```

---

Signal → Observable :

```ts
toObservable(signal);
```

---

Exemple moderne :

```ts
users$ =
  this.http.get<User[]>('/users');

users =
  toSignal(users$, {
    initialValue: []
  });
```

Template :

```html
@for(user of users(); track user.id) {

}
```

---

# 24. Erreurs fréquentes

## Subscribe imbriqué

Mauvais :

```ts
this.api.getUser()
  .subscribe(user => {

    this.api.getOrders(user.id)
      .subscribe();

});
```

---

Bon :

```ts
this.api.getUser().pipe(

  switchMap(user =>
    this.api.getOrders(user.id)
  )

);
```

---

## Subject partout

Mauvais :

```ts
new Subject();
new Subject();
new Subject();
```

Créer un Subject uniquement lorsqu'on doit pousser une valeur manuellement.

---

## Subscribe dans les services

Éviter :

```ts
loadUsers() {
  this.http.get(...).subscribe();
}
```

Préférer :

```ts
loadUsers() {
  return this.http.get(...);
}
```

Laisser le consommateur décider.

---

## Utiliser tap pour transformer

Mauvais :

```ts
tap(user => {
  user.name = 'X';
});
```

---

Bon :

```ts
map(user => ({
  ...user,
  name: 'X'
}));
```

---

# 25. Les opérateurs les plus utilisés en Angular

À maîtriser parfaitement :

```ts
map
filter
tap
take
takeUntil
switchMap
mergeMap
concatMap
exhaustMap
combineLatest
forkJoin
catchError
retry
debounceTime
distinctUntilChanged
shareReplay
finalize
```

---

# 26. Checklist Senior Angular

Un développeur Angular senior doit savoir :

✅ Différence Observable / Promise

✅ Cold vs Hot Observable

✅ Subject vs BehaviorSubject vs ReplaySubject

✅ switchMap / mergeMap / concatMap / exhaustMap

✅ combineLatest / forkJoin

✅ AsyncPipe

✅ takeUntilDestroyed

✅ shareReplay

✅ Gestion d'erreurs RxJS

✅ Reactive Forms + valueChanges

✅ Architecture Store avec BehaviorSubject

✅ Intégration Angular Signals ↔ RxJS

✅ Construction de ViewModel avec combineLatest

✅ Éviter les nested subscriptions

✅ Comprendre le cycle de vie complet d'un flux RxJS

---

# Conclusion

RxJS n'est pas simplement une bibliothèque d'Observables.

C'est un modèle complet de programmation permettant de représenter tout ce qui évolue dans le temps :

- événements utilisateur
- appels HTTP
- formulaires
- WebSockets
- état applicatif
- routing
- temps réel

La maîtrise de RxJS est l'une des compétences qui différencie le plus fortement un développeur Angular junior d'un développeur Angular senior, car Angular lui-même repose massivement sur les principes de programmation réactive.
