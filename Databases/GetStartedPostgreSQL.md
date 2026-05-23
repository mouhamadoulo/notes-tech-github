# Démarrer avec PostgreSQL et pgAdmin

Bonne question — une fois que tu as installé PostgreSQL et pgAdmin, voici les premières étapes pour créer ta base de données et commencer correctement.

---

## 1. Démarrer le serveur PostgreSQL
Assure-toi que le service PostgreSQL est bien lancé.

- **Windows** : via “Services” ou automatiquement au démarrage  
- **Mac/Linux** : généralement actif après installation  

---

## 2. Se connecter dans pgAdmin

Ouvre **pgAdmin** :

- Clique sur **"Add New Server"** (ou utilise un serveur déjà présent)
- Paramètres classiques :
  - **Host** : `localhost`
  - **Port** : `5432`
  - **Username** : `postgres`
  - **Password** : celui défini à l’installation

---

## 3. Créer une base de données

1. Dans le menu de gauche :
   - Clique droit sur **Databases**
2. → **Create → Database**
3. Donne un nom (ex : `ma_base`)
4. Clique sur **Save**

---

## 4. Créer une table (structure des données)

1. Va dans :
   - `Schemas → public → Tables`
2. Clique droit → **Create → Table**
3. Ajoute des colonnes, par exemple :
   - `id` → type `serial` (clé primaire)
   - `nom` → `text`
   - `age` → `integer`

---

## 5. Insérer des données

### Interface graphique
- Clique sur ta table → **"View/Edit Data"**

### Ou en SQL (recommandé)

```sql
INSERT INTO ma_table (nom, age)
VALUES ('Alice', 25);
```

## 6. Vérifier les données
```sql
SELECT * FROM ma_table;
```

## 7. Les bases à comprendre ensuite
Pour bien démarrer avec PostgreSQL :
- SQL de base : `SELECT`, `INSERT`, `UPDATE`, `DELETE`
- Les types de données (text, int, date, etc.)
- Les clés primaires et étrangères
- Les index (pour la performance)