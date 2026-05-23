# GIT (gestion de versions)

## 1. C’est quoi Git ?
Git est un système de contrôle de version.<br>
👉 Il sert à :
- garder l’historique de ton code
- revenir en arrière si tu casses tout
- travailler à plusieurs sans se marcher dessus
- tester des idées sans risquer le projet principal

📌 Important :
- Git ≠ GitHub
- Git = l’outil (en local)
- GitHub / GitLab / Bitbucket = des plateformes pour héberger des dépôts Git en ligne

## 2. Les concepts fondamentaux (le cœur de Git)
🔹 **Dépôt (repository)**<br>
C’est ton projet versionné.
- Local : sur ton ordinateur
- Distant : sur GitHub, GitLab…

🔹 **Les 3 zones de Git (super important)**<br>
*Working directory* → les fichiers tels qu’ils sont sur ton disque

*Staging area (index)* → les fichiers prêts à être sauvegardés

*Repository (.git)* → l’historique officiel

👉 Flux classique :
```
fichier modifié → add → commit
```

🔹 **Commit**
Un commit, c’est :
- une sauvegarde
- un instantané du projet
- avec un message explicatif

🧠 Bonne pratique :
- Un commit = une modification logique

## 3. Les commandes Git essentielles
🟢 **Démarrer un projet**
```bash
git init
```

🟢 **Voir l’état du projet**
```bash
git status
```

🟢 **Ajouter des fichiers au staging**
```bash
git add fichier.txt
git add .
```

🟢 **Créer un commit**
```bash
git commit -m "Message clair"
```

🟢 **Voir l’historique**
```bash
git log
git log --oneline
```

## 4. Les branches 🌿 (la magie de Git)
🔹 **C’est quoi une branche ?**<br>
Une branche est une ligne de développement indépendante.
- main (ou master) → version stable
- autres branches → nouvelles fonctionnalités, tests, correctifs

📌 Tu peux casser des trucs sans danger.

🔹 **Commandes importantes**
```bash
git branch            # voir les branches
git branch feature-x  # créer une branche
git checkout feature-x
git switch feature-x  # (plus moderne)
```

Créer et switcher :
```bash
git switch -c feature-x
```

🔹 **Fusionner une branche (merge)**
```bash
git checkout main
git merge feature-x
```

## 5. Les conflits 😱 (et pourquoi c’est normal)
Un conflit arrive quand :
- deux personnes modifient la même partie du fichier

Git te dit :
```"Je sais pas quoi choisir, aide-moi"```

👉 Tu ouvres le fichier, tu choisis, tu commits.

📌 Les conflits ne sont PAS une erreur, juste une discussion à résoudre.

## 6. Git et le travail en équipe
🔹 **Dépôt distant**
```bash
git remote add origin URL
```

🔹 **Envoyer ton code**
```bash
git push origin main
```

🔹 **Récupérer les changements**
```bash
git pull
```

🔹 **Récupérer sans fusionner**
```bash
git fetch
```

## 7. GitHub / GitLab : concepts clés
🔹 **Clone**
```bash
git clone URL
```

🔹 **Pull Request / Merge Request**<br>
C’est une demande pour fusionner une branche :
- revue de code
- discussions
- validation avant intégration

## 8. Annuler / corriger des erreurs (ultra utile)
🔙 Annuler un fichier non commit
```bash
git checkout -- fichier.txt
```

🔙 Annuler un add
```bash
git reset fichier.txt
```

🔙 Modifier le dernier commit
```bash
git commit --amend
```

🔙 Revenir à un ancien commit
```bash
git checkout <hash>
```

⚠️ Certaines commandes réécrivent l’historique → attention en équipe.

## 9. .gitignore 🙈
Fichier qui dit à Git :
```"Ignore ces fichiers"```

Exemples :
```bash
node_modules/
.env
*.log
```

## 10. Bonnes pratiques Git 🧠
- commits petits et fréquents
- messages clairs
- une branche = une fonctionnalité
- ne jamais push du code cassé
- ne pas versionner les secrets

## 11. Git avancé (quand tu seras à l’aise)
- ```rebase```
- ```cherry-pick```
- ```stash```
- hooks Git
- tags et releases
- workflows (Git Flow, trunk-based…)