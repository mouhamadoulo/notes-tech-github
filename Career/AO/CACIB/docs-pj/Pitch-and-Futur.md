# Pitch de présentation, questions d'entretien et perspectives

Support de préparation pour présenter **Payment Messages** à l'oral : ce qu'on raconte, dans
quel ordre, ce qu'on répond aux questions, et où va le projet ensuite.

> Règle générale pour l'oral : **ne jamais annoncer un chiffre qu'on n'a pas mesuré**. Ce
> document distingue explicitement ce qui est implémenté (vérifiable dans le code) de ce qui
> reste à mesurer (§ 3.9).

---

## 1. Le pitch

### 1.1 Version 30 secondes

> Payment Messages simule la chaîne de traitement des messages de paiement d'une banque : des
> applications de back-office déposent des messages sur une file **IBM MQ**, un consommateur
> **Spring Boot** les persiste en **PostgreSQL**, une **API REST** les expose et une interface
> **Angular** permet de les superviser et de les reprendre en cas d'échec.
>
> Ce qui m'a occupé, ce n'est pas le CRUD — c'est ce qui se passe quand ça se passe mal :
> qu'est-ce qu'on fait d'un message illisible, d'une base momentanément indisponible, d'un
> message rejoué trois fois. La règle que je me suis fixée : **zéro message perdu**, et tout
> message non traité doit rester retrouvable, en base ou en file de rebut.

### 1.2 Version 2 minutes

Structure en quatre temps : *le contexte* → *ce que j'ai construit* → *les décisions* → *ce que
j'en tire*.

**Le contexte.** Dans une banque, les flux de paiement transitent par un middleware de
messagerie — IBM MQ dans la plupart des établissements. Le message part d'une application de
back-office, arrive dans une file, est consommé, traité, tracé. Quand un message échoue, il faut
savoir *lequel*, *pourquoi*, et pouvoir le rejouer. C'est ce cycle-là que le projet reproduit de
bout en bout.

**Ce que j'ai construit.** Un monorepo avec deux builds indépendants :

- un backend **Java 21 / Spring Boot 4.1** : un `@JmsListener` sur la file d'entrée, une couche
  service, JPA/PostgreSQL avec un schéma piloté par **Flyway**, et une API REST versionnée
  `/api/v1` documentée par OpenAPI ;
- un frontend **Angular 22** en *standalone*, *zoneless*, entièrement à base de **signals** :
  tableau de bord de supervision, liste filtrable, détail d'un message, et un écran de
  simulation d'envoi ;
- l'ensemble conteneurisé — deux Dockerfiles multi-étapes, un `docker-compose` qui monte la pile
  complète (PostgreSQL, IBM MQ, backend, frontend) et une CI GitHub Actions à trois jobs.

**Les décisions.** Trois qui me paraissent structurantes :

1. **La séparation erreur définitive / erreur transitoire à l'ingestion.** Un JSON illisible ne
   sera jamais lisible : on le persiste en `FAILED` avec son texte brut et on acquitte. Une base
   indisponible, elle, sera disponible plus tard : on laisse remonter l'exception, la session
   transactée fait un rollback et le broker redélivre. Deux erreurs, deux traitements opposés —
   les confondre, c'est soit perdre des messages, soit boucler indéfiniment sur un message
   toxique.
2. **La liste ne transporte jamais le payload.** Une projection dédiée renvoie la taille du
   payload en octets, pas son contenu. Sur une liste de 100 lignes à 500 octets, c'est 50 Ko
   qu'on ne transfère pas — et sur un vrai flux, des payloads bien plus gros.
3. **L'écran de simulation ne court-circuite rien.** Il publie sur la vraie file, le message
   repasse par le consommateur, la même validation, les mêmes rejets. Un modèle de payload
   volontairement invalide produit vraiment une ligne `FAILED`. Un écran qui écrirait directement
   en base testerait l'écran, pas la chaîne.

**Ce que j'en tire.** Le projet est passé par un audit performance/résilience complet — une
quarantaine de constats classés backend / base / API / frontend, tous traités. Ce travail-là m'a
appris plus que le développement initial : la plupart des corrections ne sont pas des
optimisations, ce sont des bugs qui n'avaient pas encore eu l'occasion de se produire.

### 1.3 Version démo guidée (5–7 minutes)

Ordre conseillé — on suit le flux, pas l'arborescence du code.

