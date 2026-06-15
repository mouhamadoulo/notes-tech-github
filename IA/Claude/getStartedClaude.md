Je vais récupérer quelques pages détaillées de la documentation pour que les exemples de code et de configuration soient exacts.Je récupère encore les pages sur les hooks et MCP pour des exemples de configuration exacts.Je récupère la page MCP pour les commandes exactes d'ajout de serveurs.# Tutoriel complet : maîtriser Claude Code

Ce tutoriel couvre, dans l'ordre d'apprentissage, les notions essentielles de Claude Code : installation, interaction, mémoire, slash commands, skills, hooks, MCP, sous-agents et automatisation. Chaque section contient des exemples de commandes, de configuration ou de prompts, expliqués pas à pas.

---

## 1. Installation et premiers pas

Claude Code est un outil de codage agentique : il lit votre base de code, modifie des fichiers, exécute des commandes et s'intègre à vos outils. Il fonctionne sur plusieurs **surfaces** (terminal, VS Code, JetBrains, app desktop, web/mobile) reliées au même moteur — vos fichiers `CLAUDE.md`, vos réglages et vos serveurs MCP fonctionnent donc partout.

### Installer le CLI

```bash
# macOS, Linux, WSL
curl -fsSL https://claude.ai/install.sh | bash

# Windows PowerShell
irm https://claude.ai/install.ps1 | iex
```

### Lancer dans un projet

```bash
cd mon-projet
claude
```

