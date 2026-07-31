Comme ton dépôt GitHub contient déjà un **README.md**, il a un historique différent de ton dépôt local. La bonne méthode est de rattacher le dépôt distant, récupérer son historique, puis fusionner avant de pousser.

### 1. Vérifie que tu es dans ton projet local

```bash
cd /chemin/vers/ton/projet
```

### 2. Ajoute le dépôt distant

```bash
git remote add origin https://github.com/TON_UTILISATEUR/NOM_DU_REPO.git
```

Vérifie :

```bash
git remote -v
```

### 3. Fais ton premier commit local (si ce n'est pas déjà fait)

```bash
git add .
git commit -m "Initial commit"
```

### 4. Récupère le README du dépôt GitHub et fusionne les historiques

```bash
git pull origin main --allow-unrelated-histories
```

> Si ta branche s'appelle `master` au lieu de `main`, remplace `main` par `master`.

S'il y a un conflit (par exemple sur `README.md`), Git te le signalera. Il faudra :

* éditer le fichier pour résoudre le conflit,
* puis :

```bash
git add README.md
git commit
```

### 5. Pousse ton projet

```bash
git push -u origin main
```

---

## Si ta branche locale s'appelle `master`

Tu peux soit pousser vers `master`, soit la renommer en `main` :

```bash
git branch -M main
git push -u origin main
```

---

## Vérifier le nom de la branche locale

```bash
git branch
```

Le `*` indique la branche courante.

---

### Erreur fréquente

Si tu fais directement :

```bash
git push -u origin main
```

tu obtiendras souvent :

```text
! [rejected] main -> main (fetch first)
```

car GitHub possède déjà un commit (le README). Il faut d'abord faire le `git pull ... --allow-unrelated-histories` pour fusionner les deux historiques.

Si tu me donnes la sortie de :

```bash
git branch
git remote -v
git status
```

je peux te dire exactement quelles commandes exécuter.
