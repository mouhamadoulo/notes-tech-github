
I'll verify the current state of Angular testing tooling first, since this area has changed significantly in recent versions.# Tutoriel complet : les tests en Angular (versions 19, 20 et 21)

## 1. Ce qui a changé depuis Angular 18 (à lire avant tout)

Le paysage des tests Angular a profondément bougé sur les versions récentes. Trois bascules majeures structurent tout ce tutoriel :

- **Karma est déprécié** (depuis Angular 16) et **Vitest est devenu le runner de test par défaut** des nouveaux projets à partir d'Angular 21 ; l'intégration de Vitest dans le CLI est désormais considérée comme stable et prête pour la production. Karma n'accepte plus aucune nouvelle fonctionnalité ni correctif.
- **Le mode « zoneless » (sans Zone.js) est activé par défaut à partir d'Angular 21** : le `provideZonelessChangeDetection()` n'a plus besoin d'être importé dans la configuration d'application, et les nouvelles applications sont zoneless dès le départ. Cela change la façon d'écrire les tests.
- **`await fixture.whenStable()` est désormais préféré à `fixture.detectChanges()`** : pour que les tests se comportent au plus près du code de production, il faut éviter `fixture.detectChanges()` quand c'est possible.

> Distinction essentielle : un **framework de test** (Jasmine, Vitest, Jest) définit *comment on écrit* les tests (`describe`/`it`/`expect`). Un **runner** (Karma, Vitest) *exécute* ces tests. Historiquement la pile par défaut était Jasmine + Karma ; Vitest exécute les tests dans un processus Node.js via une couche d'émulation du DOM (`happy-dom` ou `jsdom`), sans lancer de navigateur.

Ce tutoriel privilégie l'approche **Angular 21 / Vitest / zoneless**, mais signale les équivalents Jasmine pour les projets en 19/20 encore sous Karma.

---

## 2. Les types de tests

| Type | Périmètre | Outils |
|------|-----------|--------|
| **Unitaire** | une classe / un pipe / un service isolé | Vitest (ou Jasmine), `TestBed` |
| **Intégration / composant** | un composant + son template + ses dépendances | `TestBed`, `ComponentFixture` |
| **Harness** | composant manipulé via une API stable | `@angular/cdk/testing` |
| **End-to-End (E2E)** | l'application entière dans un navigateur | Playwright, Cypress |

> Note historique : **Protractor est mort**. Angular recommande aujourd'hui Playwright, Cypress, WebdriverIO ou Nightwatch via `ng e2e`.

---

## 3. Mise en place

### 3.1 Nouveau projet (Angular 21+)

Avec un projet créé via `ng new`, tout est déjà configuré pour Vitest. Lancez simplement :

```bash
ng test                 # exécute les tests en mode watch
ng test --watch=false   # une seule passe (CI)
ng test --coverage      # avec couverture de code
```

Le `angular.json` pointe sur le nouveau builder :

```json
{
  "projects": {
    "mon-app": {
      "architect": {
        "test": {
          "builder": "@angular/build:unit-test",
          "options": {
            "runner": "vitest",
            "buildTarget": "::development"
          }
        }
      }
    }
  }
}
```

