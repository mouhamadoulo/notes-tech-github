# Prisma
<div align="center">
	<img src="../images/prisma.png" height="250" width="250" />
</div>

## 1. <ins>Qu’est-ce que Prisma ORM ?</ins>
Prisma est un ORM (Object Relational Mapper) pour Node.js et TypeScript qui permet d’interagir avec une base de données sans écrire directement de SQL.

Un ORM sert à mapper (faire correspondre) :
- les tables de la base de données
- avec des objets dans le code

Au lieu d’écrire :
```sql 
SELECT * FROM users WHERE id = 1;
```

Avec Prisma tu écris :
```TypeScript
const user = await prisma.user.findUnique({
  where: { id: 1 }
})
```
👉 Prisma transforme automatiquement cela en requête SQL pour la base.

Prisma fonctionne avec plusieurs bases comme :
- PostgreSQL
- MySQL
- SQLite
- MongoDB

## 2. <ins>Les 3 éléments principaux de Prisma</ins>
**1. Prisma Schema**<br>
C’est le fichier qui décrit la structure de ta base de données.

Exemple ```schema.prisma```
```prisma 
datasource db {
  provider = "postgresql"
  url      = env("DATABASE_URL")
}

generator client {
  provider = "prisma-client-js"
}

model User {
  id    Int     @id @default(autoincrement())
  email String  @unique
  name  String?
  posts Post[]
}

model Post {
  id        Int     @id @default(autoincrement())
  title     String
  content   String?
  published Boolean @default(false)
  authorId  Int
  author    User    @relation(fields: [authorId], references: [id])
}
```
Ici on décrit :
- table User
- table Post
- relation 1 utilisateur → plusieurs posts

**2. Prisma Client**<br>
Prisma génère automatiquement un client JS/TS typé pour interagir avec la base.
```bash
npx prisma generate
```

Puis dans ton code :
```TypeScript
import { PrismaClient } from '@prisma/client'

const prisma = new PrismaClient()
```

**3. Prisma Migrate**<br>
Permet de créer et versionner les migrations de base de données.
```bash
npx prisma migrate dev --name init
```
Cela :
- 1.crée les tables
- 2.applique les migrations
- 3.met à jour le client

## 3. <ins>Exemple complet CRUD avec Prisma</ins>
**Créer un utilisateur**
```TypeScript
const user = await prisma.user.create({
  data: {
    email: "alice@example.com",
    name: "Alice"
  }
})
```

**Lire les utilisateurs**
```TypeScript
const users = await prisma.user.findMany()
```

**Avec filtre :**
```TypeScript
const user = await prisma.user.findUnique({
  where: {
    email: "alice@example.com"
  }
})
```

**Modifier un utilisateur**
```TypeScript
await prisma.user.update({
  where: { id: 1 },
  data: { name: "Alice Martin" }
})
```

**Supprimer**
```TypeScript
await prisma.user.delete({
  where: { id: 1 }
})
```

## 4. <ins>Exemple avec relations</ins>
Créer un post pour un utilisateur
```TypeScript
const post = await prisma.post.create({
  data: {
    title: "Mon premier post",
    author: {
      connect: { id: 1 }
    }
  }
})
```

Lire un utilisateur avec ses posts
```TypeScript
const user = await prisma.user.findUnique({
  where: { id: 1 },
  include: {
    posts: true
  }
})
```

## 5. <ins>Dans quel cas utiliser Prisma ?</ins>
Prisma est très utilisé dans :<br>
Backend Node.js modernes

Frameworks populaires :
- Next.js
- NestJS
- Express.js
- tRPC

**Cas d’utilisation typiques**
*API REST*
```TypeScript
GET /users
POST /users
```

*SaaS / applications web*<br>
Exemple :
- gestion d’utilisateurs
- produits
- commandes
- dashboard

*Applications fullstack TypeScript*<br>
Prisma est très apprécié avec TypeScript car :
- autocomplétion
- types générés
- sécurité des requêtes

Exemple :
```TypeScript
const user: User = await prisma.user.findUnique(...)
```

## 6. <ins>Pourquoi Prisma est très populaire</ins>

**Avantages :**

✅ requêtes typiées<br>
✅ migrations intégrées<br>
✅ syntaxe simple<br>
✅ très bon DX (developer experience)<br>
✅ fonctionne bien avec TypeScript

## 7. <ins>Prisma vs SQL brut</ins>
SQL :
```sql
SELECT users.name, posts.title
FROM users
JOIN posts ON posts.authorId = users.id;
```

Prisma :
```TypeScript
const users = await prisma.user.findMany({
  include: {
    posts: true
  }
})
```

## 8. <ins>Limites de Prisma</ins>
Prisma peut être moins adapté si :
- tu fais beaucoup de SQL très complexe
- tu veux optimiser des requêtes ultra spécifiques
- tu travailles sur une énorme base legacy

Dans ces cas on peut préférer :
- TypeORM
- Sequelize
- ou du SQL brut

## 9. <ins>Exemple architecture réelle</ins>
Projet backend :
```
project/
 ├─ prisma/
 │   └─ schema.prisma
 ├─ src/
 │   ├─ routes/
 │   ├─ services/
 │   └─ controllers/
 └─ package.json
```