| # | Écran | Ce qu'on montre | La phrase qui porte |
|---|---|---|---|
| 1 | `/simulation` | Envoi d'un message valide, puis d'un modèle « JSON illisible » | « Rien n'est court-circuité : ça repart sur la vraie file. » |
| 2 | `/messages` | Le message valide apparaît en `RECEIVED`, l'invalide en `FAILED` avec son texte brut | « Le message rejeté n'est pas perdu — il est en base, consultable, avec la raison du rejet. » |
| 3 | `/messages` filtres | Recherche, type, statut ; les compteurs des pastilles suivent | « Les lignes et les compteurs viennent de la même requête : ils ne peuvent pas se contredire. » |
| 4 | Détail (tiroir) | Métadonnées + payload brut ; bouton *Rejouer* grisé sauf sur un `FAILED` | « Le graphe des transitions est dans l'enum côté serveur, le front le reflète. » |
| 5 | Rejeu | Rejouer le `FAILED` → repasse en `RECEIVED`, compteur de tentatives à 1 | « Au-delà du plafond, il bascule en `DEAD_LETTER` et part sur la file de rebut. » |
| 6 | `/dashboard` | KPI, volume horaire, alertes récentes | « Tout est calculé en SQL agrégé, pas sur un échantillon de 200 lignes. » |
| 7 | `/actuator/prometheus` | `payment_mq_messages_received/rejected/duplicates`, rollbacks | « Ce qui n'est pas mesuré n'est pas exploitable. » |

Si la démo doit être coupée à 3 minutes : garder **1 → 2 → 5**. C'est l'histoire complète en
trois écrans.

### 1.4 Les cinq phrases à ne pas rater

- « Deux familles d'erreurs à l'ingestion, deux traitements opposés. »
- « L'idempotence porte sur le `messageId` : une redélivrance ne crée pas de doublon. »
- « Le schéma appartient à Flyway, pas à Hibernate — `ddl-auto: validate`. »
- « La publication en DLQ se fait **après commit**, et seule une publication confirmée est
  horodatée ; un job de reprise republie le reste. »
- « L'authentification est hors périmètre — c'est une décision assumée, pas un oubli, et j'en
  connais les conséquences. »

---

## 2. Les décisions à défendre

Ce sont les points où un intervieweur technique creusera. Chacun se raconte en *problème →
choix → conséquence*.

### 2.1 Erreur définitive vs erreur transitoire

**Problème.** Un `@JmsListener` qui attrape toutes les exceptions et les journalise perd le
message. Un listener qui n'en attrape aucune boucle à l'infini sur un message illisible.

**Choix.** Deux chemins. *Permanent* (JSON illisible, validation en échec) → on écrit une ligne
`FAILED` avec le payload brut et le message d'erreur, puis on acquitte : le message est sorti de
la file mais reste consultable. *Transitoire* (base injoignable, timeout) → on laisse remonter,
la session transactée fait un rollback, le broker redélivre.

**Conséquence.** Le garde-fou anti-boucle est côté broker : `BOTHRESH(5)` + `BOQNAME` sur la file
d'entrée déplacent un message redélivré cinq fois vers une file de backout. Les rollbacks sont
comptés par un `ErrorHandler` dédié — une hausse de ce compteur est le premier signal d'une panne
d'infrastructure.

### 2.2 Idempotence de l'ingestion

**Problème.** Un `commit` JMS qui échoue après l'écriture en base donne une redélivrance et un
doublon.

**Choix.** Contrainte d'unicité sur `messageId` ; `saveMessage` intercepte la violation et
renvoie `false` au lieu de propager. Une redélivrance est donc un *non-événement*, compté dans
`payment.mq.messages.duplicates`.

**Conséquence, et c'est le point subtil.** `saveMessage` est en
`Propagation.NOT_SUPPORTED`. Deux raisons, toutes deux vécues : une transaction englobante en
lecture seule met Hibernate en `FlushMode.MANUAL` et l'insertion disparaît silencieusement ; et
une transaction englobante en écriture serait *empoisonnée* par la violation de contrainte — on
ne peut plus rien y faire après. Le mécanisme d'idempotence exige donc que l'insertion soit sa
propre unité de travail.

### 2.3 Publication en DLQ après commit

**Problème.** Publier sur la file de rebut à l'intérieur de la transaction : si celle-ci
échoue ensuite, le message est en DLQ mais pas en base. Publier après, sans transaction : si la
publication échoue, on croit l'avoir publié.