Au premier lancement, on vous demande de vous connecter. Vérifiez votre version (certaines fonctionnalités, comme l'auto-mémoire, exigent une version récente) :

```bash
claude --version
```

### Le premier prompt

Une fois dans la session interactive, parlez en langage naturel :

```text
Explique-moi l'architecture de ce projet, puis liste les 3 fichiers les plus importants à comprendre en premier.
```

> **Conseil** : commencez par demander une *exploration* avant toute modification. Vous comprenez ce que Claude a compris, et vous évitez des changements prématurés.

---

## 2. Le CLI et les modes d'interaction

### Mode interactif vs mode non-interactif (`-p`)

Le mode interactif est la session classique. Le mode **non-interactif** (`-p`, dit *headless*) lit un prompt et renvoie un résultat, ce qui le rend scriptable et composable selon la philosophie Unix :

```bash
# Analyser un log et n'agir que si anomalie
tail -200 app.log | claude -p "Signale-moi toute anomalie dans ces logs"

# Revue de sécurité ciblée sur les fichiers modifiés
git diff main --name-only | claude -p "Passe en revue ces fichiers pour des problèmes de sécurité"
```

### Le mode plan

Le **mode plan** force Claude à proposer une approche *avant* d'écrire du code. Idéal pour les changements sensibles : vous validez le plan, puis Claude exécute. On l'active dans la session (raccourci de permission) ou via `--permission-mode plan`.

### Les permissions

Claude demande l'autorisation avant de modifier un fichier ou d'exécuter une commande. Les modes vont de `default` (demande à chaque action sensible) à `acceptEdits`, `auto`, jusqu'à `bypassPermissions` (à réserver aux environnements isolés). Pour inspecter et ajuster les règles en session :

```text
/permissions
```

---

## 3. La mémoire : `CLAUDE.md`, rules et auto-mémoire

Chaque session démarre avec un contexte vierge. Deux mécanismes transportent la connaissance d'une session à l'autre : les fichiers `CLAUDE.md` (que **vous** écrivez) et l'**auto-mémoire** (que **Claude** écrit).

### 3.1 `CLAUDE.md` : vos instructions persistantes

C'est un fichier markdown lu au début de chaque session. Générez-en un automatiquement :

```text
/init
```

Claude analyse le codebase et propose un `CLAUDE.md` avec commandes de build, de test et conventions détectées.

#### Où le placer (du plus large au plus spécifique)

| Portée | Emplacement | Usage |
| --- | --- | --- |
| Utilisateur | `~/.claude/CLAUDE.md` | Vos préférences, tous projets |
| Projet (partagé) | `./CLAUDE.md` ou `./.claude/CLAUDE.md` | Standards d'équipe, versionnés |
| Local (perso) | `./CLAUDE.local.md` | Préférences perso au projet (à gitignorer) |

#### Écrire de bonnes instructions

La règle d'or : **spécifique et concis**. Visez moins de 200 lignes. Comparez :

```markdown
# Bon (vérifiable)
- Utilise une indentation de 2 espaces
- Lance `npm test` avant chaque commit
- Les handlers d'API vivent dans `src/api/handlers/`

# Mauvais (vague)
- Formate le code proprement
- Teste tes changements
- Garde les fichiers organisés
```

#### Importer d'autres fichiers

La syntaxe `@chemin` insère le contenu d'un autre fichier dans le contexte au lancement :

```markdown
Voir @README pour l'aperçu et @package.json pour les commandes npm.

# Instructions additionnelles
- Workflow git : @docs/git-instructions.md
```

> **Astuce** : les commentaires HTML de bloc (`<!-- note pour les mainteneurs -->`) sont retirés avant injection dans le contexte : utilisez-les pour des notes humaines sans consommer de tokens.

### 3.2 Les rules ciblées (`.claude/rules/`)

Pour les gros projets, découpez vos instructions par thème dans `.claude/rules/`. Avantage majeur : on peut les **scoper à certains fichiers** via le frontmatter `paths`, pour qu'elles ne se chargent que quand c'est pertinent.

```markdown
---
paths:
  - "src/api/**/*.ts"
---

# Règles de développement d'API
- Tout endpoint doit valider ses entrées
- Utiliser le format d'erreur standard
- Inclure les commentaires de documentation OpenAPI
```

Une rule sans champ `paths` s'applique à tout. Structure type :

```text
mon-projet/
└── .claude/
    ├── CLAUDE.md
    └── rules/
        ├── code-style.md
        ├── testing.md
        └── security.md
```

### 3.3 L'auto-mémoire

Claude accumule seul des apprentissages (commandes de build, pièges de débogage, préférences) dans `~/.claude/projects/<projet>/memory/`, avec un `MEMORY.md` servant d'index chargé à chaque session. Pour inspecter, éditer ou désactiver :

```text
/memory
```

Quand vous dites en session « retiens qu'il faut toujours utiliser pnpm, pas npm », Claude l'enregistre en auto-mémoire. Pour mettre une instruction dans `CLAUDE.md` à la place, dites simplement « ajoute ça au CLAUDE.md ».

> **Distinction clé** : `CLAUDE.md` et auto-mémoire sont du **contexte**, pas une configuration imposée. Pour *bloquer* une action de façon garantie, il faut un **hook** (section 6), pas une instruction en mémoire.

---

## 4. Les slash commands utiles

Quelques commandes à connaître en session :

```text
/init        # Génère ou améliore le CLAUDE.md
/memory      # Voir/éditer les fichiers de mémoire
/hooks       # Inspecter les hooks configurés (lecture seule)
/mcp         # État des serveurs MCP, authentification OAuth
/status      # Modèle actif, mode, répertoire courant
/cost        # Consommation de tokens
/clear       # Repartir d'un contexte propre
/compact     # Compacter le contexte pour libérer de la place
/model       # Changer de modèle
```

---

## 5. Les skills : empaqueter des workflows réutilisables

Une **skill** est un dossier décrit par un fichier `SKILL.md` qui devient invocable (`/nom-skill`) ou chargé automatiquement quand c'est pertinent. Contrairement aux rules (toujours en contexte), une skill ne se charge **qu'au besoin** : c'est économe en tokens et idéal pour des procédures multi-étapes.

### Structure minimale

```text
.claude/skills/review-pr/
└── SKILL.md
```

### Exemple de `SKILL.md`

```markdown
---
name: review-pr
description: Revue de PR selon la checklist de l'équipe (tests, sécurité, style)
---

# Revue de Pull Request

Quand on t'invoque :
1. Récupère le diff de la branche courante vs `main`
2. Vérifie : couverture de tests, secrets en clair, respect du style
3. Produis un rapport en 3 sections : Bloquant / À améliorer / OK
4. Termine par une recommandation : merge / changements requis
```

Invocation :

```text
/review-pr
```

> **Quand utiliser quoi ?** Une info qui doit toujours être présente → `CLAUDE.md`. Une instruction liée à certains fichiers → une rule `paths`. Une procédure ponctuelle ou lourde → une **skill**.

---

## 6. Les hooks : automatiser et imposer des règles

Un **hook** est une commande shell (ou un endpoint HTTP, un prompt, un outil MCP) déclenchée automatiquement à un point précis du cycle de vie. Différence cruciale avec la mémoire : **un hook s'exécute toujours**, quelle que soit la décision du modèle. C'est l'outil pour les garanties dures (lint, formatage, blocage de commandes dangereuses).

### Où les définir

| Emplacement | Portée | Partageable |
| --- | --- | --- |
| `~/.claude/settings.json` | Tous vos projets | Non (local machine) |
| `.claude/settings.json` | Un projet | Oui (commitable) |
| `.claude/settings.local.json` | Un projet | Non (gitignoré) |

### Anatomie : événement → matcher → handler

La config a trois niveaux d'imbrication : l'**événement** (ex. `PreToolUse`), un **matcher** qui filtre (ex. seulement l'outil `Bash`), et un ou plusieurs **handlers** (la commande à lancer).

### Exemple 1 — Formater automatiquement après chaque édition

```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/format.sh",
            "args": []
          }
        ]
      }
    ]
  }
}
```

Ici `PostToolUse` se déclenche après une édition réussie ; le matcher `Edit|Write` cible les deux outils d'écriture ; le placeholder `${CLAUDE_PROJECT_DIR}` pointe vers la racine du projet.

### Exemple 2 — Bloquer une commande dangereuse (`PreToolUse`)

Le hook reçoit un JSON sur `stdin`. Pour **bloquer**, on sort en code 2 (le `stderr` est renvoyé à Claude) :

```bash
#!/bin/bash
# .claude/hooks/block-rm.sh
command=$(jq -r '.tool_input.command' < /dev/stdin)

if [[ "$command" == *"rm -rf"* ]]; then
  echo "Bloqué : les commandes rm -rf ne sont pas autorisées" >&2
  exit 2   # erreur bloquante : l'appel d'outil est empêché
fi

exit 0     # aucune décision : le flux de permission normal s'applique
```

Enregistré ainsi, avec un filtre fin `if` en syntaxe de permission :

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "hooks": [
          {
            "type": "command",
            "if": "Bash(rm *)",
            "command": "${CLAUDE_PROJECT_DIR}/.claude/hooks/block-rm.sh",
            "args": []
          }
        ]
      }
    ]
  }
}
```

Le champ `if: "Bash(rm *)"` évite de lancer le script si la commande ne ressemble pas à un `rm`, économisant le coût de spawn.

### Codes de sortie à retenir

| Code | Effet |
| --- | --- |
| `0` | Succès. Le `stdout` peut contenir du JSON de décision |
| `2` | **Erreur bloquante**. Le `stderr` est renvoyé à Claude ; l'action est bloquée (selon l'événement) |
| autre | Erreur non bloquante : l'exécution continue |

> **Attention** : seul le code `2` bloque. Un code `1` est traité comme une erreur **non** bloquante. Pour une vraie barrière, utilisez `exit 2` (ou le système de permissions).

### Événements les plus utiles

`SessionStart` (charger du contexte au démarrage), `UserPromptSubmit` (valider/enrichir un prompt), `PreToolUse` (autoriser/refuser avant un outil), `PostToolUse` (réagir après), `Stop` (en fin de tour). Inspectez ce qui est actif avec `/hooks`.

---

## 7. MCP : connecter des outils externes

Le **Model Context Protocol** est un standard ouvert qui branche Claude Code sur des sources externes (GitHub, Jira, Slack, Postgres, vos API maison…). Trois transports existent : **stdio** (processus local), **http** (service distant), **sse** (legacy, à remplacer par http).

### Ajouter un serveur

```bash
# Serveur local (stdio) — ex. Postgres
claude mcp add --transport stdio ma-db \
  -- npx @modelcontextprotocol/server-postgres postgresql://localhost:5432/mydb

