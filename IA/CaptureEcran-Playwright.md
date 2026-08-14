Oui. Pour ton cas — Claude Code + frontend Angular + documentation Markdown — la solution la plus adaptée est probablement Playwright, soit via son MCP officiel, soit via son CLI conçu pour les agents de code.

Playwright MCP officiel⁠

Ce que Claude Code pourrait faire

Tu pourrais lui demander quelque chose comme :
```
Lance mon frontend, et en utiisant Playwright
parcours les principales fonctionnalités de l’application et prends des captures d’écran des écrans importants.
Enregistre-les dans docs/images/
et mets à jour la documentation Markdown correspondante avec les captures et une description de chaque écran.
Cette documentation enregistre le dans docs/user-guide/
```
Claude Code pourrait alors :

1. lancer ton application Angular ;
2. ouvrir le navigateur ;
3. naviguer vers les différentes pages ;
4. cliquer sur les boutons et remplir les formulaires ;
5. prendre des captures d’écran ;
6. générer des captures pleine page ;
7. sauvegarder les images dans ton projet ;
8. mettre à jour tes fichiers .md.

Playwright permet notamment de capturer la page visible, un élément précis ou la page complète avec fullPage. 

Exemple d’organisation pour ton projet

docs/
├── images/
│   ├── dashboard.png
│   ├── messages-list.png
│   ├── message-details.png
│   └── settings.png
│
├── getting-started.md
├── architecture/
│   └── frontend.md
└── user-guide/
    ├── dashboard.md
    └── message-management.md

Dans un fichier Markdown :

# Gestion des messages
L'écran de gestion des messages permet de consulter les messages de paiement
reçus depuis IBM MQ et enregistrés en base PostgreSQL.

## Fonctionnalités
- Consulter la liste des messages
- Rechercher un message
- Filtrer par statut
- Consulter le détail d'un message

Installation avec Claude Code

Tu peux connecter Playwright à Claude Code comme serveur MCP :

claude mcp add playwright -- npx @playwright/mcp@latest

Ou utiliser directement la configuration MCP :

{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp@latest"]
    }
  }
}

Playwright fournit également un CLI spécifiquement pensé pour les coding agents comme Claude Code, avec une approche plus légère en tokens. 

Ce que je te recommande pour ton projet

Pour ton application Angular + Spring Boot + IBM MQ + PostgreSQL, je mettrais en place un workflow comme celui-ci :

Claude Code
    │
    ├── Analyse le code Angular
    │
    ├── Lance le frontend
    │
    ├── Playwright
    │      ├── Ouvre le navigateur
    │      ├── Navigue dans l'application
    │      ├── Effectue les actions utilisateur
    │      └── Prend les captures
    │
    ├── Enregistre les images
    │      └── docs/images/
    │
    └── Met à jour
           ├── user-guide/*.md
           └── README.md

Je créerais même une commande Claude Code dédiée, par exemple :

/document-frontend

avec pour objectif :

Analyse le frontend Angular. Identifie les écrans et parcours utilisateurs importants. Utilise Playwright pour parcourir l’application, prends des captures d’écran des écrans principaux, enregistre-les dans docs/images/, puis crée ou mets à jour la documentation Markdown dans docs/user-guide/.

Pour ton besoin, Playwright MCP est donc le choix le plus naturel : il permet à Claude Code de combiner la connaissance de ton code source avec une vraie navigation dans ton application et la génération de captures utilisables dans ta documentation.
