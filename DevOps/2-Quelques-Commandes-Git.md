# Commandes GIT

## 🧳 git stash 
👉 Sert à mettre de côté temporairement des modifications non commitées
- Tu bosses sur un fichier mais tu dois changer de branche → stash
- Git sauvegarde ton travail dans une “pile” invisible
```Bash
git stash
```
✅ Résultat : ton dossier redevient propre (comme si rien n’avait été modifié)

## 🔄 git stash pop 
👉 Sert à récupérer ce que tu avais stashé
```Bash
git stash pop
```
- Réapplique les modifications
- Supprime le stash de la pile

⚠️ Peut créer des conflits si le code a changé entre-temps

## ✏️ git commit --amend -m "content and color"
👉 Sert à modifier le dernier commit
```Bash
git commit --amend -m "nouveau message"
```
Tu peux :
- changer le message
- ajouter des fichiers oubliés

⚠️ À éviter si le commit a déjà été push (sinon problème d’historique)

## ❌ git restore --staged index.html
👉 Sert à retirer un fichier de la zone de staging
```Bash
git restore --staged index.html
```
- Le fichier reste modifié
- Mais il n’est plus prêt à être commit

👉 équivalent moderne de :
```Bash
git reset HEAD index.html
```

## 🔥 git restore --source=HEAD --staged --worktree index.html
👉 Sert à annuler complètement les modifications d’un fichier
```Bash
git restore --source=HEAD --staged --worktree index.html
```
Supprime les changements dans :
- le staging
- le fichier local

💀 Résultat : retour à la version du dernier commit

## ↩️ git revert 07b925d5752226bbe4d6becd86c1f29d565abfa5
👉 Sert à annuler un commit sans casser l’historique
```Bash
git revert <commit>
```
- Crée un nouveau commit inverse
- Safe pour les projets en équipe

💡 Contrairement à reset, ça ne supprime rien

## 🔄 git reset
👉 Sert à revenir en arrière dans l’historique
```Bash
git reset
```
Il existe plusieurs modes :
- ```--soft``` → garde les fichiers staged
- ```--mixed``` (par défaut) → enlève du staging
- ```--hard``` 💀 → supprime tout

Ex :
```Bash
git reset --hard HEAD~1
```
⚠️ Peut détruire du travail → à utiliser avec précaution

## 🧬 git rebase
👉 Sert à réécrire l’historique en déplaçant des commits
```Bash
git rebase main
```
- Rejoue tes commits au-dessus d’une autre branche
- Rend l’historique linéaire (plus propre)

💡 Très utilisé avant un merge

⚠️ Dangereux si déjà partagé (comme amend)

## 🍒 git cherry-pick
👉 Sert à copier un commit spécifique dans ta branche
```Bash
git cherry-pick <commit>
```
- Tu prends UN commit précis
- Tu l’appliques ailleurs

💡 Utile si tu veux récupérer une feature sans tout merger

## 🧠 Résumé rapide
| Commande                    | Utilité principale             |
| --------------------------- | ------------------------------ |
| `git stash`                 | Mettre de côté temporairement  |
| `git stash pop`             | Récupérer stash                |
| `git commit --amend`        | Modifier dernier commit        |
| `git restore --staged`      | Enlever du staging             |
| `git restore --source=HEAD` | Annuler changements            |
| `git revert`                | Annuler proprement un commit   |
| `git reset`                 | Revenir en arrière (danger ⚠️) |
| `git rebase`                | Réécrire historique            |
| `git cherry-pick`           | Copier un commit précis        |