# Serveur distant (http) — ex. Notion
claude mcp add --transport http notion https://mcp.notion.com/mcp

# Avec un token d'authentification
claude mcp add --transport http secure-api https://api.example.com/mcp \
  --header "Authorization: Bearer VOTRE_TOKEN"
```

### Les scopes (portées de configuration)

```bash
# Personnel (défaut) : seulement vous, stocké dans ~/.claude.json
claude mcp add --transport stdio ma-db -- npx ...

# Projet : partagé via .mcp.json, à committer pour toute l'équipe
claude mcp add --scope project --transport stdio ma-db -- npx ...
```

### Commandes de gestion

```bash
claude mcp list      # lister les serveurs configurés
claude mcp get NOM   # détails d'un serveur
claude mcp remove NOM
```

En session, `/mcp` affiche l'état des connexions et gère l'authentification OAuth. Les outils MCP apparaissent sous le nom `mcp__<serveur>__<outil>` (utile pour les cibler dans un hook, ex. `mcp__memory__.*`).

### Exemple de prompt exploitant MCP

```text
Récupère les tickets ouverts qui me sont assignés sur Jira, puis crée une branche
et un squelette de correctif pour le plus prioritaire.
```

---

## 8. Les sous-agents et équipes d'agents

Un **sous-agent** est une instance spécialisée à qui Claude délègue une partie du travail, avec son propre contexte. Deux intérêts : **paralléliser** (plusieurs agents sur des parties différentes) et **restreindre les capacités** (un agent de revue n'a qu'un accès lecture, par exemple).

### Définir un sous-agent personnalisé

Fichier dans `.claude/agents/`, au format frontmatter :

```markdown
---
name: security-reviewer
description: Audite le code pour des failles de sécurité, en lecture seule
tools: Read, Grep, Glob
---

