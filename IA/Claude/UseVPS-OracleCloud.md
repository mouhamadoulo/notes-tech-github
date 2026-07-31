Un VPS gratuit ou payant sur **Oracle Cloud** peut servir de machine permanente pour Claude Code. L'idée est que Claude travaille sur le serveur 24h/24, tandis que tu t'y connectes depuis ton ordinateur ou ton téléphone.

Voici les usages les plus intéressants :

### Développement continu

Tu peux installer Claude Code sur le VPS et lui faire :

* développer une application web ;
* corriger des bugs ;
* refactoriser un projet ;
* écrire des tests ;
* gérer un dépôt Git.

Comme le VPS reste allumé, les longues tâches continuent même si ton ordinateur est éteint.

### Héberger tes projets

Le VPS peut faire tourner :

* un site web (Nginx + Docker) ;
* une API (FastAPI, Node.js, Go…) ;
* une base de données ;
* des applications en conteneurs avec Docker Compose.

Claude peut ensuite modifier le code et redéployer l'application directement sur le serveur.

### Automatiser des tâches

Par exemple :

* sauvegardes automatiques ;
* scripts Python quotidiens ;
* collecte de données ;
* génération de rapports ;
* surveillance de services.

Claude peut écrire et maintenir ces scripts.

### Administration système

Tu peux demander à Claude de :

* installer des logiciels ;
* configurer Docker ;
* mettre en place HTTPS avec Let's Encrypt ;
* configurer un pare-feu ;
* analyser les logs ;
* optimiser les performances.

### IA et agents

Le VPS peut héberger :

* des agents IA ;
* des bots Discord ou Telegram ;
* des workflows d'automatisation ;
* des serveurs MCP ;
* des API qui utilisent différents modèles d'IA.

### Travail à distance

En combinant :

* Oracle Cloud VPS,
* Claude Code,
* Remote Control,

tu peux lancer une tâche depuis ton PC et la suivre ensuite depuis ton téléphone, où que tu sois.

## Ce qu'il faut garder à l'esprit

Un VPS Oracle Cloud **n'exécute pas Claude lui-même**. Il exécute le client **Claude Code**, qui communique avec les serveurs d'Anthropic via Internet. Il faut donc :

* un compte Claude compatible avec Claude Code ;
* une connexion Internet sortante depuis le VPS.

## Une configuration populaire

Beaucoup de développeurs utilisent une architecture comme celle-ci :

```text
Téléphone
      │
      ▼
Remote Control
      │
      ▼
Oracle Cloud VPS (Ubuntu)
      ├── Claude Code
      ├── Git
      ├── Docker
      ├── Nginx
      ├── Tes projets
      └── Déploiement automatique
```

Avec cette configuration, tu peux demander par exemple :

* « Mets à jour mon application et déploie-la. »
* « Analyse les logs des dernières 24 h. »
* « Corrige les erreurs de mon API. »
* « Crée une nouvelle fonctionnalité et ouvre une pull request. »

Le VPS devient alors un environnement de développement et de déploiement accessible en permanence, tandis que tu pilotes Claude depuis ton ordinateur ou ton téléphone. C'est une solution pratique si tu veux lancer des tâches longues, disposer d'un environnement toujours disponible ou centraliser plusieurs projets sur une même machine.
