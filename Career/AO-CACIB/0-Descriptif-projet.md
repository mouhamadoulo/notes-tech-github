
# Contexte 
Le département de paiement de la banque est amené à recevoir des messages de la part des applications Back Office via une file IBM MQ Series. Ces messages vont transiter dans une application de routage pour être transférés vers d’autres destinations.

# A réaliser
Créer une application Web qui permet :
-	De lire et de stocker les messages déposés sur une file IBM MQ Series dans une base de données relationnelle
-	D’exposer des API REST pour la consultation des messages via une IHM
Cette application devrait répondre à des contraintes de performance et de résilience étant donnée la volumétrie importante de messages à traiter.

# Environnement technique :
-	Langage : Java 21+
-	Framework Spring Boot 4+
-	Framework Angular 22
-	Base de données relationnelle : aucune préférence
-	Projet Maven
-	Documentation pour tester l’application
-	Utilisation de docker si possible
-	Pusher le code sur github

# Out of scope :
-	Gestion de l’authentification et des autorisations
