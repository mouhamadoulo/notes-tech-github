
# Gestion des formulaires en Angular (versions 18, 19, 20 et 21)

> Ce tutoriel couvre les **trois approches** disponibles dans Angular moderne : les *Reactive Forms*, les *Template-Driven Forms*, et les nouvelles *Signal Forms* (expérimentales, introduites avec Angular 21). Tous les exemples utilisent la syntaxe **standalone** et le **nouveau control flow** (`@if`, `@for`), qui sont les valeurs par défaut depuis Angular 18.

---

## 1. Vue d'ensemble : quelle approche choisir ?

Angular propose plusieurs modèles de gestion de formulaires. Voici comment ils se positionnent :

| Approche | Module / Import | Disponibilité | Idéal pour |
|----------|----------------|---------------|------------|
| **Reactive Forms** | `ReactiveFormsModule` | Stable (toutes versions) | Formulaires complexes, validation dynamique, tests unitaires |
| **Template-Driven Forms** | `FormsModule` | Stable (toutes versions) | Formulaires simples, prototypage rapide |
| **Signal Forms** | `@angular/forms/signals` | **Expérimental (Angular 21+)** | Architecture 100 % signaux, type-safety renforcée |

Jusqu'à la version 20, seules les deux premières approches existaient. Avec Angular 21 (sorti le 20 novembre), une version expérimentale des **Signal Forms** a été introduite, construite intégralement sur les signaux.

> ⚠️ **Important** : les Signal Forms sont marquées **expérimentales** dans Angular 21 ; leur API peut changer. La stabilisation est prévue pour Angular 22. Pour la production aujourd'hui, privilégiez les **Reactive Forms**.

---

## 2. Reactive Forms (l'approche recommandée en production)

### 2.1 Principe

Le modèle du formulaire est défini **dans la classe du composant** (et non dans le template). Vous manipulez des objets `FormControl`, `FormGroup` et `FormArray` qui exposent l'état du formulaire de façon explicite et synchrone.

### 2.2 Configuration de base

```typescript
import { Component, inject } from '@angular/core';
import { FormBuilder, ReactiveFormsModule, Validators } from '@angular/forms';

@Component({
  selector: 'app-login',
  standalone: true,            // standalone est la valeur par défaut depuis Angular 17+
  imports: [ReactiveFormsModule], // import obligatoire pour les reactive forms
  templateUrl: './login.component.html',
})
export class LoginComponent {
  // inject() est la méthode moderne (préférée au constructeur)
  private readonly fb = inject(FormBuilder);

  // FormBuilder simplifie la création de la structure
  loginForm = this.fb.group({
    email: ['', [Validators.required, Validators.email]],
    password: ['', [Validators.required, Validators.minLength(8)]],
    rememberMe: [false],
  });

  onSubmit(): void {
    if (this.loginForm.valid) {
      console.log('Données :', this.loginForm.value);
    } else {
      // Marque tous les champs comme "touched" pour afficher les erreurs
      this.loginForm.markAllAsTouched();
    }
  }
}
```

**Explications ligne par ligne :**

- `inject(FormBuilder)` : injection moderne, équivalente au constructeur mais utilisable dans les *field initializers*.
- `this.fb.group({...})` : crée un `FormGroup`. Chaque entrée est un tableau `[valeurInitiale, validateurs]`.
- `Validators.required`, `Validators.email`, etc. : validateurs intégrés appliqués à la création du contrôle.

### 2.3 Le template associé

```html
<form [formGroup]="loginForm" (ngSubmit)="onSubmit()">
  <label>
    Email
    <input type="email" formControlName="email" />
  </label>

  <!-- Affichage conditionnel des erreurs avec le nouveau control flow @if -->
  @if (loginForm.controls.email.touched && loginForm.controls.email.invalid) {
    <div class="error">
      @if (loginForm.controls.email.hasError('required')) {
        <span>L'email est obligatoire.</span>
      }
      @if (loginForm.controls.email.hasError('email')) {
        <span>Format d'email invalide.</span>
      }
    </div>
  }

  <label>
    Mot de passe
    <input type="password" formControlName="password" />
  </label>

  @if (loginForm.controls.password.touched && loginForm.controls.password.hasError('minLength')) {
    <span class="error">Au moins 8 caractères requis.</span>
  }

  <label>
    <input type="checkbox" formControlName="rememberMe" /> Se souvenir de moi
  </label>

  <!-- Bouton désactivé tant que le formulaire est invalide -->
  <button type="submit" [disabled]="loginForm.invalid">Se connecter</button>
</form>
```

**Points clés :**