Tu es un auditeur de sécurité. Pour chaque fichier fourni :
- Repère injections, secrets en clair, validations manquantes
- Classe les findings par sévérité
- Ne modifie jamais de fichier ; propose seulement des correctifs
```

### Lancer une délégation

```text
Utilise un sous-agent d'exploration pour cartographier le module d'authentification,
pendant qu'un autre repère tous les appels réseau non protégés.
```

Un agent principal coordonne, assigne les sous-tâches et fusionne les résultats. Pour des workflows entièrement sur mesure, l'**Agent SDK** permet de construire vos propres agents propulsés par les outils de Claude Code, avec contrôle total sur l'orchestration et les permissions.

---

## 9. L'automatisation et le travail à distance

### En CI/CD

Intégrez Claude à GitHub Actions ou GitLab CI/CD pour automatiser revues de PR et triage d'issues. Exemple de prompt headless en pipeline :

```bash
claude -p "Traduis les nouvelles chaînes en français et ouvre une PR pour relecture"
```

### Tâches récurrentes

- **Routines** : tournent sur l'infrastructure Anthropic (même PC éteint), déclenchables par planning, appel API ou événement GitHub. Création via `/schedule`, le web ou l'app desktop.
- **Tâches planifiées desktop** : tournent sur votre machine, avec accès à vos fichiers locaux.
- **`/loop`** : répète un prompt dans une session pour du polling rapide.

### Continuité multi-surfaces

```bash
# Démarrer une tâche longue sur le web, puis la rapatrier dans le terminal
claude --teleport
```

Vous pouvez aussi passer du terminal au desktop (`/desktop`) pour une revue visuelle des diffs, ou router une tâche depuis Slack en mentionnant `@Claude`.

---

## 10. Récapitulatif : par où commencer

| Étape | Action | Pourquoi |
| --- | --- | --- |
| 1 | `claude` dans un projet + exploration | Comprendre avant d'agir |
| 2 | `/init` puis affiner `CLAUDE.md` | Donner du contexte persistant |
| 3 | 2–3 hooks essentiels (lint, format, blocage `rm -rf`) | Des garanties dures |
| 4 | Découper en rules `paths` et créer 1–2 skills | Garder le contexte propre |
| 5 | Brancher 1 serveur MCP utile (GitHub, Jira…) | Connecter vos outils |
| 6 | Sous-agents, routines, SDK | Paralléliser et automatiser |

### Trois réflexes à garder

- **Spécificité** : « lance `npm test` avant commit » bat « teste ton code ».
- **Le bon mécanisme** : contexte permanent → `CLAUDE.md` ; règle conditionnelle → rule `paths` ; procédure ponctuelle → skill ; garantie absolue → hook.
- **Itérer** : la première fois que Claude répète une erreur, écrivez-la dans `CLAUDE.md`. Au fil du temps, `CLAUDE.md` devient la mémoire collective de l'équipe et les hooks ses réflexes.