**Choix.** Le service émet un événement applicatif ; un `@TransactionalEventListener(AFTER_COMMIT)`
publie une fois la transaction validée. Le publisher renvoie `false` en cas d'échec, et **seule
une publication confirmée horodate `dlqPublishedAt`**. Un job planifié republie les lignes qui
n'ont pas cet horodatage.

**Conséquence.** L'état « en DLQ » n'est jamais une supposition : c'est une colonne, et ce qui
manque est rattrapable.

### 2.4 Le schéma appartient à Flyway

`ddl-auto: validate` : Hibernate contrôle l'accord entité/schéma mais ne le modifie plus. Toute
évolution d'entité exige une migration, sinon l'application refuse de démarrer. Trois migrations
à ce jour (schéma initial, passage en `timestamptz` + `@Version` + `payloadSize`, index sur le
type de message).

**Le point d'honnêteté à donner spontanément** : les tests unitaires tournent sur H2 avec Flyway
désactivé et un schéma généré par Hibernate. Une migration cassée passerait donc `mvn verify` en
local. C'est la CI qui la rattrape, parce que le runner a Docker et que les tests d'intégration
Testcontainers y tournent sur un vrai PostgreSQL.

### 2.5 Un seul prédicat pour la liste, ses compteurs et son curseur

**Problème.** Un tableau filtré côté serveur et des compteurs calculés côté client : les deux se
contredisent dès que les filtres divergent d'un caractère.

**Choix.** Un objet `MessageQuery` (statut, date, type, texte) normalise les critères une fois, et
une constante JPQL `FILTERS` est le **seul** prédicat utilisé par la liste, son `COUNT(*)`, la
pagination par curseur et les compteurs par statut. Un paramètre nul neutralise sa clause : seize
combinaisons de filtres, une seule requête.

**Conséquence.** Une pastille annonce exactement ce que donnerait un clic dessus. Détail de
correction qui vaut d'être cité : le groupe de recherche textuelle doit rester parenthésé — `AND`
lie plus fort que `OR`, sans parenthèses un filtre de statut était neutralisé par la recherche.

### 2.6 Angular : signals, zoneless, `OnPush` partout

Le service `MessageService` est l'unique source de données et porte tout l'état en signals. Deux
styles de récupération cohabitent **volontairement** : `httpResource` pour les lectures unitaires
(un descripteur de requête `undefined` laisse la ressource inerte — ça remplace les gardes
« déjà chargé »), et `Subject` + `switchMap` pour la liste, les stats et le dashboard, parce que
l'annulation y est indispensable.

**Le point à faire valoir** : les requêtes sont *annulées*, pas *mises en concurrence*. Le
`switchMap` garantit que c'est la dernière requête **demandée** qui gagne, pas la dernière
**arrivée**. Sans ça, un clic rapide sur deux pastilles peut afficher les résultats de la
première.

### 2.7 Conteneurisation

Backend : image multi-étapes avec **découpage du jar en couches**
(`jarmode=tools extract --layers`) — les dépendances, immuables, ne sont ni reconstruites ni
repoussées à chaque changement de code. Exécution sous un **uid numérique 1001**, groupe 0
(condition de `runAsNonRoot` sous Kubernetes, convention OpenShift). Sonde
`/actuator/health/readiness` qui inclut la base mais **pas** le broker : une coupure MQ suspend
l'ingestion, elle ne doit pas retirer l'instance du service alors que l'API répond toujours.

Frontend : build Angular puis nginx, avec pré-compression gzip niveau 9 au build (`gzip_static`)
et des en-têtes de sécurité `include`és dans **chaque** bloc `location` — `add_header` n'est pas
cumulatif en nginx, un `Cache-Control` sur les assets effacerait sinon toute la politique de
sécurité sur exactement les réponses qui portent le code.

---

## 3. Questions probables et réponses

### 3.1 « Pourquoi IBM MQ et pas Kafka / RabbitMQ ? »

Parce que le contexte est bancaire et que c'est ce qui est en place dans la plupart des
établissements — un projet de flux de paiement s'insère dans un middleware existant, il ne le
choisit pas. Sur le fond, les sémantiques diffèrent : MQ est une file de travail (un message,
un consommateur, acquittement transactionnel, DLQ native), Kafka est un journal ordonné
rejouable. Pour du traitement unitaire avec reprise et rebut, la file est le modèle naturel.
Cela dit, l'application est écrite contre **JMS** : passer sur ActiveMQ Artemis ou RabbitMQ (via
JMS) demanderait de changer la factory de connexion et la configuration, pas la logique métier.

