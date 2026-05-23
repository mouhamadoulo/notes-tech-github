# Node.js
Ressources (liens) :
- https://grafikart.fr/formations/formation-javascript
- https://apprendre-nodejs.fr/v1/
- https://adrienjoly.com/cours-nodejs/
- https://www.w3schools.com/nodejs/
- Projects Source Code => https://projectworlds.com/nodejs-projects-with-source-code/
- Playlist YouTube 13 vidéos : https://www.youtube.com/watch?v=hMbB0rViuMA&list=PLT2KSPhMMiqq5VCDawcDMS_pQOhU77wl-&index=4


## Qu'est-ce que c'est que Node.js ?
Node.js est une plateforme logicielle qui permet d’exécuter du JavaScript côté serveur. 
Il se démarque par l'utilisation d'un système de boucle d'évènement qui permet l’exécution d'opérations de manière asynchrone.
La plate-forme Node.js est passée du statut d’OVNI à celui d’incontournable. Elle fait fonctionner des applications web, 
de l’outillage front-end, de la distribution de fichiers pair-à-pair, des objets connectés et la NASA l’utilise même 
pour analyser les données télémétriques de ses sondes spatiales.
Avec NodeJS , JavaScript n'est plus réservé aux pages web. Vous pouvez écrire des scripts, créer des outils en ligne de commande, 
développer des serveurs web complets, automatiser des tâches ou même construire des applications de bureau.
Techniquement, Node.js est construit au-dessus du moteur JavaScript V8 utilisé par Google Chrome. Cela signifie qu’il utilise la même base que le navigateur 
pour comprendre et exécuter votre code JS, mais sans interface graphique, sans onglet, sans page HTML.



## Pour quel besoin l'utiliser ? 
Node.js est utilisé pour énormément de choses aujourd’hui, notamment parce qu’il est rapide, léger et qu’il permet de réutiliser le même langage côté navigateur et côté serveur. 
Voici quelques situations où Node.js est particulièrement utile : 

📌 - **Développer des serveurs web**
```
Par exemple, créer un système d’authentification, une API ou un site dynamique qui dialogue avec une base de données
```
📌 - **Créer des outils en ligne de commande**
```
De nombreux outils utilisés par les développeurs fonctionnent grâce à Node.js, comme npm, vite, parcel, webpack 
et même certains générateurs de site
```
📌 - **Développer des applications en temps réel**
```
Chats en ligne, jeux en ligne, tableaux collaboratifs, systèmes de notifications
```
📌 - **Automatiser et planifier des tâches**
```
Par exemple générer automatiquement des images, renommer des centaines de fichiers, envoyer des e-mails automatisés
```

Même des plateformes gigantesques comme Netflix, Paypal, Uber ou LinkedIn utilisent Node.js dans leurs serveurs !
Avant Node.js, un serveur gérait les connexions de manière très séquentielle. Si plusieurs utilisateurs demandaient des données au même moment, 
le serveur devait en quelque sorte faire la queue, ce qui pouvait devenir lent.
Node.js est devenu un standard pour les applications réseau, les microservices, le temps réel et les APIs performantes.

**Exemples de cas d'usages :**
- 🚀 **APIs REST & Backend Légers (c'est l'usage numéro 1 de Node.js)**<br>
*Pourquoi :* Rapide , léger, non bloquant, parfait pour exposer des données, idéal pour les microservices (démarrage rapides)<br>
*Exemples :* API d'e-commerce, API pour applications mobiles, Backend d'applications Saas<br>
*Outils   :* Express.js , Fastify, NestJS 

- 🚀**Applications Temps réel**<br>
*Pourquoi :* Node excelle dans les connexions persistantes et bidirectionnelles<br>
*Exemples :* Chat en temps réel, Outils collaboratifs (comme Google Docs), Jeux multi-joueurs, Notifications instantanées<br>
*Outils   :* Socket.io, WebSockets natifs, Colyseus (pour jeux)

- 🚀 **Microservices et architectures distribuées**<br>
*Pourquoi :* léger, rapide à déployer, facile à scaler horizontalement<br>
*Outils   :* NestJS (module microservices), Moleculer, Seneca 

- 🚀 **Gateway d'APIs & BFF (Backend For Frontend)**<br>
*Pourquoi :* idéal pour servir d'intermédiare entre plusieurs services API et un front-end<br>
*Outils   :* Express, Apollo Server, GraphQL Yoga

- 🚀 **Streaming de données & pipelines**<br>
Grâce au système de Streams natifs<br>
*Exemples :* Traitement vidéo/audio en streaming, ETL temps réel, Ingestion de données lourdes, Analytics temps réel

