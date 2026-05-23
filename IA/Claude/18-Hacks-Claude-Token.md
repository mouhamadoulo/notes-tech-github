# 18 hacks pour ne pas épuiser rapidement ces tokens
Un token = plus petite unité de texte qu'un modèle IA lit et facture

> 1 token <> 1.3 mots en français

**La relecture exponentielle :** 
> A chaque nouveau message, Claude relit tout l'historique (les messages , les réponses , tout)<br>
> Chaque message est plus cher que le précédent parce qu'il paie pour tout l'historique

**L'overhead invisible :** 
> Claude recharge aussi à chaque fois d'autres choses avant même ta première question

|Ce qui est chargé   | ~Nbre tokens consommés |
|--------------------|------------------------|
|`CLAUDE.md`         |`~2000 tokens`          |
|`Serveurs MCP`      |`~2500 tokens`          |
|`System Prompt`     |`~4000 tokens`          |
|`Skills`            |`~1500 tokens`          |
|`Fichiers contexte` |`~5000 tokens`          |
|**TOTAL**           | `~15000 tokens`        |

Il y'a un phénomène qui s'appelle le **Lost in the Middle**<br>
> Le modèle fait très attention au `début` et à la `fin` de la conversation , Mais tout ce qui est au `milieu` il l'ignore un peu<br>
Au final Tu paies plus, mais tu reçois moins

# <ins>Basique</ins>

## Hack 1 : Démarre toujours des nouvelles sessions
Utilise la commande `/clear` entre les tâches qui n'ont rien avoir entre elles

## Hack 2 : Déconnecte tes MCP inutiles
Il faut déconnecter tes serveurs MCP qui ne sont pas forcément inutiles sur ta session<br>
Chaque serveur MCP connecté charge toutes ses définitions d'outils dans ton contexte à chaque message<br>
Même un serveur que t'utilises pas forcément il est là et il consomme<br>
et un seul serveur ça peut être quasiment `18000 tokens / message` et c'est totalement invisible pour toi<br>
Lance un `/mcp` au début de chaque session et déconnecte ceux qui ne te servent à rien<br>
> Et mieux encore si tu peux trouver un CLI pour le service dont tu as besoin en MCP utilise forcément le CLI<br>
> Par exemple : au lieu d'utiliser le **MCP Google Calendar** qui bouffe des tokens , utilise **Google Workspace CLI**<br>
> C'est plus rapide , c'est moins cher et le futur va dans cette direction

## Hack 3 : Regroupe tes prompts en un seul message
`3 messages séparés` coûtent 3x plus cher que `1 message combiné`

## Hack 4 : Utilise le Plan Mode
***Avec Plan Mode***
```
Claude cartographie -> Pose les bonnes questions -> Execute avec précision
```

***Sans Plan Mode***
```
Claude part dans la mauvaise direction -> Jette tout -> Recommence
```

Tu peux par exemple ajouter dans ton `CLAUDE.md` ceci :<br>
> Ne fais aucun changement tant que tu n'as pas 95% de confiance dans ce que tu dois construire.<br>
> Pose moi des questions de suivi jusqu'à atteindre ce niveau de confiance

## Hack 5 : Utilise les commandes "/context" , "/cost", "/usage"
`/context` : te montre exactement ce qui mange tes tokens

`/cost` :  quand j'utilise en mode API

`/usage` : quand on est en Claude subscription

## Hack 6 : Configure ta Status Line
Pour faire cela, il faut taper la commande : `/statusline`

## Hack 7 : Garde un oeil sur ta consommation globale
Pour cela , tu peux le voir dans : claude.ai > profil > settings > Usage

## Hack 8 : Sois malin avec ce que tu colles directement dans le terminal
Avant de balancer un document entier ou un fichier énorme, demande toi est-ce que Claude a vraiment besoin de tout lire

## Hack 9 : Regarde Claude travailler
Ne lance pas un Prompt en passant à un autre onglet à part si tu sais exactement ce qu'il va produire<br>
Surveille le surtout pour les tâches qui sont longues<br>
S'il tourne en rond ou va dans la mauvaise direcion ,arrête le tout de suite : `ESC` pour interrompre

# <ins>Intermédiaire</ins>