`@angular/build:unit-test` est le builder qui sait piloter Vitest ; `runner: "vitest"` choisit le moteur (on peut revenir à `"karma"` le temps d'une migration progressive).

### 3.2 Migration d'un projet existant (19/20) vers Vitest

```bash
# 1. mettre à jour Angular
ng update @angular/core @angular/cli

# 2. installer Vitest + une émulation de DOM
npm install --save-dev vitest jsdom @vitest/coverage-istanbul
```

Puis on remplace le builder `test` dans `angular.json` comme ci-dessus. La migration d'un projet existant vers Vitest est documentée comme expérimentale et nécessite le nouveau « application build system », qui est de toute façon le défaut des projets récents. Un schematic officiel aide à convertir les fichiers `.spec.ts`.

> ⚠️ Le `karma.conf.js` et les `test.ts` ne sont **pas** migrés automatiquement : auditez vos reporters et seuils de couverture personnalisés avant de supprimer Karma.

---

## 4. Anatomie d'un test : `TestBed` et `ComponentFixture`

Tout test de composant Angular tourne autour de deux objets :

- **`TestBed`** : un module Angular miniature, dédié au test. On y déclare le composant à tester et on y fournit ses dépendances (services, HTTP, router…).
- **`ComponentFixture`** : l'enveloppe autour d'une instance de composant. Elle donne accès à l'instance (`componentInstance`), au DOM (`nativeElement`, `debugElement`) et au cycle de détection de changement (`whenStable`, `detectChanges`).

```ts
import { TestBed } from '@angular/core/testing';
import { CompteurComponent } from './compteur.component';

describe('CompteurComponent', () => {
  // 1. On configure le module de test
  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [CompteurComponent], // composant standalone → on l'IMPORTE
    }).compileComponents();
  });

  it('devrait se créer', async () => {
    // 2. On instancie le composant
    const fixture = TestBed.createComponent(CompteurComponent);
    const composant = fixture.componentInstance;

    // 3. On déclenche la détection de changement initiale
    await fixture.whenStable();

    // 4. On vérifie
    expect(composant).toBeTruthy();
  });
});
```

Points clés :

- Depuis qu'Angular est **standalone par défaut**, on **importe** le composant dans `imports` plutôt que de le déclarer dans `declarations`.
- `compileComponents()` compile les templates et styles externes (utile, et inoffensif sinon).
- `describe` regroupe une suite de tests, `it` (ou `test`) définit un cas, `expect(...)` formule une assertion.

---

## 5. Le point central en mode zoneless : `whenStable()` vs `detectChanges()`

En **zoneless**, la détection de changement ne se déclenche plus automatiquement à chaque tâche asynchrone (HTTP, `setTimeout`, observables). Elle ne tourne que sur des déclencheurs explicites : modification d'un signal lu dans le template, événement de template, `markForCheck()`, etc.

Conséquence directe sur les tests :

```ts
// ❌ Ancienne habitude (toujours tolérée, mais déconseillée)
it('affiche la valeur (ancien style)', () => {
  const fixture = TestBed.createComponent(CompteurComponent);
  fixture.componentInstance.valeur = 1;
  fixture.detectChanges();                 // on force la CD à la main
  expect(fixture.nativeElement.textContent).toContain('1');
});

// ✅ Style recommandé en zoneless
it('affiche la valeur (style moderne)', async () => {
  const fixture = TestBed.createComponent(CompteurComponent);
  await fixture.whenStable();              // état initial stabilisé
  expect(fixture.nativeElement.textContent).toContain('0');

  fixture.componentInstance.valeur.set(1); // un signal modifié
  await fixture.whenStable();              // Angular planifie et exécute la CD
  expect(fixture.nativeElement.textContent).toContain('1');
});
```

`await fixture.whenStable()` attend que **toutes** les tâches asynchrones se vident et que la détection de changement planifiée se soit exécutée — c'est exactement ce qui se passe en production. On évite ainsi de masquer des bugs liés à une CD jamais réellement déclenchée.

> Pour forcer un test à se comporter exactement comme une app zoneless même quand Zone.js est encore chargé :
> ```ts
> TestBed.configureTestingModule({
>   providers: [provideZonelessChangeDetection()],
> });
> ```

---

## 6. Tester un composant : DOM, événements, classes CSS

Composant à tester :

```ts
import { Component, signal } from '@angular/core';

@Component({
  selector: 'app-compteur',
  template: `
    <p class="valeur">{{ valeur() }}</p>
    <button (click)="incrementer()">+1</button>
  `,
})
export class CompteurComponent {
  valeur = signal(0);
  incrementer() { this.valeur.update(v => v + 1); }
}
```

Test complet :

```ts
import { ComponentFixture, TestBed } from '@angular/core/testing';
import { By } from '@angular/platform-browser';
import { CompteurComponent } from './compteur.component';

describe('CompteurComponent', () => {
  let fixture: ComponentFixture<CompteurComponent>;

  beforeEach(async () => {
    await TestBed.configureTestingModule({
      imports: [CompteurComponent],
    }).compileComponents();
    fixture = TestBed.createComponent(CompteurComponent);
    await fixture.whenStable();
  });

  it('incrémente au clic sur le bouton', async () => {
    // On récupère le bouton via le DebugElement
    const bouton = fixture.debugElement.query(By.css('button'));

    // triggerEventHandler simule l'événement Angular
    bouton.triggerEventHandler('click');
    await fixture.whenStable();

    const valeur = fixture.nativeElement.querySelector('.valeur');
    expect(valeur.textContent).toBe('1');
  });
});
```

Deux façons d'accéder au DOM :

- **`fixture.nativeElement`** : l'élément HTML brut, idéal pour lire `textContent` ou utiliser `querySelector`.
- **`fixture.debugElement`** : abstraction Angular avec `query(By.css(...))`, `By.directive(...)` et surtout `triggerEventHandler(...)` qui simule un événement *Angular* (et non un événement DOM natif), ce qui déclenche correctement la liaison `(click)`.

---

## 7. Tester les signals (`signal`, `computed`, `effect`)

Les signals se testent souvent **sans `TestBed`** quand la logique est pure.

```ts
import { signal, computed } from '@angular/core';

it('computed se recalcule quand sa source change', () => {
  const prix = signal(100);
  const tva = computed(() => prix() * 0.2);

  expect(tva()).toBe(20);

  prix.set(200);
  expect(tva()).toBe(40); // recalcul automatique, lecture synchrone
});
```

Pour un `effect`, il faut un contexte d'injection : on passe par `TestBed.runInInjectionContext`, et l'effet s'exécute lors de la stabilisation.

```ts
import { TestBed } from '@angular/core/testing';
import { signal, effect, provideZonelessChangeDetection } from '@angular/core';

it('un effect réagit aux changements', async () => {
  TestBed.configureTestingModule({
    providers: [provideZonelessChangeDetection()],
  });

  const source = signal(0);
  const vues: number[] = [];

  TestBed.runInInjectionContext(() => {
    effect(() => vues.push(source()));
  });

  await TestBed.inject(ApplicationRef).whenStable(); // laisse tourner l'effet
  source.set(5);
  await TestBed.inject(ApplicationRef).whenStable();

  expect(vues).toEqual([0, 5]);
});
```

Le `computed` est synchrone et se lit directement ; l'`effect` est asynchrone (planifié par Angular), d'où l'attente de stabilisation.

---

## 8. Tester les `input()`, `output()` et `model()` (API signals)

Les composants modernes utilisent `input()`, `output()` et `model()` plutôt que `@Input`/`@Output`.

```ts
import { Component, input, output, model } from '@angular/core';

@Component({
  selector: 'app-carte',
  template: `
    <h3>{{ titre() }}</h3>
    <button (click)="supprimer.emit(titre())">Supprimer</button>
    <input [value]="nom()" (input)="nom.set($any($event.target).value)" />
  `,
})
export class CarteComponent {
  titre = input.required<string>();          // input signal obligatoire
  supprimer = output<string>();              // output signal
  nom = model('');                           // two-way binding signal
}
```

### Fournir un `input()` dans un test

On utilise **`fixture.componentRef.setInput('nom', valeur)`** — c'est la seule manière correcte d'alimenter un input signal :

```ts
it('affiche le titre fourni', async () => {
  const fixture = TestBed.createComponent(CarteComponent);
  fixture.componentRef.setInput('titre', 'Bonjour');
  await fixture.whenStable();

  expect(fixture.nativeElement.querySelector('h3').textContent).toBe('Bonjour');
});
```

### Tester un `output()`

Un `output()` expose `.subscribe()` ; on s'y abonne et on vérifie la valeur émise :

```ts
it('émet le titre quand on supprime', async () => {
  const fixture = TestBed.createComponent(CarteComponent);
  fixture.componentRef.setInput('titre', 'À supprimer');
  await fixture.whenStable();

  let recu: string | undefined;
  fixture.componentInstance.supprimer.subscribe(v => (recu = v));

  fixture.debugElement.query(By.css('button')).triggerEventHandler('click');
  expect(recu).toBe('À supprimer');
});
```

### Tester un `model()` (two-way)

`model()` se comporte à la fois comme un input (on l'alimente avec `setInput`) et comme un signal modifiable :

```ts
it('met à jour le model quand on tape', async () => {
  const fixture = TestBed.createComponent(CarteComponent);
  await fixture.whenStable();

  const champ = fixture.nativeElement.querySelector('input');
  champ.value = 'Alice';
  champ.dispatchEvent(new Event('input'));
  await fixture.whenStable();

  expect(fixture.componentInstance.nom()).toBe('Alice');
});
```

---

## 9. Tester un service et l'injection de dépendances

Un service sans dépendance se teste très simplement :

```ts
import { TestBed } from '@angular/core/testing';
import { PanierService } from './panier.service';

describe('PanierService', () => {
  let service: PanierService;

  beforeEach(() => {
    TestBed.configureTestingModule({});
    service = TestBed.inject(PanierService);
  });

  it('ajoute un article', () => {
    service.ajouter({ id: 1, nom: 'Livre' });
    expect(service.articles().length).toBe(1);
  });
});
```

Quand un service en dépend d'un autre, on **remplace la vraie dépendance par un faux** via `providers` :

```ts
beforeEach(() => {
  const fauxLogger = { log: vi.fn() }; // Vitest ; en Jasmine : jasmine.createSpyObj

  TestBed.configureTestingModule({
    providers: [
      PanierService,
      { provide: LoggerService, useValue: fauxLogger },
    ],
  });
});
```

`{ provide: X, useValue: Y }` est le mécanisme central : Angular injectera `Y` partout où `X` est demandé, ce qui isole le code testé de ses dépendances réelles.

---

## 10. Tester les appels HTTP

C'est l'un des cas les plus fréquents. On utilise **`provideHttpClient()`** et **`provideHttpClientTesting()`**, puis le `HttpTestingController` pour intercepter et simuler les réponses.

Service :

```ts
import { Injectable, inject, signal } from '@angular/core';
import { HttpClient } from '@angular/common/http';

@Injectable({ providedIn: 'root' })
export class UtilisateurService {
  private http = inject(HttpClient);
  utilisateurs = signal<Utilisateur[]>([]);

  charger() {
    this.http.get<Utilisateur[]>('/api/users')
      .subscribe(u => this.utilisateurs.set(u));
  }
}
```

Test :

```ts
import { TestBed } from '@angular/core/testing';
import { provideHttpClient } from '@angular/common/http';
import {
  provideHttpClientTesting,
  HttpTestingController,
} from '@angular/common/http/testing';
import { UtilisateurService } from './utilisateur.service';

describe('UtilisateurService', () => {
  let service: UtilisateurService;
  let httpMock: HttpTestingController;

  beforeEach(() => {
    TestBed.configureTestingModule({
      providers: [
        provideHttpClient(),         // le vrai HttpClient…
        provideHttpClientTesting(),  // …mais le backend est remplacé par un faux
      ],
    });
    service = TestBed.inject(UtilisateurService);
    httpMock = TestBed.inject(HttpTestingController);
  });

  afterEach(() => {
    httpMock.verify(); // échoue si une requête non attendue traîne
  });

  it('charge les utilisateurs', () => {
    service.charger();

    // On attend exactement une requête GET sur cette URL
    const req = httpMock.expectOne('/api/users');
    expect(req.request.method).toBe('GET');

    // On répond avec des données simulées
    req.flush([{ id: 1, nom: 'Jean' }]);

    expect(service.utilisateurs()).toEqual([{ id: 1, nom: 'Jean' }]);
  });

  it('gère une erreur 500', () => {
    service.charger();
    const req = httpMock.expectOne('/api/users');

    req.flush('Erreur serveur', { status: 500, statusText: 'Server Error' });
    // …vérifier ici votre logique de gestion d'erreur
  });
});
```

Le mécanisme :

- **`expectOne(url)`** déclare qu'on s'attend à exactement une requête correspondante (sinon le test échoue).
- **`req.flush(data)`** simule la réponse réussie ; **`req.flush(body, { status })`** simule une erreur.
- **`httpMock.verify()`** dans `afterEach` garantit qu'aucune requête n'a été oubliée.
- Aucun vrai appel réseau n'est jamais émis.

---

## 11. Mocks et espions (spies)

C'est le point où l'API diffère selon le framework.

**Avec Vitest (Angular 21+) :**

```ts
const espion = vi.fn().mockReturnValue(42);
espion(); // renvoie 42

const obj = { calcul: () => 1 };
vi.spyOn(obj, 'calcul').mockReturnValue(99);
expect(obj.calcul()).toBe(99);

// promesses / observables
const api = { get: vi.fn().mockResolvedValue({ ok: true }) };

// assertions sur les appels
expect(espion).toHaveBeenCalled();
expect(espion).toHaveBeenCalledWith();
expect(espion).toHaveBeenCalledTimes(1);
```

**Avec Jasmine (Angular 19/20 sous Karma) :**

```ts
const espion = jasmine.createSpy('mon-espion').and.returnValue(42);

const obj = { calcul: () => 1 };
spyOn(obj, 'calcul').and.returnValue(99);

// objet entièrement mocké
const faux = jasmine.createSpyObj('Service', ['get', 'save']);
faux.get.and.returnValue(of([]));

expect(espion).toHaveBeenCalled();
```

L'idée est identique dans les deux cas : isoler la fonction testée en contrôlant ce que renvoient ses collaborateurs et en vérifiant *comment* ils ont été appelés.

---

## 12. Tester le code asynchrone

Trois approches coexistent ; en zoneless, privilégiez les deux premières.

### 12.1 `async/await` + `whenStable()` (recommandé)

```ts
it('résout une promesse', async () => {
  const fixture = TestBed.createComponent(MonComposant);
  fixture.componentInstance.chargerDonnees(); // déclenche un appel async
  await fixture.whenStable();
  expect(fixture.componentInstance.pret()).toBe(true);
});
```

### 12.2 Observables et `firstValueFrom`

```ts
import { firstValueFrom } from 'rxjs';

it('émet la bonne valeur', async () => {
  const resultat = await firstValueFrom(service.flux$);
  expect(resultat).toBe('ok');
});
```

### 12.3 `fakeAsync` / `tick` (contrôle fin du temps)

`fakeAsync` reste disponible pour piloter le temps virtuellement (timers, debounce) :

```ts
import { fakeAsync, tick } from '@angular/core/testing';

it('déclenche après un debounce de 300ms', fakeAsync(() => {
  let appele = false;
  service.recherche('test', () => (appele = true));

  tick(299);
  expect(appele).toBe(false);

  tick(1);              // on avance le temps virtuel jusqu'à 300ms
  expect(appele).toBe(true);
}));
```

`tick(ms)` fait avancer un horloge virtuelle : aucun délai réel, des tests déterministes et instantanés. À réserver aux cas où l'on doit vraiment contrôler des timers.

---

## 13. Tester un pipe

Un pipe pur est une simple fonction : on le teste **sans `TestBed`**.

```ts
@Pipe({ name: 'tronquer' })
export class TronquerPipe implements PipeTransform {
  transform(valeur: string, max = 10): string {
    return valeur.length > max ? valeur.slice(0, max) + '…' : valeur;
  }
}
```

```ts
describe('TronquerPipe', () => {
  const pipe = new TronquerPipe();

  it('tronque au-delà de la limite', () => {
    expect(pipe.transform('Bonjour tout le monde', 7)).toBe('Bonjour…');
  });

  it('laisse intacte une chaîne courte', () => {
    expect(pipe.transform('Salut', 10)).toBe('Salut');
  });
});
```

---

## 14. Tester une directive

Une directive se teste via un **composant hôte** minimal qui l'utilise.

```ts
@Directive({ selector: '[appSurligner]' })
export class SurlignerDirective {
  private el = inject(ElementRef);
  @HostListener('mouseenter') entrer() {
    this.el.nativeElement.style.backgroundColor = 'yellow';
  }
}
```

```ts
@Component({
  template: `<p appSurligner>Texte</p>`,
  imports: [SurlignerDirective],
})
class HoteComponent {}

it('surligne au survol', async () => {
  const fixture = TestBed.createComponent(HoteComponent);
  await fixture.whenStable();

  const p = fixture.debugElement.query(By.css('p'));
  p.triggerEventHandler('mouseenter');
  await fixture.whenStable();

  expect(p.nativeElement.style.backgroundColor).toBe('yellow');
});
```

On crée un composant `HoteComponent` jetable dont le seul rôle est d'appliquer la directive, ce qui permet de l'exercer dans un vrai contexte de template.

---

## 15. Tester les formulaires réactifs

```ts
@Component({
  selector: 'app-connexion',
  imports: [ReactiveFormsModule],
  template: `
    <form [formGroup]="form">
      <input formControlName="email" />
      <button [disabled]="form.invalid">Valider</button>
    </form>
  `,
})
export class ConnexionComponent {
  form = new FormGroup({
    email: new FormControl('', [Validators.required, Validators.email]),
  });
}
```

```ts
it('le bouton est désactivé tant que le formulaire est invalide', async () => {
  const fixture = TestBed.createComponent(ConnexionComponent);
  await fixture.whenStable();
  const bouton = fixture.nativeElement.querySelector('button');

  expect(bouton.disabled).toBe(true); // email vide → invalide

  fixture.componentInstance.form.controls.email.setValue('a@b.com');
  await fixture.whenStable();

  expect(bouton.disabled).toBe(false);
});
```

On peut piloter le formulaire directement par son modèle (`setValue`) plutôt que par le DOM, ce qui rend le test plus lisible. On vérifie ensuite la répercussion sur le template (`disabled`).

> Note : Angular 21 introduit les **Signal Forms** expérimentaux (`@angular/forms/signals`). L'approche réactive ci-dessus reste la voie stable et recommandée pour la production.

---

## 16. Tester le routing avec `RouterTestingHarness`

L'outil moderne pour les tests de navigation est `RouterTestingHarness`, combiné à `provideRouter`.

```ts
import { TestBed } from '@angular/core/testing';
import { provideRouter } from '@angular/router';
import { RouterTestingHarness } from '@angular/router/testing';

it('navigue vers la page profil', async () => {
  TestBed.configureTestingModule({
    providers: [
      provideRouter([
        { path: 'profil/:id', component: ProfilComponent },
      ]),
    ],
  });

  const harness = await RouterTestingHarness.create();
  const composant = await harness.navigateByUrl('/profil/42', ProfilComponent);

  expect(composant).toBeInstanceOf(ProfilComponent);
  expect(harness.routeNativeElement?.textContent).toContain('42');
});
```

`navigateByUrl(url, Type)` effectue la navigation réelle dans le routeur de test, instancie le composant cible et le renvoie typé : on teste à la fois la résolution de route et le rendu.

---

## 17. Component Harnesses (Angular CDK)

Les **harnesses** offrent une API stable pour manipuler un composant sans dépendre de la structure interne de son DOM. Indispensables avec Angular Material.

```ts
import { TestbedHarnessEnvironment } from '@angular/cdk/testing/testbed';
import { MatButtonHarness } from '@angular/material/button/testing';
import { HarnessLoader } from '@angular/cdk/testing';

it('clique sur le bouton Material via le harness', async () => {
  const fixture = TestBed.createComponent(MonComposant);
  await fixture.whenStable();

  const loader: HarnessLoader =
    TestbedHarnessEnvironment.loader(fixture);

  const bouton = await loader.getHarness(MatButtonHarness);
  expect(await bouton.getText()).toBe('Envoyer');

  await bouton.click();
  await fixture.whenStable();
});
```

Avantage : si la version de Material change son HTML interne, votre test continue de fonctionner car il passe par l'API du harness (`getText()`, `click()`), pas par des sélecteurs CSS fragiles.

---

## 18. Couverture de code

```bash
ng test --coverage
```

Configuration des seuils dans `vitest.config.ts` (projets Vitest) :

```ts
import { defineConfig } from 'vitest/config';

export default defineConfig({
  test: {
    coverage: {
      provider: 'istanbul',          // ou 'v8'
      reporter: ['text', 'html'],
      thresholds: {
        statements: 80,
        branches: 75,
        functions: 80,
        lines: 80,
      },
    },
  },
});
```

Un build échoue si la couverture passe sous ces seuils — utile en intégration continue. Visez la **couverture de la logique métier critique** plutôt qu'un pourcentage global : 100 % de lignes couvertes ne garantit pas que les bons comportements sont vérifiés.

---

## 19. Tests End-to-End (E2E)

Angular ne fournit plus de solution E2E intégrée depuis l'abandon de Protractor. La commande `ng e2e` propose d'intégrer un outil tiers. Exemple avec **Playwright** :

```ts
import { test, expect } from '@playwright/test';

test('un utilisateur peut se connecter', async ({ page }) => {
  await page.goto('http://localhost:4200');
  await page.getByLabel('Email').fill('jean@exemple.com');
  await page.getByLabel('Mot de passe').fill('secret');
  await page.getByRole('button', { name: 'Se connecter' }).click();

  await expect(page.getByText('Bienvenue Jean')).toBeVisible();
});
```

Les tests E2E exercent l'application complète dans un vrai navigateur. Ils sont précieux mais lents et fragiles : gardez-les pour quelques parcours utilisateur critiques, et couvrez le reste avec des tests unitaires/composant rapides.

---

## 20. Bonnes pratiques de synthèse

```ts
// ✅ Un test = un comportement, un nom explicite
it('désactive le bouton quand le formulaire est invalide', () => { /* ... */ });

// ✅ Pattern Arrange / Act / Assert
it('ajoute un article au panier', () => {
  const service = TestBed.inject(PanierService);   // Arrange
  service.ajouter({ id: 1, nom: 'Livre' });        // Act
  expect(service.total()).toBe(1);                 // Assert
});
```

Principes à retenir :

- **Préférez `await fixture.whenStable()` à `fixture.detectChanges()`** : vos tests reflètent le comportement zoneless réel.
- **Testez le comportement, pas l'implémentation** : interrogez le DOM rendu et les sorties publiques, pas les détails internes.
- **Isolez les dépendances** avec `{ provide, useValue }`, `provideHttpClientTesting()` et des spies (`vi.fn()` / `jasmine.createSpy`).
- **`setInput` pour les `input()` signals**, `subscribe` pour les `output()`.
- **Un `afterEach(() => httpMock.verify())`** sur tout test HTTP pour ne rien laisser passer.
- **Pyramide des tests** : beaucoup d'unitaires rapides, quelques tests d'intégration, très peu d'E2E.
- **Évitez les attentes réelles** (`setTimeout` dans les tests) : utilisez `fakeAsync`/`tick` ou `whenStable`.

> Le sujet des tests touche au cœur de la fiabilité d'une application : la transition Karma → Vitest et l'arrivée du zoneless par défaut rendent les suites de tests plus rapides et plus fidèles au runtime de production. Sur un projet en 19 ou 20 encore sous Jasmine/Karma, les concepts (`TestBed`, `ComponentFixture`, harnesses, `HttpTestingController`) restent identiques : seuls le runner et l'API d'espionnage changent.
