Dans un environnement bancaire, un développeur full-stack gagne à maîtriser les concepts de bases de données (BDD) bien au-delà des simples opérations CRUD. Les exigences de sécurité, de traçabilité, de performance et de conformité sont souvent beaucoup plus élevées que dans d'autres secteurs.

Voici les principaux concepts à connaître.

## 1. Modélisation des données

Avant même d'écrire une requête SQL, il faut comprendre la conception des bases de données.

* Modèle relationnel
* Entité / Association (ERD)
* Normalisation (1NF, 2NF, 3NF, BCNF)
* Dénormalisation (pour les performances)
* Clés primaires
* Clés étrangères
* Contraintes d'intégrité
* Cardinalités
* Index métier

Exemple bancaire :

* Client
* Compte
* Carte
* Contrat
* Virement
* Transaction

Toutes ces entités sont liées par des contraintes fortes.

---

## 2. SQL avancé

Au-delà du SQL basique.

À maîtriser :

* JOIN (INNER, LEFT, RIGHT, FULL)
* Sous-requêtes
* CTE (`WITH`)
* Fonctions de fenêtre (`ROW_NUMBER`, `RANK`, `LAG`, `LEAD`)
* Agrégations complexes
* GROUP BY avancé
* HAVING
* CASE WHEN
* UNION / UNION ALL
* EXISTS
* MERGE (Oracle / SQL Server)

Exemple :

Trouver la dernière transaction de chaque compte.

Les fonctions de fenêtre sont souvent utilisées.

---

## 3. Transactions

En banque, c'est probablement le concept le plus important.

Exemple :

```
Débiter 500€
Créditer 500€
```

Les deux opérations doivent réussir ou échouer ensemble.

Concepts :

* BEGIN
* COMMIT
* ROLLBACK
* SAVEPOINT

---

## 4. Les propriétés ACID

Fondamental.

### Atomicité

Tout ou rien.

### Cohérence

La base reste valide.

### Isolation

Deux utilisateurs ne se perturbent pas.

### Durabilité

Une transaction validée ne disparaît jamais.

Les banques reposent largement sur ces garanties.

---

## 5. Les niveaux d'isolation

À connaître :

* Read Uncommitted
* Read Committed
* Repeatable Read
* Serializable

Il faut aussi comprendre les anomalies associées :

* Dirty Read
* Non-repeatable Read
* Phantom Read

---

## 6. Verrouillage (Locking)

Deux conseillers peuvent modifier le même compte.

Comment éviter les conflits ?

Concepts :

* Shared Lock
* Exclusive Lock
* Row Lock
* Table Lock
* Deadlock
* Timeout

---

## 7. Concurrence

Très utilisée.

Optimistic Lock

```
version = 4
```

Lors de la sauvegarde :

```
UPDATE ...
WHERE version = 4
```

Si la version a changé :

```
OptimisticLockException
```

Pessimistic Lock

```
SELECT ...
FOR UPDATE
```

Très fréquent pour les opérations financières.

---

## 8. Index

Les index sont indispensables.

À connaître :

* B-tree
* Bitmap (Oracle)
* Hash
* Composite Index
* Covering Index

Savoir répondre à :

* Pourquoi une requête est lente ?
* Quel index créer ?
* Quand supprimer un index ?

---

## 9. Plans d'exécution

Lire un :

```
EXPLAIN
EXPLAIN ANALYZE
```

Comprendre :

* Scan
* Index Scan
* Sequential Scan
* Nested Loop
* Hash Join
* Merge Join

C'est une compétence très appréciée.

---

## 10. Optimisation SQL

Savoir éviter :

```
SELECT *
```

Préférer :

```
SELECT id, amount
```

Comprendre :

* Coût des JOIN
* Utilisation des index
* Pagination
* Batch
* Bulk Insert
* Bulk Update

---

## 11. Procédures stockées

Encore très présentes dans les banques.

À connaître :

* Stored Procedure
* Function
* Trigger
* Package (Oracle)

Exemple :

Calcul automatique des intérêts.

