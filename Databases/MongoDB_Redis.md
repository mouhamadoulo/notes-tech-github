# <ins>MongoDB</ins>
<div align="center">
	<img src="images/mongodb.png" height="350" width="350" />
</div>
MongoDB est une base de données NoSQL orientée documents, idéale pour les données semi-structurées et les modèles flexibles.
  
### 1. Applications nécessitant des schémas flexibles 
 		Données qui évoluent souvent (par ex. ajout de nouveaux champs sans migration).  
 		Startups ou projets en itération rapide.  
### 2. Stockage de contenu
 		Articles, posts, commentaires, produits catalogues (e-commerce).  
 		Plates-formes de gestion de contenu (CMS), blogs.  
### 3. Applications avec de gros volumes de données hétérogènes 
 		Logs d’applications.  
 		Analyses comportementales.  
 		IoT (données variées, capteurs multiples).  
### 4. Gestion de profils utilisateurs
 		Données de profils avec attributs personnalisables.  
 		Systèmes d’authentification, comptes utilisateurs.  
### 5. Applications géospatiales
 		Données de localisation, tracking en temps réel.  
 		Recherche de lieux à proximité.  
### 6. API et microservices
 		En backend pour stocker des documents JSON proches des objets échangés par les API REST.  


# <ins>Redis</ins>
<div align="center">
	<img src="images/redis.png" height="350" width="350" />
</div>
Redis est une base en mémoire, ultra rapide, souvent utilisée pour la performance, le caching et la gestion d’état. 
 
### 1. Cache haute performance
 		Mise en cache de réponses d’API.  
 		Mise en cache de sessions utilisateurs.  
 		Réduction de la charge sur les bases principales.  
### 2. Systèmes de file de messages / streaming
 		Files de tâches (via des structures comme lists, streams).  
 		Traitement asynchrone (ex. avec Celery, BullMQ, Sidekiq).  
### 3. Stockage de sessions
 		Sessions d’applications web ou mobile.  
 		Données temporaires, tokens, paniers e-commerce.  
### 4. Compteurs et analytics en temps réel
 		Compteurs de vues.  
 		Statistiques incrémentales (like, votes, événements).  
 		Classements leaderboards via les Sorted Sets.  
### 5. Limit rate / anti-spam
 		Contrôle de débit (limitation de requêtes d'un utilisateur).  
 		Détection de comportements abusifs en temps réel.  
### 6. Pub/Sub
 		Notifications instantanées.  
 		Chats simples.  
 		Systèmes d’événements légers.  




# <ins>Exemples de projets concrets avec utilisation de MongoDB et Redis ensemble</ins>
<div align="center">
	<img src="images/mongodb_redis.png" height="350" width="350" />
</div>

##  E-commerce (Panier en temps réel + Catalogue produits)
- Contexte : Le catalogue produits est riche et persistant, Le panier doit être ultra rapide et mis à jour en temps réel
- Rôles des bases : MongoDB (produit, images, inventaire, catégories, …) , Redis (panier utilisateur, sessions, état temporaire)
- Avantages : Temps de réponse < 5 ms pour les paniers, Mise à jour immédiate sans surcharger MongoDB

## Réseau social (Profils + Flux / Notifications en temps réel)
- Contexte : Les profils utilisateurs sont structurés mais flexibles, Les likes , vues, notifications doivent être super rapides
- Rôle des bases : MongoDB (profils, posts, relations, commentaires), Redis (compteurs de likes/vues, notifications en temps réel, feed pré-calculé (timeline))
- Avantages : Combinaison stockage persistant + événements instantanés

## IoT (Stockage longue durée + traitement temps réel)
- Contexte : Beaucoup de données entrantes, Nécessité d'alerter en temps réel
- Rôle des bases : MongoDB (historique des mesures), Redis (sliding window/trigger, buffer pour ingestion, calcul temps réel)
- Avantages : MongoDB ne subit pas les pics de charge

## Application de réservation
- Contexte : Les disponibilités doivent être vérifiées en microsecondes, Les réservations doivent être persistantes
- Rôle des bases : MongoDB (réservation persistée,  historique, documents client), Redis (verrous distribués pour éviter les doubles réservations)
- Avantages : Aucun double booking, même en forte charge
