# Angular Best Practices Checklist

## 🏗️ Architecture & Organisation

- [ ] Organiser le projet par fonctionnalité (feature modules) plutôt que par type de fichier
- [ ] Respecter la séparation des responsabilités (Component / Service / State / API)
- [ ] Garder les composants simples et orientés affichage
- [ ] Centraliser la logique métier dans les Services
- [ ] Éviter les composants trop volumineux
- [ ] Réutiliser les composants communs (`shared`)
- [ ] Respecter les conventions Angular (`core`, `shared`, `features`)

---

## 📁 Structure du projet

- [ ] Utiliser une structure claire :
```
src/app
├── core
│ ├── guards
│ ├── interceptors
│ └── services
│
├── shared
│ ├── components
│ ├── directives
│ └── pipes
│
└── features
├── users
├── orders
└── dashboard
```

- [ ] Mettre les éléments globaux dans `core`
- [ ] Mettre les composants réutilisables dans `shared`
- [ ] Isoler les fonctionnalités métier dans `features`

---

## 🧩 Components

- [ ] Garder les composants responsables uniquement de l'affichage
- [ ] Éviter la logique métier dans les templates
- [ ] Utiliser `OnPush` pour améliorer les performances
- [ ] Utiliser des `Input` / `Output` pour communiquer entre composants
- [ ] Éviter les appels HTTP directement dans les composants
- [ ] Nettoyer les subscriptions lors de la destruction du composant

---

## 🔄 Gestion des données & Services

- [ ] Centraliser les appels HTTP dans des Services
- [ ] Utiliser `HttpClient`
- [ ] Éviter les duplications d'appels API
- [ ] Utiliser RxJS correctement (`Observable`, `pipe`, opérateurs)
- [ ] Préférer `async pipe` plutôt que des `subscribe()` manuels
- [ ] Gérer les états applicatifs avec une solution adaptée (Signals, NgRx, services state)

---

## 🌐 API & Communication Backend

- [ ] Utiliser des modèles TypeScript (`interfaces`, `types`)
- [ ] Ne jamais manipuler des objets API non typés
- [ ] Centraliser la gestion des erreurs HTTP
- [ ] Utiliser des interceptors pour :
  - [ ] Authentification
  - [ ] Gestion des tokens
  - [ ] Logs
  - [ ] Gestion globale des erreurs

---

## 🧭 Routing

- [ ] Utiliser le lazy loading pour les grosses fonctionnalités
- [ ] Protéger les routes sensibles avec des guards
- [ ] Éviter les routes trop complexes
- [ ] Centraliser les constantes de navigation
- [ ] Prévoir une page 404

---

## 🔐 Sécurité

- [ ] Ne jamais stocker de données sensibles en clair
- [ ] Éviter `localStorage` pour les données critiques
- [ ] Valider les entrées utilisateur
- [ ] Éviter l'utilisation de `innerHTML`
- [ ] Utiliser les mécanismes Angular contre le XSS
- [ ] Contrôler les permissions côté frontend et backend

---

## 🎨 Templates & UI

- [ ] Éviter les expressions complexes dans les templates
- [ ] Utiliser des pipes pour les transformations simples
- [ ] Créer des composants UI réutilisables
- [ ] Uniformiser les styles avec une librairie UI (Material, PrimeNG, etc.)
- [ ] Respecter les règles d'accessibilité (ARIA, navigation clavier)

---

## 🧪 Tests

- [ ] Tester les composants critiques
- [ ] Tester les services métier
- [ ] Tester les guards et interceptors
- [ ] Tester les formulaires complexes
- [ ] Utiliser des tests unitaires automatisés
- [ ] Maintenir une couverture de test suffisante

---

## 📝 Qualité du code

- [ ] Utiliser TypeScript strict mode
- [ ] Éviter `any`
- [ ] Définir des interfaces pour les objets métier
- [ ] Utiliser ESLint + Prettier
- [ ] Respecter les conventions de nommage Angular
- [ ] Faire des revues de code
- [ ] Éviter le code dupliqué

---

## ⚡ Performance

- [ ] Utiliser `ChangeDetectionStrategy.OnPush`
- [ ] Activer le lazy loading
- [ ] Optimiser les listes avec `trackBy`
- [ ] Éviter les appels API inutiles
- [ ] Limiter les re-renders inutiles
- [ ] Optimiser la taille des bundles
- [ ] Utiliser le cache lorsque pertinent

---

## 🚀 CI/CD & Production

- [ ] Automatiser lint + tests + build
- [ ] Vérifier les erreurs TypeScript en CI
- [ ] Scanner les dépendances vulnérables
- [ ] Utiliser des variables d'environnement
- [ ] Générer un build optimisé production
- [ ] Surveiller les erreurs frontend

---

# ✅ Checklist avant mise en production

- [ ] Build production OK
- [ ] Tests automatisés OK
- [ ] Lint sans erreur
- [ ] Pas de `any` inutile
- [ ] Gestion globale des erreurs active
- [ ] Routes protégées
- [ ] Performances vérifiées
- [ ] Documentation disponible
