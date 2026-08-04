# ✅ Best Practices Testing — Application Angular + Spring Boot

## 🧭 Stratégie générale de testing

- [ ] Définir une stratégie de test dès le début du projet
- [ ] Maintenir une pyramide de tests équilibrée :
  - [ ] Beaucoup de tests unitaires rapides
  - [ ] Plusieurs tests d'intégration
  - [ ] Quelques tests end-to-end couvrant les parcours critiques
- [ ] Éviter de tester uniquement les cas nominaux
- [ ] Tester les comportements attendus plutôt que l'implémentation interne
- [ ] Maintenir un objectif de couverture de code réaliste (éviter la couverture artificielle)
- [ ] Intégrer les tests dans la CI/CD
- [ ] Bloquer les merges si les tests critiques échouent
- [ ] Rendre les tests reproductibles et indépendants de l'ordre d'exécution


# 🟦 Frontend Angular — Tests unitaires

## 📦 Organisation des tests

- [ ] Placer les fichiers de tests à côté des fichiers sources (`*.spec.ts`)
- [ ] Utiliser une convention de nommage claire
- [ ] Organiser les tests par fonctionnalité plutôt que par type technique
- [ ] Garder les tests simples à lire et à maintenir


## 🧩 Tests des composants Angular

- [ ] Tester le rendu initial du composant
- [ ] Tester les interactions utilisateur importantes
- [ ] Tester les changements d'état du composant
- [ ] Tester les inputs (`@Input`)
- [ ] Tester les outputs (`@Output`)
- [ ] Vérifier les appels aux services dépendants
- [ ] Éviter de tester directement le DOM interne non nécessaire
- [ ] Préférer tester le comportement utilisateur


## 🔌 Tests des services Angular

- [ ] Tester chaque méthode publique du service
- [ ] Mock les appels HTTP externes
- [ ] Vérifier les paramètres envoyés aux APIs
- [ ] Vérifier le traitement des réponses
- [ ] Tester les erreurs HTTP
- [ ] Tester les cas limites
- [ ] Utiliser `HttpTestingController` pour les appels REST


## 🛣️ Tests des routes Angular

- [ ] Tester les guards
- [ ] Tester les redirections
- [ ] Vérifier les permissions nécessaires
- [ ] Tester les paramètres de route


## 📝 Tests des formulaires Angular

- [ ] Tester les validations obligatoires
- [ ] Tester les formats invalides
- [ ] Tester les messages d'erreur
- [ ] Tester les valeurs par défaut
- [ ] Tester les soumissions réussies
- [ ] Tester les soumissions échouées


## 🧠 Tests RxJS

- [ ] Tester les observables complexes
- [ ] Vérifier les comportements async
- [ ] Tester les erreurs dans les streams
- [ ] Vérifier les subscriptions importantes
- [ ] Éviter les subscriptions inutiles dans les composants


# 🟩 Frontend Angular — Tests d'intégration

## 🔗 Communication entre composants

- [ ] Tester les interactions parent/enfant
- [ ] Vérifier les événements déclenchés
- [ ] Vérifier les données affichées après modification d'état


## 🌐 Communication API

- [ ] Tester les scénarios complets avec API mockée
- [ ] Vérifier les loaders
- [ ] Vérifier les messages d'erreur utilisateur
- [ ] Vérifier les comportements en cas de timeout


# 🟨 Frontend Angular — Tests End-to-End

## 🚀 Parcours utilisateurs critiques

- [ ] Tester les parcours métier principaux
- [ ] Tester l'authentification
- [ ] Tester les permissions utilisateur
- [ ] Tester les formulaires critiques
- [ ] Tester les scénarios d'erreur importants

## 🛠️ Outils

- [ ] Utiliser un framework E2E adapté :
  - [ ] Playwright
  - [ ] Cypress
- [ ] Éviter les tests E2E trop nombreux et lents
- [ ] Utiliser des données de test contrôlées
- [ ] Éviter la dépendance aux données réelles


# 🟥 Backend Spring Boot — Tests unitaires

## 🧱 Tests des services métier

- [ ] Tester la logique métier indépendamment de la base de données
- [ ] Mock les dépendances externes
- [ ] Vérifier les règles métier
- [ ] Tester les exceptions métier
- [ ] Tester les cas limites
- [ ] Vérifier les valeurs retournées


## 🗄️ Tests des repositories

- [ ] Tester les requêtes personnalisées
- [ ] Vérifier les mappings JPA
- [ ] Tester les relations entre entités
- [ ] Tester les contraintes de données
- [ ] Utiliser des bases de données de test dédiées


## 🌐 Tests des controllers REST

- [ ] Tester chaque endpoint REST
- [ ] Vérifier les codes HTTP retournés
- [ ] Vérifier les payloads JSON
- [ ] Tester les validations entrantes
- [ ] Tester les erreurs HTTP
- [ ] Tester les paramètres obligatoires


# 🟪 Backend Spring Boot — Tests d'intégration

## 🔗 Couche application complète

- [ ] Tester les interactions Controller → Service → Repository
- [ ] Utiliser un contexte Spring réel lorsque nécessaire
- [ ] Vérifier la configuration Spring
- [ ] Tester les transactions
- [ ] Tester la sécurité Spring


## 🗃️ Base de données

- [ ] Utiliser une base de données dédiée aux tests
- [ ] Préférer Testcontainers pour reproduire l'environnement réel
- [ ] Tester les migrations SQL
- [ ] Vérifier la compatibilité avec la vraie base utilisée en production


# 🔐 Tests de sécurité

## Authentification

- [ ] Tester les utilisateurs authentifiés
- [ ] Tester les utilisateurs non authentifiés
- [ ] Tester les tokens expirés
- [ ] Tester les refresh tokens


## Autorisations

- [ ] Tester les rôles utilisateurs
- [ ] Tester les permissions par endpoint
- [ ] Vérifier les accès interdits


# 📊 Qualité et couverture

- [ ] Mesurer la couverture de code
- [ ] Éviter de viser 100% uniquement pour le chiffre
- [ ] Identifier les zones métier critiques
- [ ] Ajouter des tests sur les bugs corrigés
- [ ] Utiliser des outils d'analyse qualité :
  - [ ] SonarQube
  - [ ] JaCoCo
  - [ ] Coverage Angular


# ⚙️ CI/CD

## Pipeline

- [ ] Exécuter les tests automatiquement sur chaque Pull Request
- [ ] Séparer les tests rapides et les tests longs
- [ ] Générer les rapports de tests
- [ ] Publier les résultats de couverture
- [ ] Bloquer le déploiement si les tests critiques échouent


# 🧪 Données de test

- [ ] Ne jamais utiliser de données de production réelles
- [ ] Créer des fixtures dédiées
- [ ] Utiliser des builders/factories pour générer les objets de test
- [ ] Garder les jeux de données simples
- [ ] Nettoyer les données après les tests


# 🧹 Maintenance des tests

- [ ] Supprimer les tests obsolètes
- [ ] Refactorer les tests comme le code applicatif
- [ ] Éviter les duplications
- [ ] Garder les tests rapides
- [ ] Donner des noms explicites aux tests
- [ ] Documenter les scénarios complexes


# ⭐ Règles d'or

- [ ] Un test doit échouer uniquement lorsqu'un vrai problème existe
- [ ] Un test doit être facile à comprendre
- [ ] Un test doit être indépendant des autres
- [ ] Un test doit être stable dans le temps
- [ ] Tester le métier avant de tester la technique
- [ ] Préférer 100 tests utiles à 1000 tests fragiles
