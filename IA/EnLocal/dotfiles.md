Dans le contexte de l'IA agentique, les **dotfiles** sont des fichiers de configuration (souvent cachés car leur nom commence par un point `.` sur les systèmes Unix/Linux/macOS) qui permettent de personnaliser le comportement d'un agent IA, de son environnement ou des outils qu'il utilise.

Le terme **dotfiles** ne vient pas de l'IA : c'est un concept ancien de l'administration système et du développement logiciel.

### Exemples de dotfiles classiques

On trouve par exemple :

* `.bashrc` : configuration du shell Bash.
* `.zshrc` : configuration du shell Zsh.
* `.gitconfig` : configuration de Git.
* `.vimrc` : configuration de Vim.
* `.env` : variables d'environnement (même si ce n'est pas strictement un "dotfile" historique).

---

## Dans l'IA agentique

Avec les agents IA (par exemple des assistants de développement ou des agents autonomes), les dotfiles servent souvent à définir :

* les règles que l'agent doit suivre ;
* les outils qu'il peut utiliser ;
* les commandes autorisées ;
* le style de code ;
* les tests à exécuter automatiquement ;
* les permissions (lecture, écriture, réseau, etc.).

Par exemple, un projet peut contenir :

```text
.agent/
    config.yml
.instructions.md
.tools.json
```

ou

```text
.cursor/
.claude/
.copilot/
```

Ces dossiers jouent un rôle similaire aux dotfiles traditionnels.

---

## Exemple concret

Imaginons un dépôt contenant :

```text
.
├── .git
├── .cursor
│   └── rules.md
├── .claude
│   └── settings.json
├── .env
└── src/
```

Le fichier `rules.md` pourrait contenir :

```text
- Toujours écrire les tests.
- Ne jamais modifier le dossier docs/.
- Utiliser Python 3.12.
- Respecter Black pour le formatage.
```

Ainsi, lorsque l'agent travaille sur le projet, il lit ces instructions avant d'agir.

---

## Pourquoi c'est important ?

Les dotfiles permettent de rendre un agent :

* **cohérent** : il suit toujours les mêmes règles ;
* **reproductible** : plusieurs développeurs obtiennent le même comportement ;
* **personnalisé** : chaque projet peut définir ses propres conventions ;
* **plus sûr** : on peut limiter les actions autorisées de l'agent.

---

## Est-ce un standard ?

Pas vraiment. Chaque outil possède son propre mécanisme. Par exemple :

* OpenAI Codex/CLI peut utiliser des fichiers d'instructions spécifiques au projet.
* Anthropic Claude Code utilise notamment un fichier `CLAUDE.md`.
* Anysphere Cursor utilise des règles dans le dossier `.cursor`.
* GitHub Copilot peut s'appuyer sur des instructions de dépôt et des fichiers de configuration.

L'idée est la même dans tous les cas : **décrire le comportement attendu de l'agent directement dans le projet**, afin que celui-ci puisse travailler de manière adaptée au contexte.

En résumé, dans l'IA agentique, les "dotfiles" désignent souvent l'ensemble des fichiers de configuration et d'instructions qu'un agent lit automatiquement pour comprendre **comment il doit se comporter** lorsqu'il intervient sur un projet.
