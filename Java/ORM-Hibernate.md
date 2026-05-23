# ORM (Object-Relational Mapping)
Un ORM (Object-Relational Mapping) est un outil qui sert à faire le lien entre deux mondes très différents :
- le monde objet (ton code Java avec des classes, objets, héritage…)
- le monde relationnel (ta base de données avec des tables, lignes, colonnes…)

# 🧠 1. <ins>L’idée simple</ins>
Au lieu d’écrire du SQL partout, un ORM te permet de manipuler la base comme si tu manipulais des objets Java.

👉 Exemple sans ORM :
```sql 
SELECT * FROM users WHERE id = 1;
```

👉 Avec ORM :
```java 
User user = entityManager.find(User.class, 1);
```

# ⚙️ 2. <ins>À quoi ça sert concrètement ?</ins>
✅ **1. Éviter le SQL répétitif**<br>
Tu n’as plus besoin d’écrire des requêtes SQL simples (CRUD).

✅ **2. Mapper automatiquement**<br>
Une table devient une classe :
| Table SQL | Classe Java |
| --------- | ----------- |
| users     | User        |
| id        | id          |
| name      | name        |

✅ **3. Gérer les relations facilement**<br>
Relations SQL → objets Java :
- OneToMany
- ManyToOne
- ManyToMany

✅ **4. Portabilité**<br>
Tu changes de base (MySQL → PostgreSQL), ton code change peu.

# ☕ 3. <ins>Exemple avec Hibernate (ORM Java très connu)</ins>
Hibernate ORM est une implémentation populaire de l’ORM en Java.

## 🧱 Étape 1 : Une table SQL
```sql
CREATE TABLE users (
    id INT PRIMARY KEY,
    name VARCHAR(100),
    email VARCHAR(100)
);
```

## 🧩 Étape 2 : Classe Java associée
```java 
import jakarta.persistence.*;

@Entity
@Table(name = "users")
public class User {

    @Id
    private int id;

    private String name;
    private String email;

    // getters et setters
}
```
👉 Ici :
- ```@Entity``` → classe liée à une table
- ```@Table``` → nom de la table
- ```@Id``` → clé primaire

## 🔄 Étape 3 : Lire en base
```java 
EntityManager em = entityManagerFactory.createEntityManager();

User user = em.find(User.class, 1);

System.out.println(user.getName());
```
👉 Hibernate génère automatiquement le SQL derrière.

## 💾 Étape 4 : Sauvegarder
```java
EntityTransaction tx = em.getTransaction();
tx.begin();

User user = new User();
user.setId(1);
user.setName("Alice");
user.setEmail("alice@mail.com");

em.persist(user);

tx.commit();
```
👉 Hibernate fait un INSERT automatiquement.

## 🔗 5. Exemple avec relation
Tables :
- users
- orders

En Java :
```java
@Entity
public class Order {

    @Id
    private int id;

    @ManyToOne
    private User user;
}
```
👉 Hibernate gère les clés étrangères pour toi.

## ⚠️ 6. Les limites d’un ORM
Un ORM n’est pas magique :
- ❌ Peut générer du SQL inefficace
- ❌ Moins performant sur requêtes complexes
- ❌ Peut masquer ce qui se passe réellement en base

👉 Donc :<br>
➡️ On combine souvent ORM + SQL natif

# 🧾 Conclusion
Un ORM comme Hibernate ORM te permet de :
- manipuler la base comme des objets
- réduire le SQL manuel
- accélérer le développement
- garder un code plus propre

Mais il faut comprendre le SQL derrière, sinon tu risques des problèmes de performance.