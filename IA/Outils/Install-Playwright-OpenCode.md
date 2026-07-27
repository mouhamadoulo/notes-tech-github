Pour utiliser **Playwright** avec **OpenCode**, vous avez deux méthodes complémentaires selon votre objectif :

1. **Via le protocole MCP (`@playwright/mcp`)** : Donne à l'agent OpenCode un contrôle direct du navigateur en temps réel (navigation, clics, formulaires, screenshots) pendant vos sessions.
2. **Via un Skill local (`.opencode/skills/`)** : Enseigne à l'agent la méthodologie pour écrire, exécuter et maintenir vos suites de tests E2E Playwright dans votre projet.

---

### Option 1 : Connecter le serveur MCP Playwright à OpenCode (Recommandé)

Le serveur MCP officiel de Microsoft/Playwright permet à l'agent d'ouvrir un vrai navigateur pour interagir avec une application web.

#### Étape 1 : Prérequis système

Assurez-vous d'avoir Node.js installé sur votre machine et d'installer les navigateurs Playwright globalement si ce n'est pas déjà fait :

```bash
npx playwright install

```

#### Étape 2 : Configurer le serveur MCP dans OpenCode

Ouvrez votre fichier de configuration OpenCode global (`~/.config/opencode/opencode.json`) ou le fichier à la racine de votre projet (`.opencode/opencode.json`), puis ajoutez le serveur MCP :

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["-y", "@playwright/mcp@latest"]
    }
  }
}

```

#### Étape 3 : Vérification

Relancez OpenCode dans votre terminal. L'agent disposera désormais des outils `browser_navigate`, `browser_click`, `browser_take_screenshot`, etc.

---

### Option 2 : Ajouter le Skill Playwright (Workflow de Test E2E)

Si vous voulez qu'OpenCode apprenne à **rédiger du code de test Playwright** pour votre projet (au lieu de simplement naviguer), vous pouvez installer le Skill dédié.

#### Étape 1 : Télécharger le Skill

Dans le dossier de votre projet (ou dans votre dossier global utilisateur) :

```bash
# Installation globale pour votre utilisateur
git clone https://github.com/lackeyjb/playwright-skill.git ~/.config/opencode/skills/playwright

# OU installation locale dans un projet spécifique
git clone https://github.com/lackeyjb/playwright-skill.git .opencode/skills/playwright

```

#### Étape 2 : Utilisation dans le terminal OpenCode

L'agent détectera automatiquement le fichier `SKILL.md` à chaque fois que vous lui demanderez une tâche liée à Playwright :

* *"Rédige un test Playwright E2E pour le formulaire de connexion."*
* *"Ouvre l'application locale sur http://localhost:3000 et fais un screenshot du dashboard."*
* *"Exécute les tests Playwright et debug le sélecteur qui échoue."*
