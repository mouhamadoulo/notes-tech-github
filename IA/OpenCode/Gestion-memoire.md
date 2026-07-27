Si ton objectif est de transformer **OpenCode** en véritable agent de développement qui **se souvient de tout entre les sessions**, voici ce que je te recommande.

## 🥇 1. opencode-memory (mon premier choix)

C'est aujourd'hui la solution la plus complète pour OpenCode.

Elle apporte notamment :

* ✅ mémoire persistante entre les sessions
* ✅ recherche sémantique (tu peux retrouver une décision sans utiliser les mêmes mots)
* ✅ mémorisation automatique des décisions, procédures et blocages
* ✅ mémoire par projet
* ✅ plugin qui injecte automatiquement le contexte pertinent avant chaque requête (pas besoin de demander "rappelle-toi...") ([GitLab][1])

Exemple :

```
Projet Payment Messages

Décision :
- utiliser PostgreSQL
- Angular 22 standalone
- Spring Boot 4
- IBM MQ

Deux semaines plus tard...

"Ajoute une nouvelle feature"

OpenCode récupère automatiquement ces informations.
```

C'est probablement la meilleure expérience actuellement.

---

## 🥈 2. Mem0 OpenCode Plugin

Très bonne alternative.

Le plugin fournit des outils natifs comme :

* add_memory
* search_memories
* update_memory
* delete_memory
* list_entities

L'avantage est qu'il n'a plus besoin d'un serveur MCP distant : tout est intégré au plugin OpenCode. ([New Releases][2])

---

## 🥉 3. Memories.sh

Très intéressant si tu travailles sur plusieurs machines.

Il permet de :

* synchroniser les mémoires
* synchroniser les skills
* synchroniser le fichier `opencode.json`
* synchroniser les instructions des agents

C'est un excellent choix si tu utilises :

* PC fixe
* portable
* VPS
* plusieurs environnements de développement. ([Memories][3])

---

## 4. opencode-mem

Plugin plus récent.

Il ajoute :

* base vectorielle locale
* apprentissage automatique des préférences
* déduplication intelligente
* historique complet
* interface Web

Il est orienté "long terme" avec une vraie base de connaissances locale. ([GitHub][4])

---

# Ce que j'installerais personnellement

Pour ton projet **Payment Messages**, je partirais sur :

```
OpenCode

├── opencode-memory
├── Serena MCP
├── Context7
├── Sequential Thinking
├── GitHub MCP
├── PostgreSQL MCP
├── Docker MCP
├── Playwright MCP
└── Filesystem MCP
```

avec `opencode-memory` comme mémoire principale.

---

# Encore mieux : une combinaison

Le meilleur résultat est souvent obtenu en combinant plusieurs niveaux de mémoire :

* **AGENTS.md** → règles permanentes du projet (architecture, conventions, style)
* **opencode-memory** → décisions et historique des sessions
* **Context7** → documentation à jour des frameworks
* **Git** → historique du code
* **README + docs/** → documentation fonctionnelle

Ainsi, OpenCode connaît :

* les conventions de ton projet,
* les décisions passées,
* l'état actuel du dépôt,
* et la documentation officielle des technologies.

Pour un projet de la taille de **Payment Messages**, cette combinaison offre une expérience très proche d'un développeur qui travaille sur le même codebase depuis des mois.

[1]: https://gitlab.com/lyle/opencode-memory/-/blob/master/README.md?utm_source=chatgpt.com "README.md · master · Lyle Kozloff / opencode-memory · GitLab"
[2]: https://newreleases.io/project/github/mem0ai/mem0/release/opencode-v0.2.0?utm_source=chatgpt.com "mem0ai/mem0 opencode-v0.2.0 on GitHub"
[3]: https://memories.sh/docs/integrations/opencode?utm_source=chatgpt.com "OpenCode | Memories"
[4]: https://github.com/tickernelz/opencode-mem?utm_source=chatgpt.com "GitHub - tickernelz/opencode-mem: OpenCode plugin that gives coding agents persistent memory using local vector database · GitHub"
