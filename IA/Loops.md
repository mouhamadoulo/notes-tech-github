# Tutoriel : Comprendre les Loops en Développement Agentique

Dans le développement logiciel classique, un programme suit généralement un flux simple :

```text
Entrée → Traitement → Sortie
```

Un système agentique fonctionne différemment. Il est **cyclique**. L'agent observe, réfléchit, agit, évalue les résultats puis recommence jusqu'à atteindre son objectif.

Ces boucles (loops) sont au cœur des agents modernes comme ceux construits avec des frameworks tels que :

* LangChain
* LangGraph
* AutoGen
* CrewAI

---

# Pourquoi les loops existent-elles ?

Un LLM seul est essentiellement une fonction :

```python
output = llm(prompt)
```

Mais un agent doit :

1. comprendre un objectif ;
2. décider quoi faire ;
3. utiliser des outils ;
4. observer les résultats ;
5. éventuellement corriger sa stratégie ;
6. recommencer.

Sans boucle, l'agent ne peut ni s'adapter ni raisonner de manière itérative.

---

# La boucle agentique fondamentale

```mermaid
flowchart LR
    G[Goal] --> T[Think]
    T --> A[Act]
    A --> O[Observe]
    O --> D{Goal atteint ?}
    D -- Non --> T
    D -- Oui --> F[Finish]
```

On appelle souvent cette architecture :

**Think → Act → Observe**

ou encore :

**Reason → Act → Reflect**

---

# Exemple concret : Agent de réservation de voyage

L'utilisateur dit :

> Trouve-moi un vol Paris-Tokyo en dessous de 900 €.

L'agent exécute :

### Itération 1

#### Think

```text
Je dois chercher des vols Paris-Tokyo.
```

#### Act

```python
search_flights(
    departure="Paris",
    destination="Tokyo"
)
```

#### Observe

```json
[
  {"price": 1240},
  {"price": 1180},
  {"price": 1050}
]
```

#### Réflexion

```text
Aucun vol sous 900€.
Je vais élargir la recherche.
```

---

### Itération 2

#### Act

```python
search_flights(
    departure="Paris",
    destination="Tokyo",
    dates_flexibles=True
)
```

#### Observe

```json
[
  {"price": 860}
]
```

#### Réflexion

```text
Objectif atteint.
```

L'agent s'arrête.

---

# Pseudo-code de cette boucle

```python
goal = "Trouver un vol < 900€"

while True:

    thought = think(goal)

    action = choose_action(thought)

    observation = execute(action)

    if goal_satisfied(observation):
        break
```

C'est la structure de quasiment tous les agents modernes.

---

# Loop ReAct (Reason + Act)

Le pattern le plus célèbre est ReAct :

```mermaid
flowchart LR
    Q[Question] --> R[Reason]
    R --> A[Action Tool]
    A --> O[Observation]
    O --> R
    R --> F[Final Answer]
```

Exemple :

Utilisateur :

> Quelle est la population de Lyon ?

### Tour 1

Reason :

```text
Je dois rechercher cette information.
```

Action :

```python
search("population Lyon")
```

Observation :

```text
Environ 530 000 habitants.
```

Reason :

```text
J'ai obtenu l'information.
```

Réponse finale :

```text
Lyon compte environ 530 000 habitants.
```

---

# Loop de planification (Plan-and-Execute)

Certains problèmes sont trop complexes pour être résolus en une seule boucle.

Exemple :

> Organise un lancement produit.

L'agent crée un plan.

```mermaid
flowchart TD
    G[Objectif]
    G --> P[Créer un plan]
    P --> T1[Tâche 1]
    P --> T2[Tâche 2]
    P --> T3[Tâche 3]
    T1 --> E1[Exécuter]
    T2 --> E2[Exécuter]
    T3 --> E3[Exécuter]
```

Plan :

1. Étude de marché
2. Définition du pricing
3. Création de la campagne marketing
4. Préparation du lancement

Pseudo-code :

```python
plan = planner(goal)

for task in plan:
    execute(task)
```

---

# Loop de réflexion (Reflection Loop)

Les agents avancés peuvent critiquer leurs propres réponses.

Exemple :

