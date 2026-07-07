En tant que développeur full-stack **Java + Angular**, tu peux créer des *skills* (capacités spécialisées) qui te font gagner du temps au quotidien. L'idée est de déléguer les tâches répétitives tout en gardant le contrôle sur les décisions techniques.

Voici des idées classées par domaine.

## 🚀 Développement backend (Java)

1. **Générateur de CRUD complet**

* Entité JPA
* Repository
* Service
* DTO
* Mapper (MapStruct)
* Controller
* Tests unitaires

2. **Création d'API Spring Boot**
   À partir d'une description en langage naturel :

> "Créer une API de gestion des commandes avec pagination et recherche."

L'agent génère toute la structure.

3. **Review de code**
   Il analyse un fichier Java et détecte :

* code dupliqué
* violations SOLID
* problèmes de performance
* risques de NPE
* problèmes de sécurité

4. **Générateur de tests JUnit/Mockito**

À partir d'un service Java :

* tests unitaires
* mocks
* cas limites
* assertions pertinentes

---

## 🎨 Développement Angular

5. **Générateur de composants Angular**

À partir d'un besoin :

> "Créer un composant liste des utilisateurs avec filtre."

Il génère :

* HTML
* TS
* CSS
* modèle
* routing si besoin

---

6. **Migration Angular**

Analyse :

* composants obsolètes
* API dépréciées
* standalone components
* signals
* optimisations

---

7. **Générateur de formulaires**

À partir d'un DTO Java :

```java
UserDto
```

→ génère

* ReactiveForm
* Validators
* HTML
* erreurs
* interfaces

---

## 🔄 Full Stack

8. **Génération Front + Back synchronisée**

Tu décris une entité :

```
Produit
nom
prix
stock
```

L'agent crée :

Backend :

* Entity
* Repository
* Service
* Controller

Frontend :

* Interface TS
* Service Angular
* Liste
* Formulaire
* Routing

---

9. **Synchronisation DTO Java ↔ TypeScript**

Détecte les différences entre :

```
UserDto.java
```

et

```
user.ts
```

Puis génère les mises à jour.

---

10. **Documentation automatique**

Analyse le projet et génère :

* diagrammes
* README
* documentation API
* architecture
* dépendances

---

## 🛠️ Base de données

11. **Générateur Flyway/Liquibase**

À partir des entités :

* scripts SQL
* migrations
* rollback

---

12. **Optimisation SQL**

Analyse les requêtes :

* index manquants
* JOIN inutiles
* lenteurs

---

## ☁️ DevOps

13. **Créateur Docker**

Génère :

* Dockerfile
* docker-compose
* nginx
* variables d'environnement

---

14. **Pipeline CI/CD**

Selon GitHub/GitLab/Jenkins :

* build
* tests
* Sonar
* Docker
* déploiement

---

## 🔍 Analyse de projet

15. **Architecte logiciel**

Analyse tout le dépôt et répond :

* quelles couches existent ?
* quelles dépendances sont circulaires ?
* quels packages sont trop gros ?
* quelles classes sont inutilisées ?

---

16. **Détecteur de dette technique**

Il note :

* complexité
* duplication
* conventions
* couverture de tests
* qualité globale

---

## 🤖 Assistant quotidien

17. **Explication de code**

Tu sélectionnes une classe.

L'agent explique :

* le fonctionnement
* les responsabilités
* les points d'amélioration
* les risques

---

18. **Générateur de messages Git**

À partir du diff :

```
feat(auth): add JWT refresh token support
```

---

19. **Créateur de Pull Request**

À partir des commits :

* résumé
* impacts
* checklist
* risques

---

20. **Générateur de tickets Jira**

À partir d'une demande métier.

Exemple :

> Ajouter un export Excel.

Il génère :

* User Story
* critères d'acceptation
* tâches techniques
* estimation

---

# 💡 Skills avancés (forte valeur)

* **Expert Spring Boot** : bonnes pratiques, architecture hexagonale, Spring Security, optimisation.
* **Expert Angular** : performances, RxJS, Signals, Standalone Components.
* **Expert SQL** : optimisation des requêtes, index, plans d'exécution.
* **Expert Clean Code** : refactorings et amélioration continue.
* **Expert sécurité** : détection d'injections SQL, XSS, CSRF, mauvaises configurations Spring Security.
* **Expert performance** : analyse JVM, mémoire, requêtes lentes, bundle Angular.

## Une combinaison particulièrement puissante

Si tu construis un agent IA personnel, je commencerais par ces 5 skills, qui couvrent une grande partie du quotidien d'un développeur Java/Angular :

1. **Analyse de code et revue automatique** (qualité, sécurité, performance).
2. **Génération Full Stack** (Spring Boot + Angular à partir d'une spécification).
3. **Génération de tests** (JUnit, Mockito, Cypress/Jasmine selon le frontend).
4. **Documentation et explication du code** (README, architecture, diagrammes, onboarding).
5. **Assistant Git/Jira** (messages de commit, descriptions de Pull Request, tickets techniques).

En les combinant, tu obtiens un véritable copilote de développement capable d'accélérer la création de fonctionnalités, d'améliorer la qualité du code et de réduire les tâches répétitives.
