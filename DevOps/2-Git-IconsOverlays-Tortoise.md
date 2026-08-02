Sur Windows, l'absence des **icônes overlay TortoiseGit** (petites coches vertes, rouges, points d'exclamation sur les dossiers Git) est assez fréquente. L'installation de TortoiseGit seule ne garantit pas qu'elles apparaissent : Windows limite fortement le nombre d'overlay icons disponibles.

Voici les vérifications à faire.

---

## 1) Vérifier que les overlays TortoiseGit sont activés

Clic droit dans l'explorateur Windows :

**TortoiseGit → Settings**

Puis :

**Icon Overlays**

Vérifie que tu as quelque chose comme :

```
☑ Show overlays
```

et que les états sont cochés :

```
☑ Normal
☑ Modified
☑ Conflict
☑ Added
☑ Deleted
☑ Locked
☑ Read-only
```

Clique **Apply**.

---

## 2) Vérifier la priorité des overlays Windows (cause la plus probable)

Windows n'affiche qu'environ **15 overlay icons maximum**.

D'autres logiciels prennent souvent la place :

* OneDrive
* Dropbox
* Google Drive
* Antivirus
* SVN
* autres clients Git

### Ouvre Regedit

`Win + R`

Tape :

```
regedit
```

Va dans :

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
```

Tu devrais voir des entrées comme :

```
1TortoiseNormal
2TortoiseModified
3TortoiseConflict
...
```

Le problème est souvent que Windows trie par ordre alphabétique.

Si tu vois :

```
OneDrive1
OneDrive2
DropboxExt1
...
TortoiseGitNormal
```

TortoiseGit arrive trop bas.

---

## 3) Donner la priorité à TortoiseGit

Dans cette clé, renomme les entrées TortoiseGit en ajoutant des espaces devant :

Exemple :

Avant :

```
TortoiseGitNormal
TortoiseGitModified
```

Après :

```
   TortoiseGitNormal
   TortoiseGitModified
```

Les espaces les font remonter en haut de la liste.

Ou plus propre :

```
000TortoiseGitNormal
001TortoiseGitModified
002TortoiseGitConflict
```

---

## 4) Redémarrer Explorer Windows

Après modification :

Ouvre le gestionnaire des tâches :

```
CTRL + SHIFT + ESC
```

Trouve :

```
Explorateur Windows
```

Clic droit :

```
Redémarrer
```

Ou en terminal :

```powershell
taskkill /f /im explorer.exe
start explorer.exe
```

---

## 5) Vérifier que tes dossiers sont bien des dépôts Git

Les overlays ne s'affichent que si le dossier contient :

```
.git
```

Par exemple :

```
p1-basics
 ├── .git
 ├── README.md
 └── src

p2-services
 ├── .git
 ├── pom.xml
 └── src
```

Teste :

Dans le dossier :

```bash
git status
```

Tu dois obtenir :

```
On branch main
nothing to commit, working tree clean
```

---

## 6) Vérifier le mode d'affichage TortoiseGit

Toujours dans :

```
TortoiseGit → Settings → Icon Overlays
```

Vérifie :

```
Status cache :
```

Essaie :

```
☑ Default
```

ou :

```
☑ Shell Extended
```

Puis redémarre Explorer.

---

## 7) Cas fréquent avec Windows 11

Sur Windows 11, le cache Explorer peut être bloqué.

Dans PowerShell administrateur :

```powershell
ie4uinit.exe -show
```

puis :

```powershell
taskkill /f /im explorer.exe
start explorer.exe
```

---

## 8) Vérification rapide

Dans ton screenshot, je vois :

```
p1-basics
p2-services
```

mais aucune coche Git.

La première chose que je vérifierais dans ton cas :

1. `clic droit sur p2-services`
2. Est-ce que tu vois :

   ```
   TortoiseGit
      Commit
      Diff
      Log
   ```

Si oui → TortoiseGit fonctionne, c'est uniquement le problème d'overlay Windows.

Si non → problème d'association Git/TortoiseGit.

---

Vu que tu utilises **Windows 11 + Docker Desktop + WSL + Git** d'après tes précédents échanges, le cas le plus probable est **la limite des overlays Windows (OneDrive/Dropbox/antivirus qui passent devant TortoiseGit)**.

Tu peux m'envoyer une capture de :

```
TortoiseGit → Settings → Icon Overlays
```

et éventuellement le contenu de :

```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\ShellIconOverlayIdentifiers
```

et je te dirai exactement quoi modifier.
