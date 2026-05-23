# Modules de l'écosystème Spring
- Spring MVC
- Spring Boot
- Spring Data
- Spring Batch
- Spring Security
- Spring Cloud
- Spring Session
- etc.

## 🏗️ <ins>Spring MVC</ins>
**Spring MVC** est un framework Java du projet Spring qui sert à créer des applications web (sites web, APIs REST) 
selon le pattern **MVC (Model – View – Controller)**.

👉 Il est très utilisé pour les applications professionnelles et les backends robustes.

**C’est quoi le MVC ?**<br>
MVC = une façon d’organiser le code :
- Model → données + logique métier
- View → interface utilisateur (HTML, JSP, Thymeleaf…)
- Controller → reçoit les requêtes HTTP et décide quoi faire

Spring MVC applique cette séparation clairement.

**Architecture Spring MVC**<br>
Quand un client (navigateur, mobile, Postman) envoie une requête :
```
Client → DispatcherServlet → Controller → Service → Repository → DB
                              ↓
                           View (HTML / JSON)

```
Élément clé : **DispatcherServlet**<br>
- C’est le cerveau de Spring MVC
- Il reçoit toutes les requêtes HTTP
- Il les redirige vers le bon contrôleur

🔄 **Cycle de vie d’une requête**<br>
**1.** Client envoie une requête HTTP<br>
**2.** DispatcherServlet intercepte<br>
**3.** Trouve le Controller correspondant<br>
**4.** Exécute la méthode<br>
**5.** Retourne : une vue HTML ou un JSON (API REST)

⭐ **Pourquoi utiliser Spring MVC ?**<br>
✅ Architecture claire (MVC)<br>
✅ Sécurité (Spring Security)<br>
✅ Intégration facile avec : Spring Boot, JPA / Hibernate, Bases SQL / NoSQL<br>
✅ Très utilisé en entreprise<br>
✅ Performant et scalable<br>

🔥 **Spring MVC vs Spring Boot**
|Spring MVC	|Spring Boot|
|-----------|-----------|
|Framework web	|Projet qui simplifie Spring|
|Configuration manuelle	|Configuration automatique|
|Plus verbeux	|Plus rapide à démarrer|

## 🌱 <ins>Spring Boot</ins>
**Spring Boot** est un framework qui **simplifie la création d’applications Spring**.

**À quoi ça sert ?**<br>
- Démarrer rapidement un projet Spring
- Éviter la configuration complexe (XML, annotations excessives)
- Fournir des configurations automatiques (auto-configuration)

**Caractéristiques clés :**<br>
- Serveur embarqué (Tomcat, Jetty…)
- Démarrage rapide (main Java)
- Configuration par défaut intelligente
- Très utilisé pour les microservices

👉 Spring Boot n’est pas un framework différent, c’est une façon plus simple d’utiliser Spring.

## 📦 <ins>Spring Data</ins>
**Spring Data** facilite l’accès et la manipulation des bases de données.

**À quoi ça sert ?**<br>
- Réduire le code nécessaire pour accéder aux données
- Gérer les bases relationnelles et NoSQL

**Exemples :**<br>
- Spring Data JPA (SQL)
- Spring Data MongoDB
- Spring Data Redis

**Avantage majeur :**<br>
Tu écris juste une interface :
```
interface UserRepository extends JpaRepository<User, Long> {}
```
Spring génère automatiquement les méthodes CRUD.

## 🔄 <ins>Spring Batch</ins>
**Spring Batch** est conçu pour le traitement de gros volumes de données (batch processing).

**Cas d’usage :**<br>
- Import / export de fichiers
- Traitement nocturne
- Calculs massifs (facturation, paie, statistiques)

**Concepts clés :**<br>
- Job : traitement global
- Step : étape du traitement
- ItemReader / ItemProcessor / ItemWriter

👉 Très utilisé dans les systèmes bancaires et industriels.

## 🔐 <ins>Spring Security</ins>
**Spring Security** gère la sécurité des applications.

**Fonctionnalités :**<br>
- Authentification (login, OAuth2, JWT…)
- Autorisation (rôles, permissions)
- Protection contre attaques (CSRF, XSS…)

**Exemples :**<br>
- Sécuriser des API REST
- Gérer les rôles USER, ADMIN
- Intégration avec LDAP, OAuth2, Keycloak

👉 C’est le standard de sécurité dans Spring.

## ☁️ <ins>Spring Cloud</ins>
**Spring Cloud** aide à construire des applications distribuées et microservices.

**Problèmes qu’il résout :**<br>
- Découverte de services
- Configuration centralisée
- Load balancing
- Circuit breaker

**Outils connus :**<br>
- Spring Cloud Config
- Eureka
- Gateway
- OpenFeign

👉 Indispensable pour les architectures microservices.

## 🕒 <ins>Spring Session</ins>
**Spring Session** gère les sessions utilisateur de manière avancée.

**Pourquoi l’utiliser ?**<br>
- Stocker les sessions ailleurs que en mémoire locale
- Partager les sessions entre plusieurs serveurs

**Stockages possibles :**<br>
- Redis
- JDBC
- MongoDB

**Cas typique :**<br>
➡️ Application load-balancée avec plusieurs instances