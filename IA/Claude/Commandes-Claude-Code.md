# Guide essentiel des commandes Claude Code

> Résumé pratique des commandes les plus utiles de Claude Code (CLI / assistant de développement). Les commandes exactes peuvent varier selon la version et l'environnement.

## Démarrage

| Commande                | Description                                                        |
| ----------------------- | ------------------------------------------------------------------ |
| `claude`                | Lance l'interface interactive Claude Code dans le dossier courant. |
| `claude .`              | Ouvre le projet courant et analyse le code local.                  |
| `claude /chemin/projet` | Ouvre un projet spécifique.                                        |
| `claude --help`         | Affiche l'aide complète et les options disponibles.                |
| `claude --version`      | Affiche la version installée.                                      |

## Utilisation directe par prompt

| Commande                                  | Description                          |
| ----------------------------------------- | ------------------------------------ |
| `claude "explique ce fichier"`            | Envoie une demande directe à Claude. |
| `claude "corrige les erreurs TypeScript"` | Analyse et propose des corrections.  |
| `claude "écris les tests pour ce module"` | Génère des tests automatiquement.    |

## Contexte & fichiers

| Commande                                | Description                           |
| --------------------------------------- | ------------------------------------- |
| `claude @fichier.js`                    | Inclut un fichier dans la requête.    |
| `claude @src/app.ts "explique ce code"` | Analyse ciblée d'un fichier précis.   |
| `claude @README.md "améliore la doc"`   | Réécrit ou enrichit la documentation. |

## Git & revue de code

| Commande                               | Description                           |
| -------------------------------------- | ------------------------------------- |
| `claude "résume les changements git"`  | Résume les modifications en cours.    |
| `claude "review ma PR"`                | Fait une revue de code.               |
| `claude "génère un message de commit"` | Propose un message de commit clair.   |
| `claude "explique ce diff"`            | Décrit les changements d'un diff Git. |

## Refactorisation & debug

| Commande                             | Description                            |
| ------------------------------------ | -------------------------------------- |
| `claude "refactorise ce module"`     | Propose une structure plus propre.     |
| `claude "trouve le bug"`             | Analyse les causes possibles d'un bug. |
| `claude "optimise les performances"` | Suggère des améliorations.             |
| `claude "simplifie ce code"`         | Réduit la complexité du code.          |

## Tests & qualité

| Commande                                    | Description                                |
| ------------------------------------------- | ------------------------------------------ |
| `claude "écris des tests unitaires"`        | Génère des tests unitaires.                |
| `claude "augmente la couverture de tests"`  | Cible les zones non testées.               |
| `claude "trouve les problèmes de sécurité"` | Recherche des vulnérabilités potentielles. |
| `claude "améliore la lisibilité"`           | Rend le code plus maintenable.             |

## Options fréquentes

| Commande                  | Description                               |
| ------------------------- | ----------------------------------------- |
| `claude --model <nom>`    | Choisit un modèle spécifique.             |
| `claude --verbose`        | Affiche plus de détails d'exécution.      |
| `claude --json`           | Sortie structurée en JSON (si supportée). |
| `claude --no-interactive` | Exécution sans mode interactif.           |

## Bonnes pratiques

* Exécuter Claude Code à la racine du projet.
* Donner des prompts précis et orientés objectif.
* Inclure les fichiers importants avec `@fichier`.
* Vérifier les modifications proposées avant validation.
* Utiliser Git avant les changements importants.

## Exemple de workflow rapide

```bash
claude .
claude "analyse ce projet"
claude "trouve les bugs potentiels"
claude "écris les tests manquants"
claude "génère un commit message"
```

## Remarque

Les fonctionnalités exactes dépendent de la version de Claude Code, du système et des intégrations disponibles.
