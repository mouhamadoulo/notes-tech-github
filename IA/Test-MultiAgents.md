Petit point important sur Air avant les prompts : l'unité de travail y est la *tâche*, chacune déléguée à un agent (Claude, Codex, Gemini, Junie) et exécutée en parallèle dans un environnement Local, Git Worktree ou Docker. Il n'y a donc pas de « sous-agents » déclarés dans un fichier comme dans Claude Code : le découpage dont on parlait se traduit en **N tâches parallèles**, et les skills en **fichiers de conventions versionnés** que chaque tâche lit.

Pour voir la visualisation multi-agents s'animer, il faut donc lancer 3–4 tâches d'un coup, sur des périmètres disjoints, en mode **Git Worktree** (sinon elles s'écrasent).

## Tâche 0 — à lancer seule d'abord (mode Local, Plan puis exécution)

> Contexte : projet de migration d'applications bancaires vers Spring Boot + Angular. Tu prépares le socle de travail pour plusieurs agents qui travailleront ensuite en parallèle.
>
> Crée à la racine du dépôt :
> 1. `AGENTS.md` : description du projet, stack cible (Java 21 / Spring Boot / architecture hexagonale, Angular standalone + signals), et la règle que tout agent doit lire les fichiers de `docs/conventions/` avant d'écrire du code.
> 2. `docs/conventions/backend.md`, `frontend.md`, `api-design.md`, `securite-donnees-sensibles.md`, `tests.md` — chacun avec les règles concrètes et un exemple de code court. Pour la sécurité, couvre le masquage des données porteur, l'interdiction de logger IBAN/PAN, et la gestion des secrets.
> 3. `docs/glossaire-metier.md` : squelette du langage ubiquitaire (compte, mouvement, écriture, plafond, mandat, tiers), colonnes FR / EN / classe Java cible.
> 4. `docs/patterns-migration.md` : tableau vide avec les colonnes « pattern legacy | équivalent cible | remarques », plus 3 exemples pour amorcer.
>
> Ne génère aucun code applicatif. Termine par un récapitulatif des fichiers créés.

## Puis 4 tâches lancées simultanément (Git Worktree, branche dédiée chacune)

Assigne-les à des agents différents — c'est là que la vue multi-agents devient lisible.

**Tâche A — Analyse legacy**
> Analyse le module `<chemin/du/module>` de l'application legacy. Produis `docs/analyse/<module>-spec.md` : écrans et navigation, règles métier identifiées (une ligne par règle, testable), dépendances externes, points d'ambiguïté à arbitrer avec le métier. N'écris aucun code. Respecte `docs/glossaire-metier.md` et complète-le si tu croises des termes absents.

**Tâche B — Domaine et API**
> À partir de `docs/analyse/<module>-spec.md`, écris le domaine métier (entités, value objects, ports) et le contrat `openapi/<module>.yaml`. Aucune dépendance framework dans le package domaine. Applique `docs/conventions/backend.md` et `api-design.md`. Ajoute les tests unitaires des règles métier.

**Tâche C — Front Angular**
> À partir de `openapi/<module>.yaml`, génère les modèles TypeScript, les services HTTP et les composants standalone du module. Applique `docs/conventions/frontend.md`. Aucune modification de fichier backend.

**Tâche D — Revue transverse**
> Relis le code présent sur la branche `<branche>` sous l'angle sécurité et conformité bancaire : fuites de données sensibles dans les logs et les messages d'erreur, validation des entrées, contrôle d'accès, idempotence des opérations de mouvement. Produis `docs/revue/<module>-securite.md` classé par criticité. Ne corrige rien, propose seulement.

## Pour bien voir la parallélisation

B dépend de A, donc pour la démo : lance A, et pendant qu'elle tourne, lance B/C/D sur un module déjà analysé ou sur un module bouchon. Sinon tu regarderas trois tâches en attente. C et D sont les plus sûres à faire tourner en même temps — elles ne touchent pas les mêmes fichiers.

Si tu me dis quel module ou quelle techno legacy tu veux utiliser comme cobaye, je peux te réécrire les quatre prompts avec les vrais chemins et le vrai périmètre.