### 3.2 « Comment garantissez-vous qu'aucun message n'est perdu ? »

Quatre mécanismes qui se complètent :

1. session JMS **transactée** — l'acquittement est lié à la transaction, un rollback redélivre ;
2. un message **définitivement** rejeté est persisté en `FAILED` avec son payload brut avant
   d'être acquitté : sorti de la file, mais pas disparu ;
3. `BOTHRESH`/`BOQNAME` côté broker : un message redélivré au-delà du seuil part en file de
   backout au lieu de tourner en boucle ;
4. la publication en DLQ est horodatée seulement si elle est confirmée, et un job de reprise
   republie ce qui manque.

Le critère d'acceptation que je me suis fixé : après un arrêt brutal de PostgreSQL en pleine
ingestion, tout message doit être retrouvable — en file, en backout, ou en base avec un statut
d'échec.

### 3.3 « Que se passe-t-il si la base tombe pendant l'ingestion ? »

L'insertion échoue, l'exception remonte, la session transactée fait un rollback, le broker
redélivre. La profondeur de file monte — c'est le signal à surveiller. Le compteur
`payment.mq.listener.rollbacks` grimpe. Côté API, la sonde `readiness` inclut la base : elle
passe `DOWN`, donc un orchestrateur cesse d'envoyer du trafic à l'instance. La sonde `liveness`,
elle, **n'inclut pas** la base : sinon une base momentanément absente ferait redémarrer en boucle
une application parfaitement saine.

### 3.4 « Comment ça tient à 10 millions de lignes ? »

Ce qui a été fait pour ça : index sur `(status, received_at)` et sur le type ; projection de
liste sans payload ; agrégats du dashboard en SQL (`GROUP BY`, `extract(hour …)`) et non en Java
sur un échantillon ; cache court (15 s) sur les statistiques, avec le même TTL côté HTTP
(`Cache-Control` + ETag) ; taille de page plafonnée à 200 ; **pagination par curseur** (keyset)
en plus de l'`OFFSET`, parce qu'`OFFSET 500000` fait payer les 500 000 lignes sautées.

Ce qui reste à faire, et je le dis : le **partitionnement** par mois sur `received_at`, et
l'activation de la rétention (elle existe, `RETENTION_ENABLED`, mais elle est **désactivée par
défaut** — une suppression est irréversible, ce n'est pas un défaut qu'on active à l'aveugle).

### 3.5 « Pourquoi pas d'authentification ? »

C'est une **contrainte de périmètre du projet**, pas un oubli. Il n'y a pas Spring Security au
classpath, pas de route de connexion, pas de rôle : chaque endpoint répond sans jeton — API,
actuator, Swagger UI compris.

J'en connais les conséquences et je les ai documentées : sur un environnement exposé, le
filtrage réseau et le terminateur TLS sont *de fait* la seule protection ; il faut restreindre
`/actuator`, `/swagger-ui` et `/v3/api-docs`, et couper l'écran de simulation
(`SIMULATION_ENABLED=false`) puisque n'importe quel visiteur pourrait sinon déposer des messages
sur la file d'entrée.

Ce que je ferais si le périmètre changeait : OIDC/OAuth2 avec l'IdP de l'entreprise
(`spring-boot-starter-oauth2-resource-server`), trois rôles — lecture, opération (rejeu,
changement de statut), administration (suppression, purge) — et une trace d'audit horodatée sur
chaque action, ce que le champ « motif » du changement de statut prépare déjà.

### 3.6 « Pourquoi Angular *zoneless* ? Qu'est-ce que ça change ? »

Sans `zone.js`, Angular ne détecte plus les changements en interceptant les APIs du navigateur :
c'est la mise à jour d'un **signal** qui déclenche le rendu. Concrètement, moins de cycles de
détection inutiles et un comportement prévisible. La contrepartie est une discipline : chaque
composant est en `ChangeDetectionStrategy.OnPush`, et tout état lu par un gabarit doit être un
signal — un champ mutable ordinaire lié par `ngModel` ne rafraîchit pas de façon fiable. C'est
une contrainte que j'ai fait respecter partout, y compris dans les boîtes de dialogue.

### 3.7 « Comment testez-vous ? »

Deux campagnes séparées, et la distinction est portée par le nom des classes :

