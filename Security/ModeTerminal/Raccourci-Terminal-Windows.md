Sur Windows Terminal (avec **PowerShell**, qui est le shell par défaut), la méthode est un peu différente de Linux/macOS. Vous avez le choix entre **un alias** ou **une fonction dans votre profil PowerShell**.

---

### 1. La méthode recommandée : Créer une fonction dans le profil

C'est la méthode la plus flexible : elle fonctionne pour les commandes simples, les commandes avec arguments, et reste permanente.

#### Étape A : Ouvrir votre fichier de profil PowerShell

Dans votre terminal, tapez :

```powershell
notepad $PROFILE

```

*Si le système indique que le fichier n'existe pas*, créez-le d'abord avec cette commande puis relancez la précédente :

```powershell
New-Item -Path $PROFILE -Type File -Force

```

#### Étape B : Ajouter votre raccourci

Dans le fichier Bloc-notes qui s'ouvre, ajoutez une fonction :

```powershell
function monraccourci {
    votre commande complete ici
}

```

*Exemples concrets :*

```powershell
# Raccourci simple pour mettre à jour Winget
function update-all {
    winget upgrade --all
}

# Raccourci avec argument (ex: git commit)
function gcmp ($msg) {
    git add .
    git commit -m $msg
    git push
}

```

#### Étape C : Enregistrer et recharger

1. Enregistrez le fichier (`Ctrl + S`) et fermez le Bloc-notes.
2. Rechargez votre profil dans le terminal :

```powershell
. $PROFILE

```

Désormais, il vous suffit de taper `monraccourci` (ou `gcmp "mon message"`) directement.

---

### 2. Pour les commandes courtes simples : Les alias PowerShell

Si vous voulez juste associer un mot court à un exécutable existant (sans paramètres complexes), vous pouvez utiliser `Set-Alias` dans le fichier `$PROFILE` :

```powershell
Set-Alias -Name np -Value notepad

```

---

### 💡 Astuce : Recherche dans l'historique Windows Terminal

Tout comme sur Linux, vous pouvez retrouver une commande déjà tapée sans créer de raccourci :

* Appuyez sur **`Ctrl` + `R**` et commencez à taper un morceau de la commande.
* Appuyez sur la touche **`F7`** pour afficher une liste déroulante interactive de tout votre historique de commandes récentes.