---

## 12. Vues

Types :

* View
* Materialized View

Les banques utilisent souvent des vues pour :

* masquer certaines colonnes ;
* simplifier les requêtes complexes ;
* appliquer des règles d'accès.

---

## 13. Sécurité des données

Très importante.

Comprendre :

* Chiffrement au repos
* Chiffrement en transit
* Hash
* Salt
* Masquage des données
* Tokenisation
* Contrôle d'accès
* Gestion des rôles
* Principe du moindre privilège

---

## 14. Audit

Indispensable.

Questions auxquelles répondre :

* Qui a modifié une donnée ?
* Quand ?
* Depuis quelle application ?
* Quelle était l'ancienne valeur ?

Techniques :

* Tables d'audit
* Triggers
* Historisation
* Change Data Capture (CDC)

---

## 15. Historisation

Souvent obligatoire.

Exemple :

```
Compte

Etat actuel
```

Mais aussi :

```
Compte_History
```

Chaque modification est conservée.

---

## 16. Réplication

Types :

* Master / Replica
* Réplication synchrone
* Réplication asynchrone

Objectifs :

* Haute disponibilité
* Reprise après incident
* Lecture répartie

---

## 17. Sauvegarde et restauration

Connaître :

* Backup complet
* Backup incrémental
* Point-in-Time Recovery (PITR)
* Journal des transactions

---

## 18. Partitionnement

Pour les très grosses tables.

Exemple :

```
TRANSACTIONS
```

Découpée par :

* année
* mois
* agence
* pays

Permet de traiter des milliards de lignes plus efficacement.

---

## 19. Types de bases de données

Un développeur bancaire doit connaître les grandes familles :

### Relationnelles

* PostgreSQL
* Oracle
* SQL Server
* DB2

### NoSQL

* MongoDB
* Cassandra
* Redis

Les données critiques (comptes, paiements, transactions) restent très majoritairement stockées dans des bases relationnelles grâce à leurs garanties ACID.

---

## 20. Intégrité référentielle

Exemple :

Impossible de supprimer un client possédant encore des comptes.

Concepts :

* CASCADE
* RESTRICT
* SET NULL

---

## 21. Performance

À analyser :

* Temps d'exécution
* CPU
* I/O
* Mémoire
* Cache
* Connexions

Savoir utiliser :

* Pool de connexions
* Pagination
* Batch processing

---

## 22. Connaissances spécifiques au secteur bancaire

En plus des compétences BDD générales, il est utile de comprendre :

* Double écriture comptable (chaque opération a une contrepartie).
* Idempotence des traitements (rejouer une opération ne doit pas produire un double débit).
* Réconciliation des données entre systèmes.
* Gestion des dates de valeur et des dates comptables.
* Traçabilité complète des opérations (audit).
* Tolérance zéro sur la perte de données lors des traitements critiques.
* Gestion des montants avec des types décimaux (`DECIMAL`/`NUMERIC`) plutôt que des flottants pour éviter les erreurs d'arrondi.

## Les compétences les plus valorisées en entretien

Si vous visez une banque ou un grand groupe financier, les sujets suivants reviennent très souvent :

1. SQL avancé (JOIN, CTE, fonctions de fenêtre).
2. Transactions et propriétés ACID.
3. Niveaux d'isolation et gestion de la concurrence.
4. Index et optimisation des requêtes.
5. Plans d'exécution (`EXPLAIN`).
6. Modélisation de bases relationnelles.
7. Verrouillage optimiste et pessimiste.
8. Procédures stockées et triggers (selon les technologies utilisées).
9. Audit, historisation et sécurité des données.
10. Réplication, sauvegarde et haute disponibilité.

Pour un développeur full-stack en milieu bancaire, la combinaison de ces compétences avec une bonne maîtrise d'un framework backend (Spring Boot, .NET, Node.js, etc.), des API REST, des mécanismes d'authentification (OAuth2, OpenID Connect, JWT) et des pratiques DevOps (Docker, Kubernetes, CI/CD) constitue un socle très recherché.
