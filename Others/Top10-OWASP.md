# Comprendre clairement le Top 10 OWASP dans le développement web

## Qu'est-ce que le Top 10 OWASP ?

Le **Top 10 OWASP** est une liste des **10 risques de sécurité les plus critiques pour les applications web**.

* **OWASP** = *Open Worldwide Application Security Project*
* C'est une organisation internationale qui aide les développeurs à créer des applications plus sûres.
* Le Top 10 sert de **référence mondiale** pour comprendre les failles les plus fréquentes.

En pratique, cela répond à la question :

> "Quelles erreurs de sécurité dois-je absolument éviter quand je développe un site ou une application web ?"

---

# Pourquoi c'est important pour un développeur web ?

Quand on développe un site, on manipule souvent :

* comptes utilisateurs
  n- mots de passe
* données personnelles
* paiements
* accès administrateur
* fichiers
* API

Une faille peut permettre à un attaquant de :

* voler des données
* se connecter sans autorisation
* modifier la base de données
* bloquer le service
* prendre le contrôle du serveur
* nuire à l'image de l'entreprise

Le Top 10 t'aide à **penser sécurité dès le développement**.

---

# Le Top 10 OWASP expliqué simplement

## 1. <ins>Broken Access Control (Contrôle d'accès cassé)</ins>

### Le problème

Un utilisateur peut accéder à des ressources qu'il ne devrait pas voir.

### Exemple

Un utilisateur change l'URL :

```text
/mon-compte/123
```

vers :

```text
/mon-compte/124
```

et voit le compte d'un autre client.

### Bonnes pratiques

* Vérifier les droits côté serveur
* Ne jamais faire confiance au front-end
* Utiliser des rôles (admin, user, etc.)

---

## 2. <ins>Cryptographic Failures (Erreurs de chiffrement)</ins>

### Le problème

Les données sensibles sont mal protégées.

### Exemple

* Mot de passe stocké en clair
* Site sans HTTPS
* Données bancaires non chiffrées

### Bonnes pratiques

* Utiliser HTTPS/TLS
* Hasher les mots de passe avec bcrypt/Argon2
* Chiffrer les données sensibles

---

## 3. <ins>Injection</ins>

### Le problème

L'application exécute des données fournies par l'utilisateur comme du code ou une commande.

### Exemple SQL Injection

```sql
SELECT * FROM users WHERE email = 'x' OR 1=1;
```

L'attaquant contourne la connexion.

### Bonnes pratiques

* Requêtes préparées
* ORM sécurisé
* Validation des entrées
* Jamais concaténer du SQL manuellement

---

## 4. <ins>Insecure Design (Conception non sécurisée)</ins>

### Le problème

Le bug vient de la logique métier ou de l'architecture, pas seulement du code.

### Exemple

Un système de reset de mot de passe sans expiration du lien.

### Bonnes pratiques

* Penser sécurité dès la conception
* Threat modeling
* Cas d'abus
* Validation des workflows sensibles

---

## 5. <ins>Security Misconfiguration (Mauvaise configuration)</ins>

### Le problème

Serveur, framework ou application mal configurés.

### Exemple

* Mode debug activé en production
* Ports ouverts inutilement
* Permissions trop larges
* Headers de sécurité absents

### Bonnes pratiques

* Configuration par environnement
* Désactiver debug en prod
* Durcir le serveur
* Ajouter les headers de sécurité

---

## 6. <ins>Vulnerable and Outdated Components</ins>

### Le problème

Tu utilises des bibliothèques ou dépendances avec des failles connues.

### Exemple

Un package npm obsolète contenant une vulnérabilité critique.

### Bonnes pratiques

* Mettre à jour régulièrement
* Scanner les dépendances (`npm audit`, Snyk, Dependabot)
* Supprimer les packages inutiles

---

## 7. <ins>Identification and Authentication Failures</ins>

### Le problème

La connexion des utilisateurs est mal gérée.

### Exemple

* Mot de passe faible accepté
* Session jamais expirée
* Pas de MFA
* Bruteforce possible

### Bonnes pratiques

* MFA
* Limitation des tentatives
* Sessions sécurisées
* Politique de mots de passe robuste

---

## 8. <ins>Software and Data Integrity Failures</ins>

### Le problème

Le code, les mises à jour ou les données peuvent être modifiés sans contrôle.

### Exemple

* CI/CD non sécurisé
* Dépendance téléchargée depuis une source compromise
* Désérialisation dangereuse

### Bonnes pratiques

* Signer les artefacts
* Vérifier les sources
* Sécuriser la CI/CD
* Contrôler l'intégrité des fichiers

---

## 9. <ins>Security Logging and Monitoring Failures</ins>

### Le problème

Les attaques passent inaperçues car rien n'est surveillé.

### Exemple

1000 tentatives de connexion échouées sans alerte.

### Bonnes pratiques

* Logs utiles
* Alertes automatiques
* Monitoring centralisé
* Historique exploitable

---

## 10. <ins>Server-Side Request Forgery (SSRF)</ins>

### Le problème

Le serveur effectue des requêtes vers des cibles choisies par l'attaquant.

### Exemple

L'utilisateur fournit une URL d'image, mais le serveur appelle :

```text
http://localhost/admin
```

### Bonnes pratiques

* Liste blanche d'URLs
* Bloquer IP internes
* Validation stricte des URLs
* Segmentation réseau

---

# En résumé simple

Le Top 10 OWASP représente les **erreurs de sécurité les plus fréquentes dans le développement web**.

Un bon développeur ne code pas seulement des fonctionnalités :

* il protège les données
* il contrôle les accès
* il sécurise les connexions
* il évite les dépendances vulnérables
* il surveille les attaques

---

# Comment l'utiliser au quotidien ?

À chaque fonctionnalité, demande-toi :

* Qui peut accéder à cette action ?
* Les données sont-elles validées ?
* Les mots de passe sont-ils sécurisés ?
* Puis-je subir une injection ?
* Les logs existent-ils ?
* Mes dépendances sont-elles à jour ?

---

# Si tu débutes en développement web

Commence par maîtriser en priorité :

1. Authentification
2. Gestion des rôles
3. Validation des entrées
4. SQL Injection
5. XSS
6. Sessions
7. HTTPS
8. Sécurité des API

---

# Conclusion

Le Top 10 OWASP est le **guide essentiel de la sécurité web pour les développeurs**.

Le connaître te rend immédiatement meilleur développeur, plus professionnel, et plus crédible en entreprise.
