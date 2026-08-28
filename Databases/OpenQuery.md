**OPENQUERY** est une fonction SQL propre à **Microsoft SQL Server**. Elle permet d'exécuter une requête SQL directe (pass-through) sur une source de données externe via un **serveur lié** (*linked server*).

---

### Pourquoi l'utiliser ?

Au lieu que SQL Server rapatrie toutes les tables distantes pour faire les traitements en local, `OPENQUERY` envoie la requête à la base distante : c'est la base distante qui filtre, trie et exécute le travail avant de renvoyer uniquement le résultat final.

Cela offre deux avantages majeurs :

* **Performance** : réduction drastique du volume de données transférées sur le réseau.
* **Compatibilité** : possibilité d'utiliser la syntaxe native du SGBD distant (Oracle, PostgreSQL, MySQL, etc.).

---

### Syntaxe de base

```sql
SELECT * 
FROM OPENQUERY(NomDuServeurLie, 'SELECT id, nom FROM table_distante WHERE statut = ''actif''');

```

> **Attention à la syntaxe :** La requête passée en second argument doit être entourée de guillemets simples. Pour utiliser des chaînes de caractères à l'intérieur de cette requête, il faut doubler les guillemets (`''actif''`).

---

### Exemple concret (SQL Server vers Oracle)

Si vous avez un serveur lié nommé `ORACLE_PROD` et que vous souhaitez interroger la table `CLIENTS` en utilisant la fonction Oracle `SYSDATE` :

```sql
SELECT *
FROM OPENQUERY(ORACLE_PROD, 'SELECT client_id, nom, date_creation FROM clients WHERE date_creation >= SYSDATE - 30');

```

---

### Cas d'utilisation (INSERT, UPDATE, DELETE)

`OPENQUERY` ne sert pas qu'à lire des données, vous pouvez aussi l'utiliser pour modifier la base distante :

* **INSERT :**
```sql
INSERT INTO OPENQUERY(ORACLE_PROD, 'SELECT id, nom FROM clients')
VALUES (101, 'Dupont');

```


* **UPDATE :**
```sql
UPDATE OPENQUERY(ORACLE_PROD, 'SELECT statut FROM clients WHERE id = 101')
SET statut = 'inactif';

```



---

### Limites à garder en tête

* **Pas de variables dynamiques** : `OPENQUERY` n'accepte pas de variables SQL en paramètre direct dans sa chaîne de requête.
```sql
-- IMPOSSIBLE :
DECLARE @id INT = 10;
SELECT * FROM OPENQUERY(ORACLE_PROD, 'SELECT * FROM clients WHERE id = ' + @id); 

```


*(Pour contourner cela, il faut utiliser du SQL dynamique avec `EXEC(...)`).*
* **Permissions** : l'utilisateur configuré dans le serveur lié doit avoir les droits nécessaires sur la base distante.
