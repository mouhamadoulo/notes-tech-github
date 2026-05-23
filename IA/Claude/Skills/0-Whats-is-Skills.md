# <ins>SKILLS</ins>

## C'est quoi une skill Claude Code ?
Une skill est un fichier markdown qui donne des instructions spécialisées à Claude Code.<br>
> Quand vous tapez `/nom-de-la-skill`, Claude charge ces instructions et exécute la tâche avec un niveau d'expertise que vous n'obtiendriez pas avec un prompt générique.

## Comment installer une skill ?
Copiez le fichier .md dans le dossier ~/.claude/skills/ (global) ou .claude/skills/ (projet). <br>
La skill devient immédiatement disponible via la commande slash.

## Ces skills fonctionnent avec quel modèle ?
Toutes les skills fonctionnent avec Claude Opus 4.6 et Sonnet 4.6. <br>
Certaines skills complexes (comme autoplan ou codex) donnent de meilleurs résultats avec Opus.

## Je peux créer mes propres skills ?
Absolument. Une skill est un simple fichier markdown avec des instructions. <br>
Vous pouvez en créer autant que vous voulez pour vos workflows spécifiques.

## skill-creator
skill Anthropic utilisé pour : 
- créer de nouveaux skills
- améliorer les skills existants
- et mesurer la performance des skills