- `[formGroup]="loginForm"` lie le `FormGroup` au `<form>`.
- `formControlName="email"` relie chaque `<input>` à son contrôle par son nom.
- `(ngSubmit)` est préféré à `(submit)` : Angular gère automatiquement `preventDefault()`.

### 2.4 Formulaires typés (typed forms)

Depuis Angular 14, les *Reactive Forms* sont **fortement typés**. `FormBuilder` infère les types automatiquement. Pour éviter qu'un contrôle puisse redevenir `null` (lors d'un `reset()`), utilisez `nonNullable` :

```typescript
profileForm = this.fb.group({
  // Avec nonNullable, le type est `string` et non `string | null`
  username: this.fb.nonNullable.control('', Validators.required),
  age: this.fb.nonNullable.control(0),
});

// TypeScript connaît le type exact :
const name: string = this.profileForm.controls.username.value; // ✅ pas de null
```

### 2.5 Réagir aux changements avec `valueChanges`

Chaque contrôle expose un Observable `valueChanges`. Avec les versions modernes, on le convertit volontiers en signal via `toSignal` :

```typescript
import { toSignal } from '@angular/core/rxjs-interop';
import { debounceTime } from 'rxjs';

// Signal qui suit la valeur du champ "email" en temps réel
emailValue = toSignal(
  this.loginForm.controls.email.valueChanges.pipe(debounceTime(300)),
  { initialValue: '' }
);
```

---

## 3. Validation avancée (Reactive Forms)

### 3.1 Validateur personnalisé (synchrone)

Un validateur est une **fonction** qui reçoit le contrôle et renvoie soit `null` (valide), soit un objet `ValidationErrors` :

```typescript
import { AbstractControl, ValidationErrors, ValidatorFn } from '@angular/forms';

// Validateur : interdit les espaces dans un nom d'utilisateur
export function noWhitespaceValidator(): ValidatorFn {
  return (control: AbstractControl): ValidationErrors | null => {
    const hasWhitespace = /\s/.test(control.value ?? '');
    // Renvoie une clé d'erreur si invalide, sinon null
    return hasWhitespace ? { whitespace: true } : null;
  };
}

// Utilisation
username: ['', [Validators.required, noWhitespaceValidator()]],
```

### 3.2 Validateur croisé (cross-field)

Appliqué au **groupe entier** pour comparer plusieurs champs (ex. confirmation de mot de passe) :

```typescript
export const passwordsMatchValidator: ValidatorFn = (group: AbstractControl) => {
  const password = group.get('password')?.value;
  const confirm = group.get('confirmPassword')?.value;
  return password === confirm ? null : { passwordsMismatch: true };
};

// On passe le validateur en 2e argument du group()
signupForm = this.fb.group(
  {
    password: ['', Validators.required],
    confirmPassword: ['', Validators.required],
  },
  { validators: passwordsMatchValidator } // validateur de niveau groupe
);
```

### 3.3 Validateur asynchrone (ex. vérification serveur)

Renvoie un `Observable` ou une `Promise`. Utile pour vérifier l'unicité d'un email côté serveur :

```typescript
import { AsyncValidatorFn } from '@angular/forms';
import { map, catchError, of } from 'rxjs';

export function uniqueEmailValidator(api: UserService): AsyncValidatorFn {
  return (control) =>
    api.checkEmailExists(control.value).pipe(
      map((exists) => (exists ? { emailTaken: true } : null)),
      catchError(() => of(null)) // en cas d'erreur réseau, on ne bloque pas
    );
}

// Les validateurs async se passent dans un 3e tableau
email: ['', [Validators.required, Validators.email], [uniqueEmailValidator(this.userService)]],
```

Pendant la résolution, l'état du contrôle passe à `'PENDING'`. On peut afficher un *spinner* via `loginForm.controls.email.pending`.

---

## 4. Formulaires dynamiques avec `FormArray`

`FormArray` gère une **liste de taille variable** de contrôles (ex. ajouter plusieurs numéros de téléphone) :

```typescript
export class ContactFormComponent {
  private fb = inject(FormBuilder);

  form = this.fb.group({
    name: ['', Validators.required],
    phones: this.fb.array([this.createPhone()]), // démarre avec 1 champ
  });

  // Getter typé pour accéder facilement au FormArray dans le template
  get phones(): FormArray {
    return this.form.controls.phones;
  }

  private createPhone(): FormControl {
    return this.fb.control('', Validators.pattern(/^\+?\d{10,}$/));
  }

  addPhone(): void {
    this.phones.push(this.createPhone());
  }

  removePhone(index: number): void {
    this.phones.removeAt(index);
  }
}
```

