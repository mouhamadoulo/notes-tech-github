# Le rejeu : carte « Tentatives de rejeu » du dashboard et bouton « Rejouer » des messages

Ce document décrit ce que montre la carte **Tentatives de rejeu** du dashboard, ce que fait
exactement le bouton **« Rejouer les N message(s) en échec »**, et son lien avec le bouton
**« Rejouer »** de la page Messages.

---

## 1. Ce que montre la carte « Tentatives de rejeu »

Fichier : `frontend/src/app/features/messages/pages/dashboard/dashboard.page.ts` (bloc
`@defer (on viewport)`, entête `Tentatives de rejeu · retryCount`).

### 1.1 Les quatre tuiles

Les tuiles affichent la **répartition de la colonne `retry_count` de la table
`payment_messages`**, c'est-à-dire le nombre de fois où chaque message a déjà été relancé :

| Tuile      | Signification                                                       |
|------------|---------------------------------------------------------------------|
| `aucune`   | messages dont `retryCount = 0` — jamais rejoués                      |
| `1 essai`  | messages dont `retryCount = 1`                                       |
| `2 essais` | messages dont `retryCount = 2`                                       |
| `3+`       | messages dont `retryCount >= 3` (tout ce qui dépasse est cumulé ici) |

Points importants :

- **Le comptage porte sur toute la table, tous statuts confondus** — pas seulement sur les
  messages en échec. Un message `PROCESSED` qui a été rejoué une fois reste dans `1 essai`.
  Requête : `SELECT p.retryCount, COUNT(p) FROM PaymentMessage p GROUP BY p.retryCount`
  (`PaymentMessageRepository.countByRetryCount()`), repliée en 0/1/2/3+ côté Java
  (`PaymentMessageService.retryBuckets()`).
- **Aucun filtre n'est appliqué** : ces compteurs sont globaux, ils ne suivent pas les filtres
  de la page Messages.
- La valeur vient de `GET /api/v1/messages/stats/dashboard` (champ `retries`), servie depuis
  le cache `dashboardStats` (TTL `STATS_CACHE_TTL`, 15 s par défaut). Le cache est vidé par
  les écritures de l'API (retry, changement de statut, suppression), pas par l'ingestion MQ.
- Le seuil qui fait basculer un message en `DEAD_LETTER` est `ibm.mq.max-retries`
  (`MQ_MAX_RETRIES`, **3** en dev) : la tuile `3+` correspond donc en pratique aux messages
  qui ont atteint ou dépassé le plafond.

### 1.2 Le bouton

```
Rejouer les {N} message(s) en échec
```

- `N` = compteur `FAILED` issu de `GET /api/v1/messages/stats` (le même que le KPI
  « Rejouables » en haut de page).
- Le bouton est **désactivé** si `N = 0` ou si un rejeu massif est déjà en cours.
- Pendant l'exécution il affiche `Rejeu en cours… X message(s)`, X étant l'avancement renvoyé
  par le serveur (le front ne compte rien lui-même).

---

## 2. Que se passe-t-il au clic sur « Rejouer les N message(s) en échec »

### 2.1 Flux exact

```
[Dashboard] clic
   → MessageService.batchRetryFailed()
   → POST /api/v1/messages/batch/retry-failed        (corps vide)
   ← 202 Accepted + { taskId, state: "RUNNING", processed: 0 }

   Côté serveur (BatchRetryService.start()) :
      - verrou single-flight (AtomicBoolean) : un seul rejeu massif en vol.
        Un second clic renvoie la tâche déjà en cours, il n'en démarre pas une deuxième.
      - la tâche est exécutée sur un exécuteur mono-thread dédié (batchRetryExecutor),
        la requête HTTP ne reste pas ouverte.

   Boucle de fond (BatchRetryService.run) :
      do {
         batch = PaymentMessageService.retryFailedBatch(app.batch-retry.batch-size)  // 500 par défaut
         processed += batch
      } while (batch == batchSize && processed < app.batch-retry.max-messages)       // 100 000 par défaut

   Chaque lot = une transaction :
      - SELECT des messages FAILED, page bornée à batchSize
      - applyRetry(message) sur chacun
      - saveAll
      - éviction des caches messageStats et dashboardStats

   [Front] MessageService.pollBatchRetry(taskId)
      → GET /api/v1/messages/batch/retry-failed/{taskId} toutes les 1,5 s
      tant que state == RUNNING ; à la fin : toast + refreshAll() (liste, stats, dashboard).
      Si truncated = true (plafond max-messages atteint), le toast invite à relancer.
```

