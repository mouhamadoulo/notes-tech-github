La différence tient surtout à la façon dont ton agent accède à GitHub et au niveau d'intégration.

### `gh` (GitHub CLI) avec un agent de code

`gh` est l'outil officiel en ligne de commande de GitHub.

Quand ton agent utilise `gh`, il agit comme un développeur dans un terminal. Il peut par exemple :

* cloner un dépôt ;
* créer une branche ;
* créer une Pull Request ;
* ouvrir ou commenter une issue ;
* lancer des workflows GitHub Actions ;
* appeler directement l'API GitHub.

**Avantages**

* Très rapide pour les opérations Git.
* Fonctionne avec n'importe quel script ou langage.
* Excellente intégration avec les commandes Git.
* Peut être utilisé même en dehors d'un assistant IA.

**Inconvénients**

* L'agent doit connaître les commandes `gh`.
* Les sorties sont du texte brut qu'il faut interpréter.
* Les appels complexes nécessitent parfois plusieurs commandes.

---

### GitHub MCP (Model Context Protocol)

GitHub MCP expose GitHub comme un ensemble d'outils structurés destinés aux modèles d'IA.

Au lieu de demander à l'agent :

> `gh pr list`

il peut appeler un outil comme :

> `list_pull_requests(repository="owner/repo")`

L'agent reçoit directement des données structurées (JSON) au lieu de parser du texte.

**Avantages**

* API spécialement conçue pour les LLM.
* Réponses structurées.
* Moins d'erreurs de parsing.
* Les permissions sont mieux contrôlées.
* Plus simple pour des tâches de haut niveau (recherche d'issues, lecture de PR, etc.).

**Inconvénients**

* Dépend de la disponibilité du serveur MCP.
* Toutes les fonctionnalités de `gh` ne sont pas forcément exposées.
* Moins flexible pour des opérations très spécifiques ou nouvelles.

---

## Exemple

Créer une PR.

### Avec `gh`

```bash
git push origin ma-branche

gh pr create \
  --title "Ajout de la fonctionnalité" \
  --body "..."
```

L'agent doit connaître cette syntaxe.

### Avec GitHub MCP

L'agent appelle simplement un outil :

```
create_pull_request(
    repo="owner/repo",
    head="ma-branche",
    base="main",
    title="Ajout de la fonctionnalité",
    body="..."
)
```

Il n'y a pas de commande shell à construire.

---

## Pour un agent de code

En pratique, les deux sont souvent complémentaires.

* Utilise **`gh`** lorsque l'agent travaille déjà dans un terminal, manipule Git localement, exécute des scripts ou a besoin de toute la puissance de la CLI.
* Utilise **GitHub MCP** lorsque tu veux que l'agent interagisse avec GitHub de manière fiable et structurée (issues, PR, revues de code, discussions, métadonnées) sans avoir à interpréter la sortie de commandes.

Une architecture courante consiste à utiliser :

* **Git** pour les opérations locales sur le dépôt ;
* **`gh`** pour les actions terminales qui n'ont pas d'équivalent pratique ;
* **GitHub MCP** pour toutes les interactions avec GitHub où des données structurées facilitent le travail de l'IA.

Pour un agent autonome (comme Codex, Claude Code ou un agent personnalisé), cette combinaison est souvent la plus efficace : **Git + GitHub MCP** pour la plupart des interactions avec GitHub, et **`gh`** comme solution de repli pour les fonctionnalités non couvertes par le serveur MCP.
