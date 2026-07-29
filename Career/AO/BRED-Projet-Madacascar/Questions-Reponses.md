Pour cette mission, le recruteur va probablement être un **manager technique Java/Angular**, pas un architecte Java qui va te poser des questions ultra-pointues sur le garbage collector. Il va surtout chercher à vérifier trois choses :

1. **Est-ce qu'il sait développer en Java/Angular ?**
2. **Est-ce qu'il a déjà fait de la migration d'applications ?**
3. **Est-ce qu'il va réellement apporter un gain grâce à l'IA ?**

Je te propose une préparation proche de ce qui est généralement posé dans les banques (Crédit Agricole, BPCE, BNP, Société Générale, etc.).

---

# 1. Questions sur ton expérience

## Présentez-moi votre parcours.

➡ Réponse en 2 minutes maximum.

* 8 ans d'expérience
* Fullstack Java / Angular
* Bouygues Telecom pendant plusieurs années
* applications critiques
* migration
* cloud AWS
* modernisation
* DevOps
* aujourd'hui IA agentique

Le but est de raconter une histoire.

---

## Quelle est votre plus grosse réalisation ?

Je prendrais la migration des 6 applications chez Bouygues.

Tu peux raconter :

> Nous avions plusieurs applications critiques déployées sur une infrastructure vieillissante. Nous avons d'abord réalisé une migration vers une nouvelle infrastructure locale, puis une migration vers AWS.
>
> J'ai participé au cadrage technique, aux développements Java, aux pipelines GitLab CI, à l'automatisation Ansible, aux tests, aux recettes et à la documentation.

Ça répond directement à leur projet.

---

# 2. Questions Java

## Pourquoi Spring Boot ?

Réponse :

* auto-configuration
* Spring Starter
* embedded Tomcat
* production rapide
* Actuator
* configuration simplifiée

---

## Pourquoi utiliser Spring plutôt que Java pur ?

Parce qu'il apporte :

* Injection de dépendances
* IoC
* AOP
* Transactions
* Sécurité
* REST
* Tests

---

## C'est quoi l'injection de dépendances ?

Tu peux répondre simplement.

> Au lieu qu'une classe crée elle-même ses dépendances avec new, c'est Spring qui les fournit automatiquement.
>
> Cela facilite les tests, réduit le couplage et améliore la maintenabilité.

---

## Constructor Injection ou @Autowired ?

Toujours répondre :

> Constructor Injection.

Pourquoi ?

* immutable
* testable
* dépendances obligatoires

---

## Différence @Component @Service @Repository ?

Réponse :

Ils sont tous des composants Spring.

Mais :

@Component

composant générique.

@Service

couche métier.

@Repository

accès aux données + traduction des exceptions SQL.

---

## JPA ou JDBC ?

Réponse :

JPA.

Pourquoi ?

* mapping objet
* moins de code
* repository
* pagination
* requêtes automatiques

Mais JDBC reste intéressant pour des traitements très performants.

---

## Expliquez une transaction.

Réponse :

Une transaction garantit que toutes les opérations réussissent ou qu'aucune n'est validée.

En Spring :

@Transactional

---

## Comment gérez-vous les exceptions ?

Réponse :

* exceptions métier
* exceptions techniques
* @ControllerAdvice
* ResponseEntityExceptionHandler

---

## Comment sécurisez-vous une API REST ?

Réponse.

JWT

OAuth2

HTTPS

validation

Spring Security

---

# 3. Questions Angular

## Pourquoi Angular ?

Réponse :

Parce que c'est un framework complet.

* routing

* DI

* RxJS

* formulaires

* architecture

* CLI

---

## Observable ou Promise ?

Observable.

Pourquoi ?

* plusieurs valeurs

* annulation

* operators RxJS

* streaming

---

## Subject ou BehaviorSubject ?

BehaviorSubject.

Pourquoi ?

Le dernier état est conservé.

Très utile pour partager l'état de l'application.

---

## Comment communiquez-vous entre composants ?

Réponse.

* Input

* Output

* Service partagé

* BehaviorSubject

---

## Lazy Loading ?

Pour ne charger un module que lorsqu'il est nécessaire.

---

## Comment optimiser Angular ?

OnPush

trackBy

lazy loading

standalone components

signals (Angular récent)

---

# 4. Questions Architecture

## Monolithe ou Microservices ?

Réponse nuancée.

Un monolithe convient bien aux petits projets.