### 2.2 Ce que fait `applyRetry` sur un message

`PaymentMessageService.applyRetry()` — c'est le cœur du rejeu :

```java
attempt = (retryCount == null ? 1 : retryCount + 1);
retryCount = attempt;
updatedAt  = now();

if (attempt > maxRetries) {          // maxRetries = ibm.mq.max-retries (3)
    status       = DEAD_LETTER;
    errorMessage = "Abandonné après 3 tentatives";
    requestDeadLetterPublication(message);   // publication différée après commit
} else {
    status       = RECEIVED;
    errorMessage = null;             // l'erreur précédente est effacée
}
```

Donc, pour chaque message `FAILED` :

- **soit** il repasse en `RECEIVED` avec `retryCount` incrémenté et `errorMessage` effacé ;
- **soit** (au-delà de 3 tentatives) il passe en `DEAD_LETTER`, et **là seulement** un message
  est réellement produit sur le broker (voir §3.2).

---

## 3. Est-ce que le message rejoué repasse par la queue ?

### 3.1 Cas normal (retour en `RECEIVED`) : **non**

Le rejeu est une **opération purement base de données**. Rien n'est republié sur
`PAYMENT.REQUEST.QUEUE`, le listener JMS n'est pas sollicité, le payload n'est pas
re-désérialisé ni re-validé. Le message reprend simplement l'état « en attente de
traitement ».