```html
<div formArrayName="phones">
  @for (phone of phones.controls; track $index) {
    <div>
      <input [formControlName]="$index" placeholder="Téléphone" />
      <button type="button" (click)="removePhone($index)">Supprimer</button>
    </div>
  }
</div>
<button type="button" (click)="addPhone()">+ Ajouter un téléphone</button>
```

Notez l'utilisation de `track $index` dans `@for`, obligatoire dans le control flow moderne pour le suivi des éléments.

---

## 5. Template-Driven Forms (approche simple)

Ici, la logique vit **dans le template** via `ngModel`. Idéal pour des formulaires courts.

```typescript
import { Component } from '@angular/core';
import { FormsModule } from '@angular/forms';

@Component({
  selector: 'app-newsletter',
  standalone: true,
  imports: [FormsModule], // import différent des reactive forms
  templateUrl: './newsletter.component.html',
})
export class NewsletterComponent {
  model = { email: '', frequency: 'weekly' };

  submit(): void {
    console.log(this.model);
  }
}
```

```html
<!-- #form="ngForm" expose l'état du formulaire dans une variable de template -->
<form #form="ngForm" (ngSubmit)="submit()">
  <input
    name="email"
    type="email"
    required
    email
    [(ngModel)]="model.email"
    #emailRef="ngModel"
  />

  @if (emailRef.touched && emailRef.invalid) {
    <span class="error">Email obligatoire et valide.</span>
  }

  <button type="submit" [disabled]="form.invalid">S'inscrire</button>
</form>
```

- `[(ngModel)]` : liaison bidirectionnelle (two-way binding).
- `name` est **obligatoire** pour que `ngModel` enregistre le contrôle.
- `#form="ngForm"` et `#emailRef="ngModel"` exposent l'état pour le template.

---

## 6. Signal Forms (Angular 21+, expérimental) 🧪

C'est la **grande nouveauté** des versions récentes. Avec la sortie d'Angular 21 le 20 novembre, Angular a introduit les Signal Forms, qui offrent des formulaires composables et réactifs construits sur les signaux. Le principe inverse la logique habituelle : au lieu de construire le formulaire à partir de zéro, vous partez de votre modèle de données et laissez Angular générer la structure du formulaire automatiquement.

### 6.1 Concepts clés

Les Signal Forms abandonnent les notions de `FormGroup`, `FormControl` et `FormArray`. À la place, on définit un *form model* (un signal accessible en écriture qui initialise le formulaire) et la fonction `form()` renvoie un `FieldTree`. On peut se représenter un `FieldTree` comme un signal profondément imbriqué : chaque propriété de la structure de données est représentée par un signal portant son état (valeur, dirty, invalid…).

### 6.2 Exemple complet : formulaire de connexion

```typescript
import { Component, signal } from '@angular/core';
import { form, FormField, required, email, submit } from '@angular/forms/signals';

interface LoginData {
  email: string;
  password: string;
}

@Component({
  selector: 'app-login-signal',
  standalone: true,
  imports: [FormField], // directive [formField] au lieu de ReactiveFormsModule
  templateUrl: './login-signal.component.html',
})
export class LoginSignalComponent {
  // 1. Le modèle est un signal représentant la forme des données
  loginModel = signal<LoginData>({ email: '', password: '' });

  // 2. form() reçoit le modèle + une fonction "schema" décrivant les règles
  loginForm = form(this.loginModel, (path) => {
    required(path.email, { message: 'Email obligatoire' });
    email(path.email, { message: 'Format d\'email invalide' });
    required(path.password, { message: 'Mot de passe obligatoire' });
  });

  // 3. submit() n'exécute le callback que si le formulaire est valide
  onSubmit(event: Event): void {
    event.preventDefault();
    submit(this.loginForm, async () => {
      const credentials = this.loginModel();
      console.log('Connexion avec :', credentials);
    });
  }
}
```

**Différences fondamentales avec les Reactive Forms :**

- Au lieu de passer les validateurs à la création d'un contrôle, on appelle des fonctions qui pointent vers le champ : `required(path.email)`, `email(path.email)`, etc.
- Le modèle (`signal`) **est** la source de vérité : modifier le signal met à jour le formulaire, et inversement.

### 6.3 Le template avec `[formField]`

```html
<form (submit)="onSubmit($event)">
  <label>
    Email
    <input type="email" [formField]="loginForm.email" />
  </label>

  <!-- Accès à l'état du champ en l'appelant comme une fonction -->
  @if (loginForm.email().touched() && loginForm.email().invalid()) {
    @for (error of loginForm.email().errors(); track error.kind) {
      <span class="error">{{ error.message }}</span>
    }
  }

  <label>
    Mot de passe
    <input type="password" [formField]="loginForm.password" />
  </label>

  <!-- valid() et submitting() sont des signaux sur le formulaire racine -->
  <button
    type="submit"
    [disabled]="loginForm().invalid() || loginForm().submitting()"
  >
    Se connecter
  </button>
</form>
```