## Hack 10 : il faut absolument garder son CLAUDE.md hyper léger
Ce fichier Claude va le lire à chaque début de session, il va le prendre comme le Context System<br>
Garde le en dessous des 200 lignes, tu peux y mettre : 
- ta stack technique
- tes convetions de code
- tes commandes build 
- la règle des 95% de confiance mentionnée juste au dessus

Il faut vraiment y mettre uniquement l'esentiel<br>
Il faut traiter ton `CLAUDE.md` comme un index<br>
Il faut mettre les références des autres fichiers (`architecture.md`, `conventions.md`, /specs, /guides)

## Hack 11 : Soit chirurgical avec les références de fichiers
Ne dis pas : (référence vague)
```
voilà mon repo , trouve le bug 
```

Mais plutôt : (référence chirurgicale)
```
Vérifie la fonction verifyUser dans auth.ts ligne 42
```
 
> Tu dois guider Claude parce que Moins il explore , Moins il consomme

## Hack 12 : Compacte tes conversations une fois 60% du contexte atteint
Il faut compacter tes conversations lorsque tu atteins 60% de ton contexte <br>
il faut utiliser la commande : `/compact`

## Hack 13 : Le prompt cache expire en 5 minutes
Claude code utilise un prompt caching, ça évite de retraiter le contexte qui n'a pas changé <br>
Mais le cache actuellement a un timeout de 5 minutes

Si tu dois t'absenter quelques minutes fait un `/compact` d'abord <br>
-> il se fait un résumé de la conversation<br>
-> comme ça quand tu renvoies un message , tu paies pas plein tarif, tu repars avec une base hyper propre

## Hack 14 : Fais attention aux outputs des commandes qui sont lancées
En gros quand Claude il lance une commande shell, le résultat entier il rentre dans la fenêtre de contexte<br>
Si une commande renvoie 200 commits par exemple ou des pages de logs , tout ça c'est des tokens 

> **Solution :** contrôle les permissions dans `.claude/settings.json`<br>
> utilise la commande `/permission` et tu fais un deny sur la commande que tu veux pas qu'il utilise

Exemple :
```json 
{
 "deny": [
   "git log"
   "git diff --stat"
   "find / -name"
 ]
}
```

> Tu peux aussi utiliser des outils comme `RTK (Read Tool Kill)` qui te permette de réduire la consommation de tes tokens 

# <ins>Avancé</ins>

## Hack 15 : Choisis le bon modèle en fonction de la tâche que tu veux effectuer
**Sonnet 4**
- modèle par défaut
- Code, debug, features
- Meilleur rapport qualité/tokens

**Haiku**
- Sous-agents
- Formatage simple
- Sous-tâches
- il est rapide et pas cher

**Opus**
- Planification
- Architecture profonde
- Quand Sonnet ne suffit pas 
- il est puissant

## Hack 16 : Comprendre le coût des sub-agents
Les workflows avec des agents utilisent 7 à 10x plus de tokens qu'une session standard avec un seul agent<br>
> chaque sous-agent charge tout le contexte depuis le début

Utilise ça intelligemment<br>
> Délègue aux sous-agents les tâches ponctuelles surtout si tu veux que cette tâche utilise Haiku

## Hack 17 : Exploite les Peak Hours
Anthropic ils ont adapté la vitesse à laquelle se draine tes 5h de consommation<br>
Ils ont dont mis en place les `PEAK HOURS` et les `OFF-PEAK HOURS` -> car surement les serveurs n'arrivent pas à suivre

**PEAK HOURS     :** 8AM -> 2PM Eastern Time (14h à 20h heure française) Jours de semaine uniquement 
- Ton usage va forcément se consommer plus rapidement 

**OFF-PEAK HOURS :** Soirée et Week-end (Après 20h en semaine et tout le Week-end)
- Moins de monde = ta limite dure beaucoup plus longtemps

## Hack 18 : CLAUDE.md = Source de vérité
Ton CLAUDE.md il doit contenir des décisions stables :
- des règles d'architecture
- les résumés de progression

C'est la source de vérité qui va rendre chaque prompt beaucoup plus court

Tu peux y rajouter des règles de gestion<br>
Exemple : sub-agents toujours en Haiku
```
## Règles
- Sous-agents: toujours utiliser Haiku
- Tests: lancer avant chaque commit 
```
Tu gagnes du temps et en consommation tokens