Conséquence à connaître : **aucune transition n'est automatique** dans ce projet. Un message
remis en `RECEIVED` y reste — il n'existe aucun consommateur applicatif qui reprendrait les
`RECEIVED` pour les passer en `PROCESSED` ou `FAILED`. Le passage suivant se fait à la main via
`PUT /api/v1/messages/{id}/status` (bouton « Changer statut » de l'UI). Le rejeu remet donc le
message *dans la file d'attente logique*, pas dans la file MQ.

> Pour qu'un message repasse réellement par IBM MQ (désérialisation, validation, insertion), il
> faut le **republier**, ce que fait l'onglet **Simulation** — un vrai producteur : il envoie le
> payload sur `ibm.mq.queue` et le message revient par `PaymentMessageListener`. Le rejeu, lui,
> ne fait que corriger l'état d'une ligne existante.

### 3.2 Cas du dépassement de seuil (`DEAD_LETTER`) : **oui, sur la DLQ**

Quand `attempt > ibm.mq.max-retries`, le service **n'envoie pas dans la transaction**. Il émet
un `DeadLetterRequestedEvent` ; `DeadLetterDispatcher` l'écoute en
`@TransactionalEventListener(AFTER_COMMIT)` et publie **après le commit** :

```
applyRetry → status = DEAD_LETTER + DeadLetterRequestedEvent
   → COMMIT
   → DeadLetterDispatcher (REQUIRES_NEW)
        → DeadLetterPublisher.publish() : jmsTemplate.send(ibm.mq.dlq-queue, payload brut)
             + propriétés JMS originalMessageId / reference / retryCount / errorMessage
        → succès  : dlqPublishedAt = now()
        → échec   : dlqPublishedAt reste null, compteur payment.dlq.publish.failures++
                    → DeadLetterRecoveryJob (planifié, ibm.mq.dlq-recovery.*) republie plus tard
```

Ordre voulu : publier avant le commit exposait à un message en DLQ sans ligne `DEAD_LETTER` en
base ; l'incohérence inverse (commit sans publication) est rattrapée par le job de reprise.

La destination est la **DLQ applicative** (`ibm.mq.dlq-queue`), pas la queue de requêtes : le
message n'est donc jamais réingéré par l'application.

---

## 4. Lien avec le bouton « Rejouer » de la page Messages

**Oui, c'est le même mécanisme** — deux portes d'entrée sur `applyRetry`.

| | Dashboard — « Rejouer les N message(s) en échec » | Messages (drawer / page détail) — « Rejouer » |
|---|---|---|
| Endpoint | `POST /api/v1/messages/batch/retry-failed` | `POST /api/v1/messages/{id}/retry` |
| Portée | **tous** les messages `FAILED` de la base | **un seul** message, celui ouvert |
| Réponse | `202` + `taskId`, traitement de fond par lots | `200` + le message à jour, synchrone |
| Suivi | polling `GET /batch/retry-failed/{taskId}` toutes les 1,5 s | aucun, la réponse contient le résultat |
| Garde | un seul rejeu massif en vol ; plafond `max-messages` | refus `400` si le statut n'est pas `FAILED` |
| Logique unitaire | `applyRetry` | `applyRetry` (identique) |
| Effet MQ | aucun, sauf bascule en `DEAD_LETTER` → DLQ | idem |
| Caches | `messageStats` + `dashboardStats` vidés à chaque lot | `messageStats` + `dashboardStats` vidés |

Côté frontend, les deux passent par `MessageService`
(`frontend/src/app/features/messages/services/message.service.ts`) :

- `retry(id)` → `POST /{id}/retry`, puis mise à jour locale du message, flash visuel sur la
  ligne, `afterWrite()` (rechargement des vues déjà chargées) ;
- `batchRetryFailed()` → `POST /batch/retry-failed` puis `pollBatchRetry(taskId)`, et
  `afterWrite()` à la fin.

Le bouton unitaire est désactivé hors statut `FAILED` (`canRetry()` dans
`message-drawer.component.ts`) ; le bouton massif ne cible de toute façon que les `FAILED`
(`repository.findAllByStatus(FAILED, …)`).

Différence pratique : rejouer 1 000 messages un par un depuis la liste produit exactement le
même état final que le rejeu massif — ce dernier évite seulement 1 000 allers-retours HTTP et
borne la charge base (lots de 500, une transaction par lot, jamais toute la table en mémoire).

---

## 5. Effet observable sur la carte après un rejeu

Après un rejeu massif de `N` messages `FAILED` (aucun n'atteignant le seuil) :

- le compteur `FAILED` tombe à 0, `RECEIVED` augmente de `N` (donut « Répartition par statut »,
  KPI « Rejouables » et « En attente ») ;
- dans les tuiles, `N` messages **glissent d'une tuile vers la droite** (`aucune` → `1 essai`,
  `1 essai` → `2 essais`, `2 essais` → `3+`) ;
- pour les messages dont la tentative dépasse `max-retries`, le statut devient `DEAD_LETTER` :
  ils apparaissent dans les « Alertes récentes » avec le niveau `CRITIQUE` et le message
  « Abandonné après 3 tentatives », et leur payload part sur la DLQ.

Les compteurs affichés peuvent accuser jusqu'à 15 s de retard hors rejeu (caches serveur
`messageStats`/`dashboardStats` + `Cache-Control`), mais un rejeu vide ces caches, donc le
`refreshAll()` de fin de tâche montre l'état réel.

---

## 6. Références de code

| Élément | Fichier |
|---|---|
| Carte + bouton | `frontend/src/app/features/messages/pages/dashboard/dashboard.page.ts` |
| Appels & polling | `frontend/src/app/features/messages/services/message.service.ts` |
| Bouton unitaire | `frontend/src/app/features/messages/components/message-drawer/message-drawer.component.ts`, `pages/message-detail/message-detail.page.ts` |
| Endpoints | `backend/…/controller/PaymentMessageController.java` |
| Orchestration massive | `backend/…/service/BatchRetryService.java` |
| `applyRetry`, `retry`, `retryFailedBatch`, `retryBuckets` | `backend/…/service/PaymentMessageService.java` |
| Agrégat `retryCount` | `backend/…/repository/PaymentMessageRepository.java` (`countByRetryCount`) |
| Publication DLQ | `backend/…/mq/DeadLetterDispatcher.java`, `mq/DeadLetterPublisher.java`, `mq/DeadLetterRecoveryJob.java` |
| Seuil | `ibm.mq.max-retries` (`MQ_MAX_RETRIES`), `app.batch-retry.batch-size` / `max-messages` |
