# Les hooks dans Claude Code (explication simple)

Les **hooks** sont des points d'accroche qui permettent d'exécuter automatiquement un script ou une commande à certains moments du fonctionnement de Claude Code.

On peut les voir comme des **"événements"** :

> "Quand telle action se produit, exécute automatiquement ce script."

## Pourquoi utiliser des hooks ?

Ils servent à automatiser des tâches sans demander à Claude de les lancer manuellement à chaque fois.

Exemples :

- Vérifier la qualité du code après une modification.
- Lancer les tests automatiquement.
- Formater le code avec Prettier ou Black.
- Envoyer une notification.
- Bloquer certaines actions dangereuses.
- Journaliser les actions réalisées par Claude.

## Comment ça fonctionne ?

Schéma simplifié :

```text
Claude Code
     │
     ▼
Événement déclenché
     │
     ▼
Hook exécuté
     │
     ▼
Script personnalisé
```

## Exemple concret

Imaginons que Claude modifie un fichier.

Sans hook :

```text
Claude modifie le fichier
→ Rien d'autre ne se passe
```

Avec hook :

```text
Claude modifie le fichier
→ Hook déclenché
→ npm test
→ Résultat affiché
```

## Les types de hooks les plus courants

### PreToolUse

Exécuté juste avant qu'un outil soit utilisé.

Exemple :

```text
Claude veut modifier un fichier
→ Hook vérifie que le fichier est autorisé
→ Modification acceptée ou refusée
```

### PostToolUse

Exécuté juste après l'utilisation d'un outil.

Exemple :

```text
Claude modifie un fichier
→ Hook lance les tests
```

### Notification

Exécuté lorsqu'une notification est envoyée.

Exemple :

```text
Fin d'une tâche
→ Envoi d'un message Slack
```

### Stop

Exécuté lorsque Claude a terminé son travail.

Exemple :

```text
Claude termine
→ Génération d'un rapport
```

## Exemple de configuration mentale

Imagine que tu développes une application :

```text
Claude modifie du code
        │
        ▼
PostToolUse
        │
        ▼
npm run lint
        │
        ▼
npm test
        │
        ▼
Succès ou erreur
```

Ainsi, chaque modification est automatiquement vérifiée.

## À retenir

Les hooks sont simplement :

```text
SI un événement se produit
ALORS exécuter une action automatique
```

Autrement dit :

```text
Événement → Hook → Script
```

Ils permettent d'ajouter de l'automatisation, du contrôle qualité, de la sécurité et des intégrations autour de Claude Code sans modifier le comportement principal de Claude.