- `*Test` → Surefire, base H2, aucun service externe — logique métier, transitions de statut,
  mappers, listener, service de rejeu par lots, contrôleur (MockMvc) ;
- `*IT` → Failsafe, **Testcontainers** — persistance et migrations Flyway sur un vrai
  PostgreSQL, plus un test de bout en bout sur IBM MQ (opt-in, ~1 Go d'image).

Une vingtaine de classes de test côté backend, huit `*.spec.ts` côté frontend (Vitest).

Deux détails dont je suis assez content : sans démon Docker, les `*IT` sont **ignorés**, pas en
échec — `verify` reste vert sur un poste sans Docker, et la CI, elle, a Docker donc ils
s'exécutent réellement. Et dans le test MQ, on ne consomme **jamais** depuis la file de requête
pour vérifier quoi que ce soit : ça volerait le message au listener sous test. On lit les
compteurs Micrometer à la place.

### 3.8 « Quelle a été la difficulté principale ? »

Spring Boot 4.1, honnêtement. Les réflexes de Boot 3 induisent en erreur : Jackson **3** est le
mapper autoconfiguré (`tools.jackson.databind`), un `ObjectMapper` Jackson 2 déclaré à la main
n'écrase rien ; des packages ont bougé ; et surtout, une propriété `spring.*` supprimée **ne
remonte aucune erreur**, elle ne se lie simplement à rien. J'avais une clé
`spring.jms.listener.acknowledge-mode` qui ne servait à rien et une concurrence JMS que je
croyais configurée. La leçon : avant de faire confiance à une clé, la vérifier dans le jar
(`javap` sur la classe de propriétés) plutôt que dans ses souvenirs.

Deuxième difficulté du même genre : Testcontainers 2 avec JUnit 6. `@ServiceConnection` lit les
champs annotés par réflexion **pendant la découverte des tests**, ce qui initialise la classe et
démarre le conteneur avant toute condition de saut — un poste sans Docker faisait donc échouer le
build au lieu de l'ignorer. Le passage par `@DynamicPropertySource` (recherche de méthode, qui
n'initialise rien) règle le problème.

### 3.9 « Quels gains avez-vous mesurés ? »

**Réponse honnête, à donner telle quelle** : la campagne de mesure avant/après n'a pas été
conduite. L'audit a été implémenté, le protocole et l'outillage sont en place —
`infra/load/MqInjector.java` pour le débit d'ingestion, `infra/load/k6-api.js` pour l'API avec
des seuils qui font échouer le tir (p95 < 500 ms sur la liste paginée, < 300 ms sur le curseur,
< 150 ms sur les stats) — mais les chiffres ne sont pas encore produits.

Ce que je peux affirmer sans mesure, parce que c'est structurel : la liste ne transfère plus le
payload, les agrégats du dashboard sont exacts au lieu d'être estimés sur 200 lignes, et la
pagination par curseur est insensible à la profondeur là où l'`OFFSET` ne l'est pas.

C'est d'ailleurs le premier élément de ma feuille de route (§ 4.1) : une optimisation non mesurée
est une conviction, pas un résultat.

### 3.10 « Pourquoi un monorepo ? »

Deux builds réellement indépendants (`backend/`, `frontend/`), mais un seul dépôt : le contrat
d'API change des deux côtés à la fois, et une pull request qui touche un endpoint et son appelant
se relit d'un bloc. La CI a trois jobs distincts, donc l'indépendance des builds est préservée.
Si les deux applications devaient être livrées à des rythmes différents par des équipes
différentes, la question se reposerait.

### 3.11 « Le front peut-il diverger du back ? »

Oui, et c'est le risque assumé du projet. Deux endroits doivent rester synchronisés à la main :
le graphe des transitions de statut (enum côté serveur / `status.config.ts` côté client) et les
couleurs de statut (variables CSS / même fichier). C'est documenté dans `CLAUDE.md`. La bonne
réponse à terme est la génération du client TypeScript depuis l'OpenAPI (§ 4.2) : le contrat
cesse alors d'être recopié.

### 3.12 « Et si le message arrive avec des champs inconnus ? »

Il est accepté : la désérialisation est tolérante aux propriétés inconnues. C'est un choix de
compatibilité ascendante — un producteur qui enrichit son message ne doit pas casser le
consommateur. Inversement, un champ **obligatoire manquant** est un rejet définitif
(`jakarta.validation`), avec persistance en `FAILED`.

### 3.13 « Comment déployez-vous ? »

`docker compose up -d --wait` monte la pile complète ; `--wait` s'appuie sur les healthchecks,
dont `/actuator/health/readiness` côté backend, donc il ne rend la main que quand Flyway a
migré, que le pool JDBC est ouvert et que le conteneur d'écoute JMS est démarré. La procédure
complète pour un VPS est dans `docs/Deployment.md`, avec un point qui vaut la peine d'être cité
en entretien : **l'image IBM MQ n'est publiée qu'en amd64, s390x et ppc64le — pas en arm64**, ce
qui exclut d'emblée les offres ARM (l'Always Free Ampere d'Oracle, les instances Graviton). C'est
le genre de contrainte qu'on découvre tard si on ne la vérifie pas.

### 3.14 Questions plus pièges

| Question | Réponse courte |
|---|---|
| « Pourquoi `OffsetDateTime` et pas `LocalDateTime` ? » | Un instant sans fuseau n'est pas un instant. Colonnes `timestamptz`, y compris le paramètre `receivedAfter` : l'appelant doit envoyer un décalage. |
| « Que fait `@Version` sur l'entité ? » | Verrouillage optimiste : deux changements de statut concurrents donnent un 409 au lieu d'un écrasement silencieux. |
| « Pourquoi le rejeu massif est asynchrone ? » | Une requête HTTP synchrone sur 100 000 lignes finit en timeout côté client alors que le traitement continue côté serveur. `202` + `taskId` interrogeable, exécution par lots bornés, une seule campagne en vol. |
| « Pourquoi pas de choix de file dans la simulation ? » | L'API ne prend pas de destination : offrir un sélecteur ouvrirait l'écriture sur toutes les destinations du queue manager, pour un besoin qui n'existe pas. La file est affichée, jamais choisie. |
| « Pourquoi l'ingestion n'invalide-t-elle pas le cache du dashboard ? » | Parce qu'un flux soutenu le viderait à chaque message : le cache ne servirait plus jamais. Il expire au bout de 15 secondes, et ce sont les écritures d'API qui l'évincent. |
| « `env` dans actuator ? » | Retiré volontairement : il exposait toute la configuration résolue, identifiants MQ compris. |
| « Pourquoi `provideAnimations` a-t-il disparu ? » | Toutes les animations sont en CSS ; `@angular/animations` n'est plus une dépendance. `provideAnimationsAsync` est déprécié depuis 20.2, retrait annoncé en v23 — le réintroduire serait de la dette immédiate. |
| « Combien de temps ça vous a pris ? » | Répondre en phases, pas en heures : socle fonctionnel, puis audit performance/résilience, puis conteneurisation et livraison. La deuxième phase a été la plus longue et la plus formatrice. |

### 3.15 Questions à leur poser

Un entretien technique se termine mieux quand on renvoie la balle :

- Quel middleware de messagerie est en place, et sur quelle version de MQ ?
- Comment sont traités aujourd'hui les messages en échec — reprise manuelle, outil dédié ?
- Quels sont les volumes réels : messages/jour, taille moyenne de payload, rétention exigée ?
- Le schéma de base est-il piloté par des migrations, ou par un DBA ?
- Quelle est la politique de test d'intégration : Testcontainers, environnement partagé, mocks ?

---

## 4. Axes d'amélioration

Classés par ce qu'ils rapportent, pas par difficulté.

### 4.1 Court terme — ce qui manque pour être crédible

| # | Action | Pourquoi maintenant |
|---|---|---|
| 1 | **Conduire la campagne de mesure** : injecter 500 k messages, tirer `k6-api.js`, publier les p95 avant/après index et curseur | C'est le seul chapitre de l'audit resté théorique (§ 3.9). L'outillage existe déjà. |
| 2 | **Tests de panne automatisés** : couper PostgreSQL en pleine ingestion, injecter un message toxique, redémarrer le broker | Le « zéro message perdu » est aujourd'hui un raisonnement, pas un test qui échoue si on le casse. |
| 3 | **Corriger la dérive documentaire** : `infra/load/README.md` mentionne encore un compte `admin/admin` qui n'existe plus | Une doc fausse coûte plus cher qu'une doc absente. |
| 4 | **Partitionner `payment_message` par mois** sur `received_at` + activer la rétention | La purge et les requêtes sur fenêtre récente deviennent triviales ; sans partition, la purge est une longue suite de `DELETE`. |

### 4.2 Moyen terme — solidité

- **Générer le client TypeScript depuis l'OpenAPI** (`openapi-generator`, tâche de build). Supprime
  la recopie manuelle du contrat, et le graphe des transitions cesse d'exister en deux exemplaires
  (§ 3.11).
- **Tracing distribué** (Micrometer Tracing + OpenTelemetry, export OTLP). Le `requestId` en MDC
  est un bon début, mais il ne relie pas l'appel HTTP à l'ingestion MQ qui l'a précédé. Une trace
  unique message → base → API → écran, c'est ce qui fait gagner une heure lors d'un incident.
- **Tableau de bord Grafana + règles d'alerte** sur les métriques déjà exposées : profondeur de
  file, taux de rejet, rollbacks, occupation du pool Hikari, latence p99 d'ingestion. Les
  métriques existent, elles ne sont regardées par personne.
- **Tests end-to-end Playwright en CI** sur les parcours du guide utilisateur — les captures du
  guide ont été produites par pilotage Playwright, l'infrastructure est là.
- **Journal d'audit** : qui a rejoué quoi, quand, avec quel motif. Le champ « motif » du
  changement de statut existe déjà mais n'est conservé que comme message d'erreur.
- **Politique de reprise plus fine** : aujourd'hui le plafond de rejeu est global
  (`MQ_MAX_RETRIES`). Un backoff exponentiel entre tentatives, ou un plafond par type de message,
  correspondrait mieux à la réalité (un référentiel indisponible se répare en heures, pas en
  millisecondes).

### 4.3 Long terme — passage à l'échelle

- **Scalabilité horizontale du consommateur.** Plusieurs instances consomment déjà la même file
  sans conflit (l'idempotence sur `messageId` couvre le cas). Ce qui manque : la coordination des
  tâches planifiées — le job de reprise DLQ et la purge de rétention s'exécuteraient en parallèle
  sur chaque instance. Verrou distribué (ShedLock) ou externalisation vers un ordonnanceur.
- **Séparation lecture / écriture** : réplique en lecture pour l'API et le dashboard, primaire
  réservé à l'ingestion. Utile seulement quand la mesure aura montré que la contention existe.
- **Kubernetes** si la plateforme cible l'est déjà. L'image est prête (uid non-root numérique,
  sondes distinctes) ; restent les manifestes et une vraie réflexion sur le stockage d'un
  queue manager en `StatefulSet`.
- **Archivage froid** des messages traités : S3/Object Storage après N mois, la base ne gardant
  que l'index. Combiné au partitionnement, c'est ce qui permet de garder plusieurs années
  d'historique sans faire grossir la base chaude.

---

## 5. Nouvelles fonctionnalités envisageables

Classées par valeur perçue par un exploitant du flux.

### 5.1 Fort impact

| Fonctionnalité | Description | Pourquoi ça compte |
|---|---|---|
| **Alerting sortant** | Notification (mail, Teams/Slack, webhook) au franchissement d'un seuil : N messages en échec sur 5 minutes, profondeur de file anormale, aucun message reçu depuis X | Aujourd'hui, il faut regarder l'écran pour savoir que ça va mal. Une supervision qui exige d'être regardée n'est pas une supervision. |
| **Sélection multiple et actions de masse** | Cocher des lignes dans la liste, rejouer / changer le statut / exporter la sélection | Le rejeu de masse actuel est « tous les `FAILED` », sans nuance. Un incident touche souvent un type ou une plage horaire. |
| **Export complet asynchrone** | L'export CSV ne couvre que la page affichée. Un export du résultat filtré entier, généré en tâche de fond et téléchargeable | Demande récurrente dès qu'un contrôle ou un rapprochement est en jeu. |
| **Recherche dans le payload** | Index plein texte PostgreSQL (`tsvector`) ou GIN sur JSONB : retrouver un message par IBAN, montant, identifiant de transaction | Aujourd'hui la recherche porte sur la référence, le `messageId` et le type — pas sur le contenu, qui est précisément ce qu'on cherche en production. |

### 5.2 Confort d'exploitation

- **Vues sauvegardées** : nommer et retrouver une combinaison de filtres (« échecs SEPA du jour »).
- **Comparaison de payloads** : diff entre le message d'origine et sa version rejouée.
- **Chronologie par message** : historique complet des transitions avec auteur, horodatage et
  motif — c'est le pendant visuel du journal d'audit (§ 4.2).
- **Statistiques temporelles enrichies** : comparaison J/J-1, J/J-7, détection d'anomalie simple
  sur le volume horaire.
- **Mode « incident »** : une vue plein écran centrée sur les échecs, rafraîchie en continu, à
  afficher pendant une astreinte.

### 5.3 Extension du domaine

- **Support multi-files** : consommer plusieurs files d'entrée (par flux, par filiale), avec la
  file d'origine comme dimension de filtrage et de statistique.
- **Règles de routage** : orienter un message vers une file de sortie selon son type ou son
  montant — l'application deviendrait un vrai maillon de la chaîne, pas seulement son observatoire.
- **Validation métier configurable** : au-delà de la validation syntaxique, des règles (plafond de
  montant, devise autorisée, format d'IBAN) éditables sans redéploiement.
- **Webhooks sortants** : notifier une application tierce lorsqu'un message change d'état, ce qui
  ouvre l'intégration dans une chaîne plus large.
- **Rapprochement** : import d'un fichier de référence et détection des écarts avec les messages
  reçus — le cas d'usage bancaire classique après une journée de production.

---

## 6. Ce que je ferais différemment

À sortir spontanément si on demande « qu'est-ce que vous regrettez ? ». C'est souvent la question
qui départage.

1. **J'aurais mesuré avant d'optimiser.** L'audit a été mené par lecture de code et raisonnement,
   ce qui est efficace pour trouver des bugs mais pas pour prioriser des optimisations. Certains
   correctifs sont indiscutables (idempotence, transactions) ; d'autres — le cache des stats, la
   pagination par curseur — auraient mérité un chiffre avant, pas seulement une justification.
2. **J'aurais fait tourner les tests unitaires sur PostgreSQL dès le départ.** Le choix de H2 rend
   les tests rapides, mais il interdit d'écrire du SQL PostgreSQL dans les repositories (H2 rejette
   `ON CONFLICT`) et laisse passer une migration cassée en local. Testcontainers avec un conteneur
   réutilisable aurait supprimé ce compromis.
3. **J'aurais généré le client d'API dès le premier endpoint**, plutôt que de recopier les types et
   de devoir maintenir deux exemplaires du graphe de statuts.
4. **J'aurais versionné un `docker-compose.prod.yaml` en même temps que celui de développement.**
   Le fichier de dev publie tous les ports et porte des mots de passe en clair ; c'est très bien
   en local, et c'est exactement ce qu'on déploie par erreur quand la variante de production
   n'existe pas.

---

## 7. Fiche mémo

**Chiffres à connaître**

| Élément | Valeur |
|---|---|
| Java / Spring Boot / Angular | 21 / 4.1 / 22 |
| Classes Java (main) | ~50 |
| Classes de test backend / specs front | ~20 / 8 |
| Migrations Flyway | 3 |
| Statuts | 4 (`RECEIVED`, `PROCESSED`, `FAILED`, `DEAD_LETTER`) |
| Concurrence JMS | 5 → 10 threads (`MQ_MIN/MAX_CONCURRENCY`) |
| Pool JDBC | 20 max / 5 idle |
| Plafond de page API | 200 |
| TTL des caches de stats | 15 s (serveur **et** `Cache-Control`) |
| Rafraîchissement auto du front | 30 s, suspendu si l'onglet n'est pas visible |
| Timeout HTTP client | 15 s, 2 reprises sur GET/HEAD uniquement |
| Jobs CI | 3 (backend `verify`, frontend build+test, compose smoke test) |

**Endpoints à citer**

`GET /api/v1/messages` (paginé, 4 filtres) · `GET /cursor` (keyset) · `GET /stats` ·
`GET /stats/dashboard` · `GET /types` · `GET /{id}` · `POST /{id}/retry` ·
`POST /batch/retry-failed` (202 + `taskId`) · `PUT /{id}/status` (422 sur transition interdite) ·
`DELETE /{id}` · `POST /api/v1/simulation/sends`

**Métriques à citer**

`payment.mq.messages.received|rejected|duplicates` · `payment.mq.listener.rollbacks` ·
`payment.dlq.publish.failures` · `payment.mq.processing` (timer, taggé par issue)

**Les trois pièges à ne pas laisser passer sous silence** — les évoquer avant qu'on les trouve
vaut mieux que l'inverse : pas d'authentification (périmètre assumé), tests unitaires sur H2 donc
migrations non couvertes en local, campagne de mesure non conduite.
