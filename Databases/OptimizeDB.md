Voici les actions concrètes à entreprendre pour optimiser les requêtes sur une base relationnelle (MySQL, PostgreSQL, Oracle).<br> 
Elles sont valables dans 90 % des cas, quel que soit le moteur.

## 1. Analyser avant d’optimiser (indispensable)
👉 Ne jamais optimiser à l’aveugle<br>
Utiliser **EXPLAIN** / **EXPLAIN ANALYZE**<br>
Identifier :
- scans de tables complets (Seq Scan, Full Table Scan)
- jointures coûteuses
- estimations fausses (rows estimées vs réelles)

Vérifier le temps réel d’exécution, pas seulement le plan<br>
📌 PostgreSQL : EXPLAIN (ANALYZE, BUFFERS)<br>
📌 MySQL : EXPLAIN ANALYZE<br>
📌 Oracle : EXPLAIN PLAN + DBMS_XPLAN.DISPLAY

## 2. Créer les bons index (le levier n°1)
**A. Indexer les colonnes critiques**<br>
Indexer en priorité :
- WHERE
- JOIN
- ORDER BY
- GROUP BY
```sql
CREATE INDEX idx_user_email ON users(email);
```

**B. Index composites (très souvent oubliés)**<br>
Respecter l’ordre des colonnes
```sql
WHERE status = 'ACTIVE' AND created_at > ?
-- index (status, created_at)
```
⚠️ Un index (a, b) ne sert pas pour une requête filtrant uniquement sur b.

**C. Éviter la sur-indexation**<br>
- Trop d’index = écritures plus lentes
- Supprimer les index inutilisés

## 3. Écrire des requêtes efficaces
**A. Éviter** ```SELECT *```<br>
Lire uniquement les colonnes nécessaires
```sql
SELECT id, name FROM users;
```

**B. Attention aux fonctions dans le WHERE**<br>
❌
```sql
WHERE LOWER(email) = 'test@mail.com'
```

✅
```sql
WHERE email = 'test@mail.com'
```
(ou index fonctionnel)

**C. Éviter les sous-requêtes inutiles**<br>
Remplacer par des JOIN quand possible.

**D. Limiter les résultats**
```sql 
LIMIT 50 OFFSET 0;
```

## 4. Optimiser les jointures
- Toujours joindre sur des colonnes indexées
- Même type de données des deux côtés (INT vs VARCHAR = ⚠️)
- Éviter les jointures sur des expressions

## 5. Réduire le volume de données traité
- Ajouter des filtres le plus tôt possible
- Utiliser des tables de référence (lookup tables)
- Archiver les données historiques

## 6. Adapter la structure des tables
**A. Choisir les bons types**<br>
- ```INT``` plutôt que ```BIGINT``` si possible
- ```VARCHAR(50)``` plutôt que ```TEXT```
- Éviter ```NULL``` si inutile

**B. Normalisation vs dénormalisation**<br>
- Normaliser pour la cohérence
- Dénormaliser quand la performance l’exige (avec contrôle)

## 7. Mettre à jour les statistiques
Indispensable pour un bon plan d’exécution.

PostgreSQL :
```sql
ANALYZE;
VACUUM ANALYZE;
```

MySQL :
```sql
ANALYZE TABLE table_name;
```

Oracle :
```sql
DBMS_STATS.GATHER_TABLE_STATS;
```

## 8. Utiliser le cache intelligemment
- Cache applicatif (Redis, Memcached)
- Pool de connexions
- Requêtes préparées (prepared statements)

## 9. Surveiller en production
- Requêtes lentes (slow query log)
- Temps CPU / I/O
- Verrous et contentions

📌 Activer le slow query log est souvent révélateur.