- 🚀 **Automatisation , scripts CLI et outils DevOps**<br>
Node sert souvent à créer des outils pour développeurs<br>
*Exemples :* Outils CLI (Angular CLI, Vue CLI, create-react-app), Scripts d'automatisation, Générateurs de code, Outils CI/CD personnalisés<br>
*Outils   :* Commander.js, Yargs, Inquirer.js 




## Quelques exemples d'utilisation de Node.js par les grosses entreprises
**LinkedIn**
```
Linkedin a commencé à utiliser Node.js pour son application mobile uniquement, avant d’y transférer l’ensemble de sa base de données. 
Ils ont également enregistré une vitesse d’application 20 fois plus rapide que leur itération précédente avec Ruby on Rails.
```
**Netflix**
```
Selon Netflix, ils ont construit leur interface utilisateur (UI) avec Node.js en raison de sa modularité. 
Netflix a connu un temps de démarrage deux fois plus rapide en utilisant Node.js.
```
**Uber**
```
Uber a également adopté très tôt Node.js.
Les principales raisons de leur adoption sont que Node.js offre une facilité de vérification des erreurs, 
un debugging supérieur, 
des vitesses de traitement plus rapides et un développement continu.
Ces attributs ont stimulé les opérations de développement d’Uber, leur permettant de corriger les erreurs 
presque immédiatement sans relancer le processus d’application.
```
**PayPal**
```
PayPal utilise Node.js pour construire la partie contenu de son application destinée aux utilisateurs.
Node.js unifie le JavaScript sur le frontend et le backend de chaque application. 
Ce que PayPal aime le plus de Node.js, c’est qu’il facilite ses processus de développement.
Selon Brainhub, Node.js a permis à PayPal d’accélérer le temps de chargement de son application web, 
qui est presque deux fois plus rapide et contient 33 % de lignes de code 
et 40 % de fichiers en moins que son application initiale basée sur Java.
```
**Mozilla**
```
Mozilla utilise Node.js comme langage principal, et beaucoup de ses applications web l’utilisent. 
Mozilla Persona, par exemple, est une technologie d’ouverture de session multi-plateforme construite avec Node.js.
```


## Installation Windows
- Il faut télécharger la version LTS ou Current depuis le site : https://nodejs.org
- Une fois téléchargé, ouvrez l’exécutable. Vous verrez un installateur simple. Acceptez les conditions d’utilisation. Laissez les paramètres par défaut, puis, cliquez sur Installer.
- Lorsque l'installation est terminée, ouvrez l'invite de commande et tapez : **> node -v** et ensuite vérifiez si npm est installé avec : **> npm -v**

Maintenant que Node.js est installé, vous pouvez enfin écrire votre premier programme. 
L’objectif ici n’est pas de créer tout de suite un serveur ou une application complexe, 
mais simplement de comprendre comment Node exécute du JavaScript en dehors du navigateur.
- Créer un dossier (exple : **WorkspaceNodeJS**) et dedans créer un fichier **"app.js"** dans lequel mettre le code suivant : 
```js
console.log("Bonjour, ceci est mon premier programme en Node.js !");
```
- Enregitrez le fichier , Ouvrez un terminal dans ce dossier puis exécutez : > node app.js
- Vous devriez voir apparaître le message dans votre terminal. Vous venez d’exécuter du JavaScript, sans navigateur. 