```mermaid
flowchart LR
    G[Question]
    G --> D[Draft]
    D --> C[Critique]
    C --> R[Révision]
    R --> V{Suffisant ?}
    V -- Non --> C
    V -- Oui --> F[Réponse]
```

Exemple :

Utilisateur :

> Écris une documentation API.

L'agent :

### Draft

Produit une première documentation.

### Critique

```text
Il manque les exemples de requêtes.
```

### Révision

Ajoute :

```http
POST /users
```

### Nouvelle critique

```text
Il manque les codes d'erreur.
```

### Révision

Ajoute :

```http
400 Bad Request
401 Unauthorized
500 Internal Server Error
```

Puis l'agent s'arrête.

---

# Loop Tool-Use

La plupart des agents passent leur temps dans cette boucle.

```mermaid
flowchart TD
    T[Think]
    T --> D{Besoin d'un outil ?}
    D -- Oui --> U[Utiliser outil]
    U --> O[Observer]
    O --> T
    D -- Non --> F[Répondre]
```

Exemple :

Question :

> Quelle est la météo à Tokyo et convertis-la en Fahrenheit ?

L'agent :

### Boucle 1

```python
weather("Tokyo")
```

Observation :

```text
22°C
```

### Boucle 2

```python
celsius_to_fahrenheit(22)
```

Observation :

```text
71.6°F
```

Réponse finale :

```text
22°C (71.6°F)
```

---

# Loop de correction d'erreur

Les agents robustes savent réessayer.

```mermaid
flowchart LR
    A[Action]
    A --> R[Résultat]
    R --> E{Erreur ?}
    E -- Oui --> C[Corriger]
    C --> A
    E -- Non --> F[Continuer]
```

Exemple :

```python
query_database()
```

Erreur :

```text
ConnectionTimeout
```

L'agent :

```python
sleep(2)
reconnect()
query_database()
```

Ce pattern est extrêmement fréquent dans les agents de production.

---

# Loop Multi-Agent

Plusieurs agents collaborent.

```mermaid
flowchart LR
    P[Planner]
    P --> R[Researcher]
    R --> W[Writer]
    W --> Q[Reviewer]
    Q --> P
```

Exemple :

### Planner

Décompose la tâche.

### Researcher

Cherche les informations.

### Writer

Rédige le document.

### Reviewer

Vérifie la qualité.

Si la qualité est insuffisante :

```text
Reviewer → Planner
```

Une nouvelle itération démarre.

---

# Exemple complet : Agent de génération de code

Objectif :

> Crée une API REST avec authentification JWT.

```mermaid
flowchart TD
    G[Objectif]
    G --> P[Plan]
    P --> C[Générer code]
    C --> T[Exécuter tests]
    T --> D{Tests OK ?}
    D -- Non --> F[Corriger code]
    F --> T
    D -- Oui --> R[Livrer]
```

Pseudo-code :

```python
goal = "API JWT"

plan = planner(goal)

while True:

    code = generate_code(plan)

    result = run_tests(code)

    if result.success:
        break

    code = fix(code, result.errors)
```

Cette boucle est exactement celle utilisée par de nombreux assistants de programmation agentiques.

---

# Comment reconnaître une vraie architecture agentique ?

Si votre système contient seulement :

```python
prompt -> llm -> réponse
```

ce n'est pas vraiment un agent.

Un système devient agentique lorsqu'il possède au moins une boucle de ce type :

```text
Objectif
↓
Réflexion
↓
Action
↓
Observation
↓
Décision
↓
Nouvelle itération éventuelle
```

En pratique, les agents modernes combinent souvent plusieurs loops imbriquées :

```mermaid
flowchart TD
    Goal[Objectif global]
    Goal --> Plan[Planification]

    Plan --> Task1[Tâche]
    Task1 --> Think[Think]
    Think --> Act[Act]
    Act --> Observe[Observe]
    Observe --> Reflect[Reflect]

    Reflect --> Done{Terminé ?}
    Done -- Non --> Think
    Done -- Oui --> Next[Prochaine tâche]
```

On peut voir le développement agentique comme un ensemble de **boucles de décision récursives** : l'agent pense, agit, apprend de ses observations et réitère jusqu'à satisfaction de l'objectif. C'est cette capacité de boucle et d'adaptation qui distingue fondamentalement un agent d'un simple appel à un LLM.
