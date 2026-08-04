# ✅ Checklist sécurité — Développement d'applications Web (Angular / Spring Boot / Full Stack)

## 🔐 1. Architecture et conception sécurisée

- [ ] Définir les exigences de sécurité dès la phase de conception (Security by Design)
- [ ] Appliquer le principe du moindre privilège (Least Privilege)
- [ ] Séparer clairement les responsabilités :
  - [ ] Frontend (présentation uniquement)
  - [ ] Backend (logique métier et contrôle d'accès)
  - [ ] Base de données (persistance)
- [ ] Ne jamais faire confiance aux données provenant du client
- [ ] Définir une stratégie de gestion des erreurs sécurisée
- [ ] Éviter de révéler des informations sensibles dans les messages d'erreur
- [ ] Documenter les flux d'authentification et d'autorisation
- [ ] Réaliser une modélisation des menaces (Threat Modeling)

---

# 🌐 2. Sécurité Frontend Angular

## Protection du code client

- [ ] Ne jamais stocker de secrets dans le frontend
- [ ] Ne jamais exposer :
  - [ ] Clés API privées
  - [ ] Tokens sensibles
  - [ ] Credentials
  - [ ] Variables d'environnement confidentielles

- [ ] Activer le mode production Angular
- [ ] Supprimer les logs sensibles avant déploiement
- [ ] Éviter d'inclure des dépendances inutilisées
- [ ] Maintenir Angular et les packages à jour

---

## Prévention XSS (Cross-Site Scripting)

- [ ] Ne jamais utiliser `innerHTML` sans sanitization
- [ ] Utiliser les mécanismes natifs Angular :
  - [ ] Interpolation (`{{ }}`)
  - [ ] Property binding (`[property]`)
  - [ ] Angular Sanitizer

- [ ] Éviter le bypass de sécurité :
  - [ ] `bypassSecurityTrustHtml`
  - [ ] `bypassSecurityTrustScript`

- [ ] Échapper les contenus utilisateurs affichés
- [ ] Valider les contenus riches (HTML, Markdown, fichiers)

---

## Gestion des tokens côté Angular

- [ ] Éviter le stockage des JWT dans `localStorage`
- [ ] Préférer :
  - [ ] Cookies HttpOnly + Secure
  - [ ] Protection CSRF adaptée

- [ ] Configurer une expiration courte des tokens
- [ ] Implémenter un mécanisme de refresh token sécurisé
- [ ] Nettoyer les tokens lors du logout
- [ ] Éviter d'envoyer les tokens dans les URLs

---

# 🛡️ 3. Sécurité Backend Spring Boot

## Authentification

- [ ] Utiliser un framework reconnu :
  - [ ] Spring Security
  - [ ] OAuth2
  - [ ] OpenID Connect

- [ ] Ne jamais implémenter son propre système crypto
- [ ] Stocker les mots de passe avec un algorithme robuste :
  - [ ] BCrypt
  - [ ] Argon2
  - [ ] PBKDF2

- [ ] Appliquer une politique de mots de passe robuste
- [ ] Ajouter MFA lorsque nécessaire
- [ ] Bloquer ou ralentir les tentatives répétées de connexion

---

## Autorisation

- [ ] Vérifier les permissions côté backend uniquement
- [ ] Ne jamais se baser uniquement sur les contrôles Angular
- [ ] Implémenter :
  - [ ] RBAC (Role Based Access Control)
  - [ ] ABAC si nécessaire

- [ ] Vérifier les droits sur chaque ressource
- [ ] Éviter les contrôles d'accès uniquement par URL

Exemple :

❌ Mauvais :
```
GET /api/users/123
```

sans vérifier que l'utilisateur peut accéder à l'utilisateur 123.

---

# 🔑 4. Gestion des JWT et sessions

- [ ] Utiliser une signature JWT forte :
  - [ ] RS256
  - [ ] ES256

- [ ] Ne jamais utiliser :
  - [ ] Algorithme "none"
  - [ ] Secrets faibles

- [ ] Vérifier :
  - [ ] Signature
  - [ ] Expiration (`exp`)
  - [ ] Émetteur (`iss`)
  - [ ] Audience (`aud`)

- [ ] Ne pas mettre d'informations sensibles dans les JWT
- [ ] Prévoir une stratégie de révocation

---

# 🗄️ 5. Sécurité base de données

## Protection contre les injections SQL

- [ ] Toujours utiliser :
  - [ ] Prepared Statements
  - [ ] JPA/Hibernate correctement configuré

- [ ] Éviter les requêtes SQL concaténées :

❌
```java
"SELECT * FROM users WHERE name='" + username + "'"
````

* [ ] Valider les paramètres entrants
* [ ] Limiter les droits du compte SQL utilisé par l'application

---

## Données sensibles

* [ ] Chiffrer les données sensibles au repos
* [ ] Ne jamais stocker les mots de passe en clair
* [ ] Masquer les données sensibles dans les logs
* [ ] Appliquer une politique de rétention des données

---

# 📥 6. Validation des entrées

* [ ] Ne jamais faire confiance aux entrées utilisateur
* [ ] Valider côté frontend ET backend
* [ ] Utiliser des DTO dédiés
* [ ] Ajouter des contraintes :

Spring :

```java
@NotNull
@Size(max = 100)
@Email
```

* [ ] Vérifier :

  * [ ] Taille maximale
  * [ ] Format
  * [ ] Type
  * [ ] Encodage

---

# 📂 7. Upload de fichiers

* [ ] Limiter les extensions autorisées
* [ ] Vérifier le MIME type réel
* [ ] Limiter la taille des fichiers
* [ ] Renommer les fichiers côté serveur
* [ ] Stocker hors du dossier public
* [ ] Scanner les fichiers si nécessaire
* [ ] Bloquer :

  * [ ] Scripts exécutables
  * [ ] Fichiers HTML dangereux
  * [ ] Double extensions

---

# 🌍 8. Sécurité HTTP

## Headers de sécurité

* [ ] Configurer :

```
Content-Security-Policy
X-Frame-Options
X-Content-Type-Options
Strict-Transport-Security
Referrer-Policy
Permissions-Policy
```

* [ ] Activer HTTPS partout
* [ ] Désactiver HTTP en production
* [ ] Configurer correctement CORS

---

## CORS

* [ ] Ne jamais utiliser :

```
Access-Control-Allow-Origin: *
```

avec authentification.

* [ ] Autoriser uniquement les domaines nécessaires
* [ ] Limiter les méthodes HTTP
* [ ] Limiter les headers exposés

---

# 🛠️ 9. Gestion des dépendances

* [ ] Scanner régulièrement les dépendances :

  * [ ] npm audit
  * [ ] OWASP Dependency Check
  * [ ] Snyk
  * [ ] Dependabot

* [ ] Supprimer les librairies inutilisées

* [ ] Mettre rapidement à jour les dépendances vulnérables

* [ ] Vérifier la provenance des packages

---

# 📝 10. Logs et monitoring

* [ ] Logger les événements importants :

  * [ ] Connexions
  * [ ] Échecs d'authentification
  * [ ] Actions sensibles

* [ ] Ne jamais logger :

  * [ ] Passwords
  * [ ] Tokens
  * [ ] Secrets
  * [ ] Données personnelles sensibles

* [ ] Centraliser les logs

* [ ] Mettre en place une alerte sécurité

* [ ] Conserver une traçabilité des actions critiques

---

# 🚀 11. Sécurité CI/CD

* [ ] Scanner le code automatiquement

* [ ] Ajouter du SAST :

  * [ ] SonarQube
  * [ ] Semgrep

* [ ] Ajouter du DAST :

  * [ ] OWASP ZAP

* [ ] Scanner les images Docker

* [ ] Ne jamais stocker de secrets dans Git

* [ ] Utiliser un gestionnaire de secrets :

  * [ ] Vault
  * [ ] AWS Secrets Manager
  * [ ] Azure Key Vault

---

# 🐳 12. Sécurité Docker / Kubernetes

* [ ] Utiliser des images officielles
* [ ] Scanner les images
* [ ] Ne pas exécuter les containers en root
* [ ] Limiter les permissions Kubernetes
* [ ] Utiliser des namespaces séparés
* [ ] Gérer les secrets correctement
* [ ] Mettre à jour régulièrement les images

---

# 🧪 13. Tests sécurité

* [ ] Ajouter des tests d'autorisation
* [ ] Tester les accès interdits
* [ ] Tester les injections SQL
* [ ] Tester les attaques XSS
* [ ] Tester les attaques CSRF
* [ ] Tester les uploads malveillants
* [ ] Faire des tests de pénétration régulièrement

---

# 📚 14. Référentiels sécurité

* [ ] Suivre les recommandations :

  * [ ] OWASP Top 10
  * [ ] OWASP ASVS
  * [ ] CWE Top 25
  * [ ] NIST Cybersecurity Framework

* [ ] Former régulièrement les développeurs aux risques sécurité

---

# ✅ Checklist finale avant mise en production

* [ ] Aucun secret dans le repository
* [ ] HTTPS activé
* [ ] Authentification sécurisée
* [ ] Autorisations testées
* [ ] Dépendances à jour
* [ ] Logs sécurisés
* [ ] Headers HTTP configurés
* [ ] Scan sécurité effectué
* [ ] Tests d'intrusion réalisés
* [ ] Plan de réponse incident défini


Cette checklist peut servir de **base de Definition of Done sécurité**, de **revue de code**, ou de **garde-fou avant mise en production** pour une équipe Angular/Spring Boot ou une stack équivalente.