Les microservices deviennent intéressants lorsqu'on souhaite :

* scaler

* déployer indépendamment

* découpler les domaines

Mais ils ajoutent aussi de la complexité.

---

## Pourquoi Docker ?

Réponse.

Même environnement partout.

Déploiement simplifié.

Isolation.

---

## CI/CD ?

Tu connais.

Tu peux expliquer :

Git

↓

Pipeline GitLab

↓

Build

↓

Tests

↓

Sonar

↓

Package

↓

Déploiement

---

# 5. Questions Migration

Très probables.

## Comment prépare-t-on une migration ?

Réponse.

Je procède généralement en plusieurs étapes :

* analyse de l'existant

* cartographie des dépendances

* estimation

* stratégie

* migration

* tests

* recette

* mise en production

* supervision

---

## Quels sont les risques ?

Réponse.

Perte de données

Régression

Performance

Dépendances

Configuration

Disponibilité

---

# 6. Questions IA

C'est ici que tu peux faire la différence.

---

## Comment utilisez-vous l'IA ?

Réponse.

Je l'utilise à plusieurs niveaux.

* génération de code

* revue de code

* debugging

* tests

* documentation

* refactoring

* analyse d'architecture

* génération SQL

* scripts DevOps

---

## Quelle différence entre Copilot et un agent IA ?

Très bonne question.

Réponse.

Copilot complète du code.

Un agent travaille sur une tâche complète.

Il :

analyse la codebase

comprend le contexte

propose un plan

modifie plusieurs fichiers

exécute des commandes

génère la documentation

produit un résumé

---

## Donnez-moi un cas concret.

Réponse.

> Sur un projet Spring Boot, je devais ajouter un nouveau champ métier.
>
> Au lieu de modifier chaque fichier manuellement, j'ai demandé à un agent d'analyser la codebase et d'appliquer l'évolution de bout en bout. Il a mis à jour les entités, DTO, mappers, services, contrôleurs, les composants Angular concernés et les tests. J'ai ensuite revu les modifications, exécuté les tests et validé le résultat avant le commit. Cela réduit le temps consacré aux tâches répétitives tout en gardant la maîtrise technique.

---

## L'IA remplace-t-elle le développeur ?

Réponse.

Non.

Elle accélère énormément.

Mais :

elle peut se tromper

elle ne connaît pas toujours le métier

elle ne décide pas de l'architecture

elle ne remplace pas les revues de code.

Le développeur reste responsable.

---

## Pourquoi avoir suivi cette formation ?

Réponse.

> Parce que je pense que les agents IA vont profondément transformer notre façon de développer. Je voulais aller au-delà de l'utilisation classique de ChatGPT et Copilot. J'ai donc suivi une formation de 70 heures dédiée aux agents IA pour le développement, qui couvrait notamment Claude Code, Cursor, les MCP, les workflows d'équipe, les agents autonomes avec mémoire et leur intégration dans les pipelines CI/CD. Mon objectif est de mettre ces pratiques au service des projets pour gagner en productivité tout en maintenant un haut niveau de qualité. 

---

# 7. Questions comportementales

## Vous êtes bloqué ?

Je cherche 30 minutes.

Je documente.

Je demande de l'aide avec suffisamment d'éléments.

---

## Vous êtes en désaccord avec un collègue ?

Je m'appuie sur des faits.

On compare.

On choisit la meilleure solution.

---

## Vous gérez plusieurs priorités ?

Jira

priorisation

communication

anticipation.

---

# Les 10 questions que je pense les plus probables

1. Présentez-vous.
2. Pourquoi cette mission ?
3. Pourquoi vous ?
4. Racontez une migration que vous avez réalisée.
5. Comment utilisez-vous l'IA ?
6. Quelle différence entre Copilot et un agent IA ?
7. Comment préparer une migration d'application ?
8. Pourquoi Spring Boot ?
9. Pourquoi Angular ?
10. Comment réagissez-vous lorsqu'une migration échoue en recette ?

Au vu de ton profil et de l'appel d'offres, je pense qu'il y a **80 % de chances** que l'entretien tourne autour de ces thèmes plutôt que sur des questions théoriques très pointues. Le manager cherchera surtout à vérifier que tu peux être rapidement opérationnel sur un projet de migration Java/Angular et que tu sauras exploiter les agents IA comme accélérateur de développement, ce qui est un critère explicitement demandé dans la mission.