> 💡 Chaque champ expose son état via des signaux : `field().valid()`, `field().touched()`, `field().errors()`. On accède à l'état en appelant le champ comme une fonction, ce qui renvoie un objet `FieldState`.

### 6.4 Schémas réutilisables

Un *schema* permet de définir un ensemble de règles une seule fois et de les appliquer à plusieurs endroits. On utilise ensuite `apply` pour l'attacher à un sous-objet :

```typescript
import { schema, form, required, email, pattern, apply } from '@angular/forms/signals';

// Schéma réutilisable pour une adresse
const addressSchema = schema<Address>((addr) => {
  required(addr.street);
  required(addr.city);
  pattern(addr.zipCode, /^\d{5}$/);
});

userForm = form(this.userModel, (path) => {
  required(path.name);
  apply(path.address, addressSchema); // applique le schéma au sous-objet
});
```

Pour un tableau d'éléments, on utilise `applyEach` afin d'appliquer un schéma à **chaque** élément de la collection.

### 6.5 Validation conditionnelle avec `applyWhen` / `when`

Les Signal Forms simplifient énormément la validation conditionnelle. Auparavant, cela impliquait de s'abonner à `valueChanges`, de mettre à jour les validateurs manuellement et d'appeler `updateValueAndValidity()`. Désormais, un validateur peut prendre une condition `when`.

```typescript
form(this.model, (path) => {
  required(path.promoCode, {
    message: 'Code promo requis pour la réduction',
    // ne s'applique que si applyDiscount vaut true
    when: ({ valueOf }) => valueOf(path.applyDiscount),
  });
});
```

Ou avec un schéma entier conditionnel via `applyWhen` :

```typescript
import { applyWhen, required, pattern } from '@angular/forms/signals';

addressForm = form(this.addressModel, (path) => {
  applyWhen(
    path,
    ({ valueOf }) => valueOf(path.country) === 'FR',
    (p) => {
      required(p.zipCode);
      pattern(p.zipCode, /^\d{5}$/);
    }
  );
});
```

### 6.6 Gestion de la soumission et erreurs serveur

La fonction `submit()` orchestre tout le cycle de soumission. Elle marque les champs interactifs comme « touched », vérifie la validation (et stoppe si une règle échoue), exécute l'action (pendant laquelle `submitting()` renvoie `true`), puis route les éventuelles erreurs renvoyées vers les champs ciblés.

```typescript
import { submit, customError } from '@angular/forms/signals';

async onSubmit(): Promise<void> {
  await submit(this.userForm, async (form) => {
    const response = await this.api.save(form().value());

    // On peut renvoyer des erreurs serveur vers un champ précis
    if (response.error) {
      return [{
        field: this.userForm.email,
        error: customError({ kind: 'server', message: response.error }),
      }];
    }
    return undefined; // succès
  });
}
```

---

## 7. Tableau comparatif récapitulatif

| Critère | Reactive Forms | Template-Driven | Signal Forms (v21+) |
|---------|---------------|-----------------|---------------------|
| Source de vérité | Classe TS | Template | Signal (modèle) |
| Validation | `Validators` + fonctions | Directives HTML | Fonctions sur le `path` |
| Type-safety | Forte (depuis v14) | Faible | Très forte (dérivée du modèle) |
| Liaison | `formControlName` | `[(ngModel)]` | `[formField]` |
| Réactivité | Observables (`valueChanges`) | Détection de changement | Signaux natifs |
| Maturité | ✅ Stable production | ✅ Stable production | 🧪 Expérimental |

---

## 8. Bonnes pratiques

- **En production aujourd'hui** : utilisez les **Reactive Forms** typés avec `inject(FormBuilder)` et `nonNullable`. C'est l'approche éprouvée et la plus testable.
- **Affichage des erreurs** : ne les montrez qu'après interaction (`touched`/`dirty`) pour ne pas agresser l'utilisateur dès le chargement.
- **`@if` / `@for`** : utilisez systématiquement le control flow moderne (par défaut depuis Angular 18) plutôt que `*ngIf` / `*ngFor`.
- **Signal Forms** : explorez-les sur des projets neufs ou des branches expérimentales. Leur interopérabilité avec les Reactive Forms permet une adoption progressive sans réécriture complète. Évitez-les en production tant qu'elles ne sont pas stabilisées (prévu pour Angular 22).
- **Migration** : Angular fournit un mécanisme de compatibilité (`compatForm`) pour faire cohabiter Reactive Forms et Signal Forms le temps d'une transition.
