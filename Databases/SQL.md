# 1.SQL : c’est quoi exactement ?
SQL (Structured Query Language) est le langage standard pour :
- interroger des bases de données relationnelles
- modifier des données
- définir la structure des données
- gérer les droits et transactions

Il est utilisé par :
- PostgreSQL
- MySQL / MariaDB
- Oracle
- SQL Server
- DB2
- H2 (souvent en Java)

# 2.Les grandes familles de commandes SQL
🧱 **DDL – Data Definition Language**<br>
Définit la structure
```sql
CREATE TABLE users (...);
ALTER TABLE users ADD email VARCHAR(255);
DROP TABLE users;
```
👉 schéma, tables, colonnes, index, contraintes

✏️ **DML – Data Manipulation Language**<br>
Manipule les données
```sql
INSERT INTO users VALUES (...);
UPDATE users SET name = 'Paul';
DELETE FROM users WHERE id = 1;
```

🔍 **DQL – Data Query Language**<br>
Interrogation des données
```sql
SELECT * FROM users;
```

🔐 **DCL – Data Control Language**<br>
Gestion des droits
```sql
GRANT SELECT ON users TO app_user;
REVOKE SELECT ON users FROM app_user;
```

🔄 **TCL – Transaction Control Language**<br>
Transactions
```sql
BEGIN;
COMMIT;
ROLLBACK;
SAVEPOINT sp1;
```

# 3.Structure d’une requête SELECT
```sql 
SELECT colonne1, colonne2
FROM table
WHERE condition
GROUP BY colonne
HAVING condition
ORDER BY colonne
LIMIT 10 OFFSET 20;
```

**Ordre logique d’exécution (important)**
- 1.FROM
- 2.JOIN
- 3.WHERE
- 4.GROUP BY
- 5.HAVING
- 6.SELECT
- 7.ORDER BY
- 8.LIMIT

# 4.Types de données SQL (principaux)
| Type        | Description              |
| ----------- | ------------------------ |
| INT, BIGINT | Entiers                  |
| DECIMAL     | Montants précis          |
| FLOAT       | Approximation            |
| VARCHAR(n)  | Chaîne variable          |
| TEXT        | Long texte               |
| DATE        | Date                     |
| TIMESTAMP   | Date + heure             |
| BOOLEAN     | Vrai/Faux                |
| JSON        | Données semi-structurées |


# 5.Contraintes d'intégrité
```sql 
PRIMARY KEY
FOREIGN KEY
UNIQUE
NOT NULL
CHECK
```
👉 Garantissent la cohérence des données

# 6.Les index (très important)
```sql 
CREATE INDEX idx_user_email ON users(email);
```
- Accélèrent les SELECT
- Ralentissent INSERT / UPDATE
- Indispensables pour JOIN et WHERE

# 7.Les JOINTURES SQL (gros morceau)
🎯 **À quoi servent les jointures ?**<br>
```
Relier des données entre plusieurs tables via des clés (souvent PK ↔ FK)
```
**Tables d’exemple**<br>
```
users(id, name)
orders(id, user_id, amount)
```

🔹**INNER JOIN (jointure interne)**<br>
<div align="center">
	<img src="images/innerjoin.png" height="200" width="200" />
</div>

**Principe :**<br>
Retourne uniquement les lignes qui ont une correspondance dans les deux tables
```sql
SELECT u.name, o.amount
FROM users u
INNER JOIN orders o ON u.id = o.user_id;
```
👉 utilisateurs avec au moins une commande

🔹**LEFT JOIN (ou LEFT OUTER JOIN)**<br>
<div align="center">
	<img src="images/leftjoin.png" height="200" width="200" />
</div>

**Principe :**<br>
- Toutes les lignes de la table de gauche
- NULL si pas de correspondance à droite
```sql
SELECT u.name, o.amount
FROM users u
LEFT JOIN orders o ON u.id = o.user_id;
```
👉 utilisateurs avec ou sans commande

🔹**RIGHT JOIN**<br>
<div align="center">
	<img src="images/rightjoin.png" height="200" width="200" />
</div>
Inverse du LEFT JOIN (peu utilisé)
```sql
SELECT u.name, o.amount
FROM users u
RIGHT JOIN orders o ON u.id = o.user_id;
```

🔹**FULL OUTER JOIN**<br>
<div align="center">
	<img src="images/fullouterjoin.png" height="200" width="200" />
</div>
- Toutes les lignes des deux tables
- NULL quand pas de correspondance
```sql
SELECT u.name, o.amount
FROM users u
FULL OUTER JOIN orders o ON u.id = o.user_id;
```
⚠️ Non supporté par MySQL (simulation possible)

🔹**CROSS JOIN**<br>
<div align="center">
	<img src="images/crossjoin.png" height="450" width="450" />
</div>
Produit cartésien
```sql 
SELECT *
FROM users
CROSS JOIN orders;
```
👉 Très dangereux si mal utilisé

🔹**SELF JOIN**<br>
<div align="center">
	<img src="images/selfjoin.png" height="200" width="200" />
</div>

Une table jointe à elle-même
```sql 
SELECT e.name, m.name AS manager
FROM employee e
LEFT JOIN employee m ON e.manager_id = m.id;
```

# 8.JOIN vs WHERE (piège classique)
❌ Mauvais (transforme un LEFT JOIN en INNER JOIN)
```sql
SELECT *
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
WHERE o.amount > 100;
```

✅ Correct
```sql
SELECT *
FROM users u
LEFT JOIN orders o
  ON u.id = o.user_id AND o.amount > 100;
```

# 9.Jointures avec agrégations
```sql 
SELECT u.name, COUNT(o.id) AS nb_orders
FROM users u
LEFT JOIN orders o ON u.id = o.user_id
GROUP BY u.name;
```

# 10.Sous-requêtes vs JOIN
**Sous-requête**
```sql
SELECT name
FROM users
WHERE id IN (
  SELECT user_id FROM orders
);
```
**JOIN (souvent plus performant)**
```sql
SELECT DISTINCT u.name
FROM users u
JOIN orders o ON u.id = o.user_id;
```

# 11.Cas avancés utiles en pratique
**EXISTS (performant)**
```sql
SELECT *
FROM users u
WHERE EXISTS (
  SELECT 1 FROM orders o WHERE o.user_id = u.id
);
```
**JOIN sur plusieurs colonnes**
```sql
ON a.id = b.id AND a.type = b.type
```
**JOIN + conditions complexes**
Toujours distinguer :
- conditions de jointure → ON
- conditions de filtrage → WHERE

# 12.Résumé
| Concept     | À retenir                      |
| ----------- | ------------------------------ |
| SQL         | Langage des BDD relationnelles |
| JOIN        | Lien entre tables              |
| INNER       | Intersection                   |
| LEFT        | Tout à gauche                  |
| RIGHT       | Tout à droite                  |
| FULL        | Union                          |
| CROSS       | Produit                        |
| SELF        | Table ↔ table                  |
| ON vs WHERE | Fondamental                    |
