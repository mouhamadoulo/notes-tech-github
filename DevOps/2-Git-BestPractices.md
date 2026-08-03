Les conventions Git (et les bonnes pratiques) visent principalement à rendre l'historique clair, faciliter la collaboration et réduire les risques d'erreurs. Voici les recommandations les plus courantes.

## 1. Faire des commits petits et cohérents

Chaque commit devrait représenter une seule modification logique.

✅ Bon exemple :

* Ajouter la validation des emails
* Corriger un bug sur le paiement
* Renommer une classe

❌ À éviter :

* Ajouter une fonctionnalité
* Corriger trois bugs
* Mettre à jour la documentation
* Reformater tout le projet

Tout cela dans un seul commit rend les revues et les retours en arrière difficiles.

---

## 2. Écrire des messages de commit explicites

Le format le plus répandu est :

```
<type>: <description>
```

Exemples :

```
feat: ajouter la connexion OAuth
fix: corriger la validation du formulaire
docs: mettre à jour le README
refactor: simplifier le service utilisateur
test: ajouter les tests des permissions
style: corriger le formatage
chore: mettre à jour les dépendances
```

Les types les plus utilisés (inspirés de **Conventional Commits**) sont :

| Type     | Description                             |
| -------- | --------------------------------------- |
| feat     | Nouvelle fonctionnalité                 |
| fix      | Correction de bug                       |
| docs     | Documentation                           |
| style    | Formatage uniquement                    |
| refactor | Refactoring sans changement fonctionnel |
| test     | Ajout ou modification de tests          |
| chore    | Maintenance, dépendances, CI            |

---

## 3. Utiliser l'impératif

Git lui-même génère des messages à l'impératif.

Préférer :

```
fix: corriger le calcul des taxes
```

plutôt que

```
fix: correction du calcul des taxes
```

ou

```
fix: j'ai corrigé les taxes
```

L'idée est que le commit "applique" cette action.

---

## 4. Respecter une longueur raisonnable

Souvent :

* première ligne ≤ 50 caractères (ou environ 72 selon les équipes)
* si besoin, ajouter un corps explicatif

Exemple :

```
feat: ajouter le cache Redis

Réduit le nombre d'appels à la base de données.
Le cache expire après 5 minutes.
```

---

## 5. Utiliser des branches courtes

Noms fréquents :

```
feature/login
feature/payment-api

fix/cart-bug
bugfix/order-total

hotfix/security

refactor/auth

docs/api

release/2.4.0
```

Éviter :

```
test
nouvellebranche
branche2
```

---

## 6. Travailler via des Pull Requests / Merge Requests

Même dans une petite équipe :

* créer une branche
* ouvrir une PR/MR
* faire relire
* fusionner

Cela améliore :

* la qualité du code
* le partage des connaissances
* la traçabilité

---

## 7. Commiter souvent

Mieux vaut :

```
feat: ajouter le modèle User
feat: implémenter le repository
feat: créer l'API REST
test: ajouter les tests utilisateurs
```

que :

```
feat: terminer toute la gestion des utilisateurs
```

---

## 8. Ne jamais commiter

* mots de passe
* clés API
* certificats
* fichiers `.env`
* gros fichiers générés
* dossiers `node_modules`
* binaires compilés

Utiliser un `.gitignore` adapté au projet.

---

## 9. Garder `main` ou `master` stable

La branche principale devrait toujours être dans un état déployable.

On évite de développer directement dessus.

Flux classique :

```
main
  ↑
 merge
  ↑
feature/...
```

---

## 10. Rebase ou Merge ?

Deux approches sont courantes :

### Merge

Préserve l'historique réel.

```
A---B---C
     \   \
      D---E
```

Avantage :

* historique fidèle

---

### Rebase

Réécrit l'historique pour obtenir une suite linéaire.

```
A---B---C---D'---E'
```

Avantages :

* historique plus propre
* plus facile à lire avec `git log`

En revanche, il ne faut **pas** faire de `rebase` sur une branche déjà partagée sans coordination, car cela réécrit l'historique.

---

## 11. Squasher avant de merger

Pendant le développement, on peut avoir des commits comme :

```
fix
oops
test
encore
```

Avant la fusion :

```
feat: ajouter l'authentification JWT
```

L'historique principal reste lisible.

---

## 12. Utiliser des tags pour les versions

```
v1.0.0
v1.1.0
v2.0.0
```

Ils permettent d'identifier précisément les versions publiées.

---

## 13. Suivre le versionnement sémantique (SemVer)

Format :

```
MAJOR.MINOR.PATCH
```

* **MAJOR** : changement incompatible
* **MINOR** : nouvelle fonctionnalité compatible
* **PATCH** : correction de bug

Exemple :

```
1.4.2
 ↓
1.5.0   nouvelle fonctionnalité
 ↓
1.5.1   correction
 ↓
2.0.0   rupture de compatibilité
```

---

## 14. Intégrer fréquemment les changements

Ne laissez pas une branche vivre plusieurs semaines sans mise à jour.

Synchronisez régulièrement avec la branche principale (`merge` ou `rebase` selon la stratégie de l'équipe) pour limiter les conflits.

---

## 15. Configurer des protections sur la branche principale

Dans un dépôt partagé, il est recommandé de protéger `main` en exigeant notamment :

* des Pull Requests pour fusionner ;
* des revues de code ;
* le succès des tests automatisés (CI) ;
* l'interdiction des `push --force` sur la branche principale.

---

# Exemple d'un workflow simple

```text
main
 │
 ├── feature/login
 │      ↓
 │   commits
 │      ↓
 │   Pull Request
 │      ↓
 │   Review
 │      ↓
 └── Merge → main
```

Puis :

```
git checkout main
git pull

git checkout -b feature/login

# développement

git add .
git commit -m "feat: ajouter la page de connexion"

git push origin feature/login
```

## Résumé

Les principes les plus importants sont :

* Faire des commits **petits, atomiques et fréquents**.
* Écrire des **messages de commit clairs**, idéalement selon **Conventional Commits**.
* Développer sur des **branches dédiées** et fusionner via **Pull Requests/Merge Requests**.
* Maintenir une branche principale **stable et toujours déployable**.
* Utiliser une **CI**, des **revues de code** et des **tags** pour les versions.
* Éviter de versionner des secrets ou des fichiers générés.

Ces pratiques sont largement adoptées dans les équipes professionnelles et constituent une base solide pour des projets de toute taille.
