Là tu n’as plus le problème ARM/FFI — tu as un **problème de PATH entre Windows et WSL**.

---

## 🧠 Ce qui se passe

Quand tu tapes :

```bash
opencode
```

WSL essaie d’exécuter :

```
/mnt/c/Users/mouha/AppData/Roaming/npm/opencode
```

👉 Donc il prend **le binaire Windows npm global**, pas celui installé via Bun dans WSL.

Or ce fichier Windows :

* n’existe plus (`No such file or directory`)
* donc crash immédiat

---

## 🎯 Cause réelle

Tu as **2 installations mélangées** :

* Windows :

  * npm global (`C:\Users\...\AppData\Roaming\npm`)
* WSL :

  * Bun + `bun add -g`
  * opencode-ai installé dans WSL

👉 MAIS ton shell WSL priorise encore le PATH Windows.

---

## 🧪 Vérification rapide

Dans WSL :

```bash
which opencode
```

Tu vas probablement voir :

```
/mnt/c/Users/.../npm/opencode
```

---

## ✅ Solution propre (recommandée)

### 🧹 1. Supprimer le conflit Windows dans WSL PATH

Édite ton `.bashrc` ou `.zshrc` :

```bash
nano ~/.bashrc
```

Ajoute à la fin :

```bash
export PATH=$(echo "$PATH" | tr ':' '\n' | grep -v "/mnt/c/Users" | paste -sd ':' -)
```

Puis recharge :

```bash
source ~/.bashrc
```

---

## 🔁 2. Vérifie que Bun est bien prioritaire

```bash
which bun
which opencode
```

Tu veux obtenir :

```
~/.bun/bin/bun
~/.bun/bin/opencode
```

---

## 🔁 3. Réinstalle opencode proprement dans WSL

```bash
bun remove -g opencode-ai
bun add -g opencode-ai
```

Puis :

```bash
hash -r
```

---

## 🚀 4. Test final

```bash
opencode --version
opencode
```

---

## 💡 Alternative encore plus simple (si tu veux éviter les surprises)

Forcer l’exécution directe :

```bash
~/.bun/bin/opencode
```

---

## ⚠️ Pourquoi ça arrive souvent sur WSL

WSL hérite automatiquement du PATH Windows, donc :

* npm Windows
* node Windows
* pnpm Windows

peuvent “casser” les outils Linux installés dans WSL.

---

## 👍 Conclusion

Ton installation est bonne — c’est juste un **conflit de PATH Windows ↔ WSL**.

---

Si tu veux, je peux aussi t’aider à :

* isoler complètement WSL (mode dev propre sans Windows PATH)
* ou configurer Bun + Node proprement sans conflits
* ou diagnostiquer ton environnement dev Angular dans WSL

Dis-moi 👍
