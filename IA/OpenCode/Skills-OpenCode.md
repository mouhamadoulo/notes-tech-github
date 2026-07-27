Vu ton profil (Java/Spring Boot, Angular, Docker, Oracle Cloud, IA pour générer du code), je privilégierais une configuration **orientée qualité du code** plutôt qu'une configuration avec des dizaines de MCP. Beaucoup de développeurs expérimentés constatent qu'un petit nombre de skills bien conçus apporte plus qu'une longue liste de MCP qui consomment du contexte. ([Reddit][1])

Je partirais sur la stack suivante.

# 1. Skills indispensables ⭐⭐⭐⭐⭐

Les **Skills** sont la chose qui améliore le plus la qualité du code. Ils chargent des workflows uniquement lorsqu'ils sont nécessaires, ce qui évite de gaspiller la fenêtre de contexte. ([OpenCode Guide][2])

Je recommande :

| Skill               | Pourquoi                                                    |
| ------------------- | ----------------------------------------------------------- |
| Grill Me            | Force l'IA à challenger le design avant de coder            |
| Handoff             | Résume automatiquement une session pour reprendre plus tard |
| Understand Anything | Comprend rapidement une grosse codebase                     |
| Obra Superpowers    | Architecture + TDD + refactoring                            |
| Firecrawl           | Documentation web à jour                                    |
| Skill Optimizer     | Apprend de tes workflows                                    |
| Caveman             | Réduit énormément les tokens inutiles                       |
| Bug Diagnosis       | Recherche systématique des bugs                             |
| Domain Modeling     | Très utile pour Spring Boot                                 |
| TDD Workflow        | Génère d'abord les tests                                    |

---

# 2. MCP indispensables ⭐⭐⭐⭐⭐

Je n'en installerais pas plus de 8-10.

## GitHub MCP

Indispensable.

Permet :

* PR
* Issues
* Reviews
* Commits
* Branches

---

## Context7 MCP ⭐⭐⭐⭐⭐

Pour moi le meilleur.

Il va chercher la documentation officielle de :

* Spring Boot
* Angular
* Java
* Docker
* PostgreSQL
* React
* Kubernetes

au lieu d'utiliser les connaissances internes du modèle.

Très gros gain de qualité.

---

## Playwright MCP

Pour générer automatiquement :

* tests E2E
* captures d'écran
* validation UI

---

## Filesystem MCP

Très utile.

Le modèle peut :

* modifier plusieurs fichiers
* rechercher
* refactoriser

---

## PostgreSQL MCP

Pour :

* exécuter des requêtes
* analyser le schéma
* générer des migrations

---

## Docker MCP

Très pratique pour :

* inspecter les conteneurs
* lire les logs
* redémarrer
* lancer des commandes

---

## Git MCP

Très utile pour :

* analyser l'historique
* expliquer un commit
* créer une branche

---

## Browser MCP

Navigation Web complète.

Très utile pour :

* Swagger
* Admin
* Angular

---

# 3. Plugins OpenCode

Je mettrais :

### Oh My OpenCode Slim ⭐⭐⭐⭐⭐

Probablement le meilleur plugin aujourd'hui.

Il ajoute notamment :

* sous-agents
* outils AST/LSP
* meilleure orchestration
* gestion de tâches en arrière-plan. ([Composio][3])

---

### History Search

Très pratique.

Recherche dans les anciennes conversations.

---

### Multiplexer

Pour avoir plusieurs sessions IA simultanées.

Très utile sur les gros projets.

---

# 4. Agents spécialisés

Au lieu d'un seul agent, j'utiliserais plusieurs agents.

Exemple :

```
Architect

↓

Backend Engineer

↓

Frontend Engineer

↓

Reviewer

↓

Performance Engineer

↓

Security Engineer

↓

Test Engineer
```

Chaque agent est spécialisé.

---

# 5. Skills que je créerais moi-même

Là tu peux vraiment faire la différence.

Par exemple :

## springboot-enterprise

Qui impose :

* architecture hexagonale
* DTO
* Mapper
* Validation
* Tests
* Logs
* Exceptions
* OpenAPI
* Actuator

---

## angular22-enterprise

Qui impose :

* Standalone
* Signals
* Feature folders
* PrimeNG
* RxJS
* Smart/Dumb components

---

## docker-best-practices

Qui impose :

* Multi-stage build
* Non-root user
* Healthcheck
* Optimisation image

---

## clean-architecture

Qui vérifie automatiquement :

* dépendances
* SOLID
* DDD
* DRY
* KISS

---

## review-pr

Avant chaque commit :

* analyse
* bugs
* sécurité
* performances
* dette technique

---

# 6. Les modèles

Pour ton projet Payment Messages :

| Usage         | Modèle            |
| ------------- | ----------------- |
| Architecture  | Claude Sonnet 4.5 |
| Backend Java  | GPT-5.5           |
| Angular       | GPT-5.5           |
| Refactoring   | Claude Sonnet 4.5 |
| Debug         | GPT-5.5           |
| SQL           | GPT-5.5           |
| Docker        | GPT-5.5           |
| Documentation | Claude Sonnet 4.5 |

---

# 7. Ma configuration idéale

```
OpenCode

├── GPT-5.5
├── Claude Sonnet 4.5
├── Context7 MCP
├── GitHub MCP
├── PostgreSQL MCP
├── Docker MCP
├── Filesystem MCP
├── Playwright MCP
├── Browser MCP
├── Oh My OpenCode Slim
├── History Search
├── Multiplexer
├── Grill Me Skill
├── Handoff Skill
├── Understand Anything
├── Domain Modeling
├── Bug Diagnosis
├── Spring Boot Enterprise (perso)
├── Angular Enterprise (perso)
├── Clean Architecture (perso)
└── Review PR (perso)
```

Pour ton projet **Payment Messages**, si ton objectif est d'avoir un assistant qui produit un code proche d'un développeur senior, je mettrais l'effort sur **des skills personnalisés** (Spring Boot Enterprise, Angular Enterprise, Clean Architecture) plutôt que d'ajouter beaucoup de MCP. Les skills permettent d'imposer des conventions de conception et de qualité de manière répétable, alors que les MCP servent surtout à connecter l'agent à des outils ou services externes. ([OpenCode Guide][2])

[1]: https://www.reddit.com/r/opencodeCLI/comments/1sypefe/pluginmcp_recommendations/?utm_source=chatgpt.com "Plugin/MCP Recommendations"
[2]: https://opencodeguide.com/en/opencode-skills/?utm_source=chatgpt.com "OpenCode Agent Skills Guide | OpenCode Guide"
[3]: https://composio.dev/content/best-opencode-plugins?utm_source=chatgpt.com "Best OpenCode Plugins to improve flow state coding in 2026 | Composio"
