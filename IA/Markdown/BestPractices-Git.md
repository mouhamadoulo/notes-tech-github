# ✅ Git Best Practices pour le développement d'applications

## 📁 1. Organisation du dépôt

- [ ] Utiliser un fichier `.gitignore` adapté au langage/framework utilisé
- [ ] Ne jamais versionner :
  - [ ] Les fichiers de configuration contenant des secrets (`.env`, clés API, mots de passe)
  - [ ] Les dépendances installées (`node_modules`, `vendor`, etc.)
  - [ ] Les fichiers générés automatiquement (`build`, `dist`, logs, caches)
- [ ] Garder une structure de projet claire et cohérente
- [ ] Ajouter un `README.md` expliquant :
  - [ ] Le but de l'application
  - [ ] Les prérequis
  - [ ] L'installation locale
  - [ ] Les commandes principales
  - [ ] Le processus de contribution
- [ ] Ajouter des fichiers de configuration utiles :
  - [ ] `.editorconfig`
  - [ ] Configuration du linter
  - [ ] Configuration du formatter
  - [ ] Fichiers de CI/CD

---

# 🌿 2. Gestion des branches

- [ ] Utiliser une stratégie de branches définie par l'équipe
- [ ] Éviter de développer directement sur `main` / `master`
- [ ] Protéger la branche principale :
  - [ ] Interdire les push directs
  - [ ] Exiger une Pull Request
  - [ ] Exiger une validation avant merge
  - [ ] Exécuter les tests automatiquement

## Convention de branches

- [ ] Utiliser des noms explicites :

```
feature/ajout-authentication
bugfix/correction-login
hotfix/correction-crash-production
chore/mise-a-jour-dependances
refactor/restructuration-api
```

- [ ] Garder les branches courtes et focalisées sur une seule fonctionnalité
- [ ] Supprimer les branches fusionnées

---

# 💾 3. Commits

## Création des commits

- [ ] Faire des commits petits et cohérents
- [ ] Un commit doit représenter une modification logique unique
- [ ] Éviter les commits énormes contenant plusieurs sujets
- [ ] Vérifier son code avant de commiter :

```bash
git diff
git status
```

* [ ] Ne jamais commiter du code cassé sur une branche partagée

---

## Messages de commit

* [ ] Utiliser des messages explicites
* [ ] Écrire les commits au présent :

✅ Bon :

```
Ajoute la validation du formulaire utilisateur
```

❌ Mauvais :

```
Modification formulaire
```

* [ ] Utiliser une convention de commit :

```
feat: ajout du système de notifications
fix: correction du calcul du panier
docs: mise à jour README
style: correction formatage
refactor: simplification service utilisateur
test: ajout tests API
chore: mise à jour dépendances
```

* [ ] Éviter les messages vagues :

```
update
changes
fix stuff
wip
```

---

# 🔄 4. Synchronisation avec le dépôt distant

* [ ] Récupérer régulièrement les changements :

```bash
git fetch
```

* [ ] Mettre à jour sa branche avant de créer une Pull Request
* [ ] Éviter les longues périodes sans synchronisation
* [ ] Résoudre les conflits localement avant le merge

---

# 🔀 5. Pull Requests (Merge Requests)

* [ ] Créer une Pull Request pour toute modification importante
* [ ] Fournir une description claire :

  * [ ] Pourquoi cette modification existe
  * [ ] Ce qui a changé
  * [ ] Comment tester
  * [ ] Captures d'écran si nécessaire
* [ ] Garder les Pull Requests petites
* [ ] Demander une revue de code
* [ ] Répondre aux commentaires avant le merge
* [ ] Vérifier que la CI passe avant fusion

---

# 👀 6. Revue de code

* [ ] Vérifier la lisibilité du code
* [ ] Vérifier la cohérence avec l'architecture existante
* [ ] Vérifier les performances
* [ ] Vérifier la sécurité
* [ ] Vérifier la couverture de tests
* [ ] Éviter les discussions personnelles : commenter le code, pas la personne
* [ ] Préférer les suggestions constructives

---

# 🧪 7. Tests et qualité du code

* [ ] Ajouter des tests avec chaque nouvelle fonctionnalité

* [ ] Corriger les tests cassés avant merge

* [ ] Automatiser :

  * [ ] Tests unitaires
  * [ ] Tests d'intégration
  * [ ] Lint
  * [ ] Formatage
  * [ ] Build

* [ ] Vérifier localement avant push :

```bash
npm test
npm run lint
npm run build
```

(adapter selon la technologie utilisée)

---

# 🔐 8. Sécurité Git

* [ ] Ne jamais commiter de secrets
* [ ] Utiliser des variables d'environnement
* [ ] Scanner les dépendances régulièrement
* [ ] Activer la double authentification sur les plateformes Git
* [ ] Utiliser des clés SSH plutôt que des mots de passe
* [ ] Révoquer les clés inutilisées
* [ ] Vérifier l'historique avant publication :

```bash
git log
git grep "password"
git grep "API_KEY"
```

---

# 🏷️ 9. Releases et versions

* [ ] Utiliser des tags Git pour les versions importantes :

```bash
git tag v1.0.0
git push --tags
```

* [ ] Suivre une convention de version :

```
MAJOR.MINOR.PATCH

1.0.0
1.1.0
1.1.1
```

* [ ] Documenter les changements dans un `CHANGELOG.md`
* [ ] Créer des releases officielles pour la production

---

# 🚀 10. Déploiement et CI/CD

* [ ] Automatiser les builds
* [ ] Automatiser les tests
* [ ] Déployer automatiquement depuis des branches contrôlées
* [ ] Séparer les environnements :
  * [ ] Développement
  * [ ] Staging
  * [ ] Production
* [ ] Vérifier qu'un déploiement peut être reproduit
* [ ] Garder une trace des versions déployées

---

# 🧹 11. Maintenance du dépôt

* [ ] Supprimer régulièrement :
  * [ ] Les branches obsolètes
  * [ ] Les fichiers inutilisés
  * [ ] Les dépendances mortes
* [ ] Garder les dépendances à jour
* [ ] Nettoyer les historiques inutiles si nécessaire
* [ ] Archiver les anciennes versions importantes

---

# 🛠️ 12. Commandes Git essentielles

* [ ] Vérifier l'état du dépôt :

```bash
git status
```

* [ ] Voir les modifications :

```bash
git diff
```

* [ ] Créer une branche :

```bash
git checkout -b feature/ma-feature
```

* [ ] Récupérer les changements :

```bash
git pull
```

* [ ] Préparer un commit :

```bash
git add .
```

* [ ] Créer un commit :

```bash
git commit -m "feat: ajout fonctionnalité"
```

* [ ] Envoyer ses changements :

```bash
git push
```

* [ ] Voir l'historique :

```bash
git log --oneline --graph
```

---

# ⭐ 13. Règles d'or

* [ ] Toujours savoir ce que contient un commit avant de le pousser
* [ ] Toujours pouvoir expliquer l'objectif d'une branche
* [ ] Ne jamais utiliser Git comme sauvegarde temporaire anarchique
* [ ] Préférer plusieurs petits commits propres à un gros commit
* [ ] Écrire l'historique Git pour les développeurs futurs
* [ ] Considérer Git comme une documentation vivante du projet

