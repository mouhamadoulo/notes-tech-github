Vu ton contexte, la présentation sera probablement plus intéressante si elle est orientée **retour d'expérience** plutôt que démonstration marketing. Tu as 7 ans de recul sur un même client, ce qui permet de montrer une vraie évolution des pratiques.

Je proposerais un plan qui raconte l'évolution de l'IA dans votre cycle de développement.

# Titre

**L'IA au service du développement logiciel : 7 ans d'évolution d'un projet Java/Angular**

ou

**Du copilote au développement agentique : retour d'expérience d'une équipe Java/Angular**

---

# 1. Contexte du projet (5 min)

Présenter rapidement le contexte.

* Projet Java / Angular
* Taille de l'équipe
* Durée de la mission
* Organisation Agile
* Qualité attendue
* Pourquoi introduire l'IA ?

Objectif :

> "L'objectif n'était pas de remplacer les développeurs mais d'améliorer la qualité, la vitesse et le confort de développement."

---

# 2. L'évolution de l'IA dans notre équipe (5 min)

Une frise chronologique fonctionne très bien.

Sans IA
↓

IA interne générative (Lucia)

↓

GitHub Copilot

↓

Développement agentique

Montrer que chaque étape apporte un niveau supplémentaire d'automatisation.

---

# 3. Première étape : Lucia, notre IA générative interne (10 min)

Expliquer ce qu'elle permettait.

Exemples :

* explication de code
* génération de documentation
* aide sur les API internes
* recherche dans la documentation
* rédaction de comptes-rendus
* création de tests unitaires
* aide à la compréhension d'un bug

Exemple concret :

> "Explique-moi cette classe Spring Boot."

ou

> "Génère les tests JUnit de cette classe."

Les points positifs

* sécurisée
* adaptée au contexte de l'entreprise
* gain de temps
* pas de fuite de données

Les limites

* parfois imprécise
* hallucinations
* contexte limité

---

# 4. Deuxième étape : GitHub Copilot intégré dans les IDE (15 min)

Là tu peux montrer des cas très concrets.

### Génération de code

Avant :

écrire tout le code.

Après :

Copilot propose directement :

* méthodes
* DTO
* Mapper
* Repository
* Service

---

### Refactoring

Exemple :

Transformer une boucle en Stream.

Ou

Simplifier un code complexe.

---

### Génération de tests

JUnit

Mockito

MockMvc

---

### Angular

Copilot aidait à :

* composants
* services
* observables RxJS
* formulaires
* HTML
* CSS

---

### SQL

* requêtes
* optimisation
* scripts

---

### Regex

Très utile.

---

### Documentation

JavaDoc

README

Commentaires

---

### Gain constaté

Tu peux rester pragmatique.

Par exemple :

* moins de tâches répétitives
* concentration sur la logique métier
* accélération des développements
* aide aux nouveaux arrivants

---

# 5. Dernière étape : le développement agentique (15 min)

C'est probablement la partie la plus intéressante.

Commencer par expliquer la différence.

## IA générative

Je demande.

↓

Elle répond.

---

## Copilot

Elle propose pendant que je développe.

---

## Agent IA

Je donne un objectif.

↓

L'agent réfléchit.

↓

Analyse le projet.

↓

Modifie plusieurs fichiers.

↓

Exécute des commandes.

↓

Corrige les erreurs.

↓

Propose un résultat complet.

---

Exemple

> "Ajoute une nouvelle fonctionnalité de gestion des utilisateurs."

L'agent :

* crée les entités
* crée le repository
* crée le service
* crée le contrôleur
* ajoute les tests
* modifie Angular
* met à jour les routes

Le développeur devient davantage un **superviseur** qu'un simple rédacteur de code.

---

# 6. Les bénéfices observés (10 min)

Tu peux organiser cela dans un tableau.

| Domaine              | Avant         | Avec IA  |
| -------------------- | ------------- | -------- |
| Recherche            | Documentation | IA       |
| Code répétitif       | Manuel        | Généré   |
| Tests                | Long          | Assisté  |
| Refactoring          | Lent          | Rapide   |
| Documentation        | Rare          | Générée  |
| Découverte du projet | Difficile     | Assistée |

---

# 7. Les limites rencontrées

Important pour garder de la crédibilité.

Par exemple :

* hallucinations
* code parfois faux
* oublis des règles métier
* qualité variable
* nécessité de relire
* sécurité
* confidentialité
* dépendance potentielle

La règle d'or :

> L'IA produit vite.
>
> Le développeur reste responsable.

---

# 8. Les bonnes pratiques que nous avons retenues

Exemples :

* toujours relire le code
* demander de petites tâches
* fournir un maximum de contexte
* écrire de bons prompts
* lancer les tests
* faire une revue de code classique

---

# 9. Ce qui change dans le métier de développeur

C'est une bonne conclusion.

Avant :

* écrire du code

Aujourd'hui :

* concevoir
* guider l'IA
* valider
* tester
* arbitrer

Le développeur apporte davantage de valeur sur l'architecture et la compréhension du métier.

---

# 10. Conclusion

Quelques messages clés :

* l'IA est devenue un outil quotidien
* elle améliore fortement la productivité
* elle ne remplace pas les compétences techniques
* le développement agentique marque une nouvelle étape
* demain, les développeurs travailleront de plus en plus avec des agents spécialisés

---

## Bonus : une démonstration en direct (si possible)

Une démo de 5 à 10 minutes est souvent très appréciée. Par exemple :

1. Créer un projet Spring Boot simple.
2. Demander à un agent de générer une fonctionnalité CRUD complète (entité, repository, service, contrôleur, tests).
3. Montrer la génération d'un composant Angular associé.
4. Lancer les tests et expliquer le rôle du développeur dans la validation et les ajustements.

Cette démonstration illustre concrètement le passage d'une assistance à la saisie (Copilot) à une approche où l'IA prend en charge un ensemble de tâches coordonnées, sous la supervision du développeur.
