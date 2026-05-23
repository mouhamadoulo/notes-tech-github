# FastAPI
Si Django est le "tout-en-un" robuste et complet, FastAPI est le bolide de course moderne, léger et ultra-rapide.<br>
C'est un framework Python plus récent (créé vers 2018) <br>
qui sert spécifiquement à construire des API (Application Programming Interfaces). <br>
Une API, c'est ce qui permet à deux applications de discuter entre elles <br>
(par exemple, entre une application mobile et un serveur).

Voici ce qui rend FastAPI spécial :
- **1. La Vitesse (Performance)**
```
Comme son nom l'indique, il est extrêmement rapide. 
Sur le plan des performances, il rivalise avec des langages comme Node.js ou Go. 
Cela est possible grâce à sa gestion "asynchrone" (il peut gérer plusieurs tâches en même temps 
sans attendre que la précédente soit finie).
```

- **2. La Validation Automatique**
```
C’est l’un de ses plus gros points forts. 
Grâce aux "Type Hints" de Python (le fait de préciser si une donnée est un texte, un nombre, etc.), 
FastAPI vérifie automatiquement si les données envoyées par l'utilisateur sont correctes. 
S'il y a une erreur, il renvoie un message clair sans que tu aies à coder la vérification.
```

- **3. La Documentation "Auto-générée"**
```
C'est souvent l'effet "Wahou" pour les développeurs. 
Dès que tu écris ton code, FastAPI génère une page web interactive (via Swagger UI) 
qui permet de tester ton API directement dans le navigateur, sans aucun effort supplémentaire.
```

## Comparaison rapide : Django vs FastAPI
|Caractéristique|Django|FastAPI|
|---------------|------|-------|
|Philosophie|"Tout inclus" (Batteries included)"|Minimaliste et spécialisé|
|Vitesse|Standard|Ultra-rapide (Asynchrone)|
|Courbe d'apprentissage|Plus longue (beaucoup de concepts)|Plus courte (très intuitif)|
|Usage idéal|Sites web complets, blogs, e-commerce|Microservices, IA, applications temps réel|
|Interface de gestion|Incluse (Admin Django)|Non (à créer soi-même)|

**Lequel choisir ?**<br>
- Choisis Django si tu veux créer un site web complet avec une base de données, des comptes utilisateurs et un panneau d'administration en un temps record.
- Choisis FastAPI si tu veux créer un service ultra-performant, léger, ou si tu travailles sur un projet de Data Science / IA qui doit exposer des résultats rapidement.