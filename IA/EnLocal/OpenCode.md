Voici le moyen le plus simple d'installer et de lancer **OpenCode**.

### 1. Installer OpenCode

#### macOS / Linux / WSL (recommandé)

```bash
curl -fsSL https://opencode.ai/install | bash
```

Ou, si vous préférez utiliser npm :

```bash
npm install -g opencode-ai
```

Si vous utilisez Homebrew :

```bash
brew install anomalyco/tap/opencode
```

Ces méthodes sont celles recommandées par la documentation officielle. ([OpenCode][1])

---

### 2. Vérifier l'installation

Dans un terminal :

```bash
opencode --version
```

Si un numéro de version s'affiche, l'installation est réussie. ([OpenCode Guide][2])

---

### 3. Configurer un modèle IA

Au premier lancement, OpenCode a besoin d'un fournisseur de modèles (OpenAI, Anthropic, Ollama, etc.).

Lancez :

```bash
opencode
```

Puis, dans l'interface, utilisez la commande :

```
/connect
```

Vous pourrez ensuite choisir votre fournisseur et renseigner votre clé API (ou configurer un modèle local comme Ollama). ([OpenCode][1])

---

### 4. Utilisation

Pour ouvrir OpenCode dans le dossier courant :

```bash
cd mon-projet
opencode
```

Vous pourrez ensuite demander par exemple :

```
Explique ce projet.
```

ou

```
Ajoute une API REST.
```

---

### Si vous utilisez Ollama (modèles locaux)

Après avoir installé Ollama et téléchargé un modèle, vous pouvez lancer OpenCode avec :

```bash
ollama launch opencode
```

Cela configure automatiquement OpenCode pour utiliser Ollama. ([Ollama][3])

---

Si vous me dites votre système d'exploitation (**Windows, macOS ou Linux**), je peux vous donner les commandes exactes adaptées à votre environnement.

[1]: https://opencode.ai/docs?utm_source=chatgpt.com "Intro | AI coding agent built for the terminal"
[2]: https://opencodeguide.com/en/installation/?utm_source=chatgpt.com "Install OpenCode on Mac & Linux | OpenCode Guide"
[3]: https://docs.ollama.com/integrations/opencode?utm_source=chatgpt.com "OpenCode - Ollama"
