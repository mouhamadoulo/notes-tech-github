# Supabase
<div align="center">
	<img src="../images/supabase.jpg" height="300" width="300" />
</div>

## 🧠 <ins>Qu’est-ce que Supabase ?</ins>
Supabase est une plateforme backend open-source qui permet de créer rapidement des applications web et mobile.<br>
Son objectif est simple :<br>
👉 être une alternative open-source à **Firebase**.

Avec **Supabase**, tu obtiens :
- une base de données
- une authentification
- une API automatique
- du stockage de fichiers
- des fonctions backend
- du temps réel

tout ça sans créer ton propre serveur.<br>
La différence principale : Supabase est basé sur SQL, pas sur NoSQL.

## 🏗️ <ins>Sur quoi est construit Supabase ?</ins>
Supabase repose principalement sur :

🗄️ **PostgreSQL**

C’est une base de données relationnelle très puissante utilisée dans beaucoup de grandes entreprises.

Donc contrairement à Firebase :
| Plateforme | Type de base     |
| ---------- | ---------------- |
| Firebase   | NoSQL            |
| Supabase   | SQL (PostgreSQL) |

Exemple table :
```sql 
users
id | name | email
```

## ⚙️ <ins>Les services principaux de Supabase</ins>

### 1. Base de données (PostgreSQL)
La base de données Supabase est :
- relationnelle
- très puissante
- extensible
- compatible SQL

Exemple :
```sql 
SELECT * FROM users;
```

Tu peux faire :
- relations
- jointures
- requêtes complexes
- transactions

C’est beaucoup plus puissant que NoSQL pour certaines apps.

### 2. Authentification
**Supabase Auth**<br>
Supabase gère les comptes utilisateurs.

Connexion possible avec :
- email / mot de passe
- Google
- GitHub
- Apple
- Discord
- Magic link (email)

Exemple :
```js
supabase.auth.signInWithPassword()
```

### 3. API automatique
Une des forces de Supabase :<br>
👉 toutes les tables deviennent automatiquement une API REST.

Si tu crées une table :
```
users
```

Tu peux directement faire :
```
GET /users
POST /users
DELETE /users
```
Sans écrire de backend.<br>
Supabase génère aussi une API GraphQL.

### 4. Temps réel
Supabase peut écouter les changements dans la base.

Exemple :
```
si quelqu'un ajoute un message
→ l'app reçoit la mise à jour instantanément
```
Fonctionne avec :
- WebSockets
- Postgres replication

### 5. Stockage de fichiers
**Supabase Storage**

Permet de stocker :
- images
- vidéos
- documents
- avatars

Exemple :
```
avatars/user1.png
```

### 6. Fonctions backend
**Supabase Edge Functions**

Permet d’exécuter du code côté serveur.<br>
Utilise :
- Deno

Exemple :
```js
serve(async (req) => {
  return new Response("Hello World")
})
```

### 7. Dashboard
Supabase propose un dashboard web pour :
- gérer la base
- voir les utilisateurs
- gérer le stockage
- écrire du SQL

Interface similaire à :
- phpMyAdmin
- ou Firebase Console

## 🧱 <ins>Architecture Supabase</ins>
Architecture simplifiée :
```
Application
   │
   ▼
Supabase Client SDK
   │
   ▼
API auto générée
   │
   ▼
PostgreSQL
```
Supabase est construit avec plusieurs outils open-source :
- PostgREST
- GoTrue
- Realtime

## 🧑‍💻 <ins>Exemple simple avec JavaScript</ins>
Avec le client :
```js
const { data } = await supabase
  .from("users")
  .select("*")
```
Tu interroges directement la base.

## 🚀 <ins>Pourquoi Supabase est populaire</ins>
**Avantages**

✅ open-source<br>
✅ SQL puissant<br>
✅ pas de vendor lock-in<br>
✅ facile à auto-héberger<br>
✅ API automatique<br>
✅ très bon pour les startups

**Inconvénients**

❌ moins mature que Firebase<br>
❌ certaines features encore en développement<br>
❌ scaling parfois plus complexe

## 🆚 <ins>Supabase vs Firebase</ins>
|                    | Firebase  | Supabase    |
| ------------------ | --------- | ----------- |
| Type DB            | NoSQL     | SQL         |
| Open source        | ❌        | ✅         |
| Backend            | Google    | Open source |
| Requêtes complexes | difficile | très facile |
| Vendor lock-in     | fort      | faible      |

## 🧠 <ins>Quand utiliser Supabase ?</ins>
Supabase est très bien pour :
- SaaS
- dashboards
- apps web
- apps mobiles
- projets avec relations complexes

## 🧑‍💻 <ins>Technologies utilisées avec Supabase</ins>
Supabase est souvent utilisé avec :
- React
- Next.js
- Vue.js
- Svelte
- Flutter

## 💰 <ins>Prix</ins>
Supabase propose :

🟢 **Free tier**
- base PostgreSQL
- authentification
- stockage limité

🔵 **Pro**<br>
Tu paies selon :
- base de données
- stockage
- bande passante

## 🧠 <ins>En résumé</ins>
Supabase = backend moderne basé sur SQL

Il fournit :
- base de données PostgreSQL
- authentification
- API automatique
- stockage
- fonctions backend
- temps réel

Le tout open-source.