Node.js vient avec pas mal de modules intégrés (pas besoin d'install via npm) pour vous aider à travailler avec 
les fichiers, les servers, les paths, l'os , ...

**Exemples de modules intégrés :** assert, buffer, crypto, dns, events, fs, http, https, os, path, stream, v8,  ...



## 5 Projets intéressants à faire : (https://developpeur-pro.com/developpeur-node-js/node-js-5-projets-concrets)
✔️ Développer une API REST avec Express.js<br>
✔️ Créer ton première Agent IA avec Node.js<br>
✔️ Mettre en place un Système d’Authentification Sécurisé<br>
✔️ Développer L’Application de Chat en Temps Réel<br>
✔️ Construire une API Scalable avec Nest.js, TypeScript et Docker<br>
✔️ +1 (bonus) => Weather App (API-Based) - Technos : Node.js, Express.js, Axios (for external API calls), any weather API (such as OpenWeatherMap)



## Project Structure Best Practices
Voici une des meilleures manières de structurer son projet Node.js
```
my-express-app/
├── node_modules/ # Dependencies
├── config/ # Configuration files
│ ├── db.js # Database configuration
│ └── env.js # Environment variables
├── controllers/ # Route controllers
├── models/ # Database models
├── routes/ # Route definitions
├── middleware/ # Custom middleware
├── public/ # Static files
├── tests/ # Test files
├── .env # Environment variables
├── .gitignore # Git ignore file
├── app.js # Application entry point
└── package.json # Project configuration
```


## Comment fonctionne un serveur Node.js ?
L’une des forces de Node.js est sa capacité à créer des serveurs web très facilement. 
Voyons un exemple simple. Dans le même dossier, créez un fichier serveur.js :
```js
	const http = require("http");
	const serveur = http.createServer((requete, reponse) => {
    		reponse.writeHead(200, { "Content-Type": "text/plain" });
    		reponse.end("Bienvenue sur mon premier serveur Node.js !");
	});

	serveur.listen(3000, () => {
    		console.log("Serveur en écoute sur http://localhost:3000");
	});
```
Enregistrez puis lancez la commande : **> node serveur.js**<br>
Ouvrez ensuite le navigateur et entrez l'url : http://localhost:3000<br>
Vous verrez s’afficher le message du serveur.<br>
Cette petite expérience montre que Node.js peut agir comme le cerveau d’une application web, en répondant aux visiteurs, 
en envoyant des informations, en recevant des formulaires, etc.<br>
Pour comprendre ce qu’il se passe ici :<br>
- Node “écoute” les demandes qui arrivent sur votre machine à l’adresse localhost (l’ordinateur lui-même) sur le port 3000.
- À chaque demande, il répond par un texte simple. Pas de HTML pour l’instant, mais nous pourrions, bien sûr, en envoyer.



## Le rôle de NPM
npm signifie Node Package Manager. Il est installé automatiquement avec Node.js.<br>
Imaginez npm comme une immense bibliothèque contenant des centaines de milliers de modules (ou paquets) prêts à être utilisés. <br>
Ces modules permettent d’ajouter des fonctionnalités à votre projet sans les réinventer. Comme pour Composer avec PHP.<br>
Pour prendre un exemple concret : <br>
si vous voulez créer un serveur web plus confortablement et plus rapidement, vous pouvez installer le framework Express.
Dans votre dossier, lancez : 
```bash
npm init -y 
```
Cette commande crée un fichier package.json, qui sert de carte d’identité à votre projet<br>
Ensuite lancez : 
```bash
npm install express
```
On peut maintenant créer un fichier **"serveur-express.js"** : 
```js
		const express = require("express");
		const app = express();
		app.get("/", (req, res) => {
    			res.send("Bonjour avec Express !");
		});

		app.listen(3000, () => {
    			console.log("Serveur Express en ligne sur http://localhost:3000");
		});
```
On peut lancer par la suite : > node serveur-express.js <br>
Retournez sur http://localhost:3000. Vous verrez un message similaire au précédent, mais Express rend le code plus lisible, plus organisé, plus agréable.



## Pourquoi Node.js est-il si rapide ?
Node.js repose sur un modèle non bloquant. Ce terme peut paraître abstrait. 
Pour l’expliquer simplement : La plupart des serveurs traditionnels effectuent les tâches les unes après les autres. 
Si une opération prend du temps, tout le reste attend.
Node.js fonctionne différemment. Lorsqu’une opération est longue (accès à une base de données, lecture d’un fichier, connexion réseau), Node ne bloque pas. 
Il continue de répondre à d’autres demandes, puis revient à la première lorsqu’elle est prête.
C’est un peu comme une boulangère très organisée qui prépare plusieurs commandes en parallèle. Pendant que la pâte lève, elle sert un autre client. 
Elle n’attend jamais les bras croisés. Ce système basé sur les événements permet à Node de gérer des milliers de connexions simultanées avec une efficacité remarquable.



## Un exemple concret reliant les deux mondes
Supposons que vous créez une application de chat. 
Côté navigateur : une interface avec des messages qui apparaissent
Côté Node.js : un serveur qui reçoit , stocke et redistribue les messages aux utilisateurs.
Même langage , Deux univers, Une collaboration parfaite.
Node.js a profondément transformé l’univers du développement web. Il a permis à JavaScript de sortir du navigateur, 
de devenir un langage polyvalent, capable de construire des serveurs, des outils, des applications complètes. 
Il simplifie la vie des développeurs en permettant d’utiliser un seul langage pour créer à la fois l’interface utilisateur et la logique serveur.



## Top Frameworks NodeJS
Les frameworks Node.js sont devenus le premier choix des développeurs en raison de leur capacité à construire des applications intelligentes et évolutives côté serveur.<br>
🌀 **Express.js :** utilisé principalement pour créer des applications web et des APIs REST<br>
🌀 **Fastify :** rapide et modulable , axé sur les performances , conçu pour gérer un grand nombre de requêtes par seconde<br>
🌀 **Next.js :** développement web<br>
🌀 **NestJS :** puissant pour créer des applications server-side fiables, scalables et maintenables<br>
🌀 **Socket.IO :** pour créer des applications en temps réel et établir une communication bidirectionnelle entre serveurs et clients web<br>
🌀 **Hapi :** qui se targue de mettre la sécurité informatique au premier plan 
 

