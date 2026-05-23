# 🏦 Notions & Concepts Importants pour un Développeur Fullstack en Banque

---

# 🎯 Objectif

Dans une banque, on n’attend pas seulement un développeur qui code.  
On attend quelqu’un qui comprend :

- la criticité des applications
- les enjeux métier
- la sécurité
- la conformité
- la fiabilité
- la traçabilité
- la qualité logicielle

---

# 🧠 1. Compréhension du Métier Bancaire

---

# Produits bancaires de base

À connaître au minimum :

- compte courant
- compte joint
- livret d’épargne
- carte bancaire
- virement
- prélèvement
- crédit
- assurance
- placements

## Pourquoi c’est utile

Pour comprendre les parcours clients et les impacts métier.

---

# Typologie des clients

- Particuliers
- Professionnels
- Entreprises
- Associations
- Personnes Morales

## Pourquoi

Les règles métiers changent selon le type de client.

---

# Cycle de vie client

- Prospection
- Entrée en relation
- Ouverture de compte
- Activation
- Mise à jour dossier
- Contrôles périodiques
- Fermeture compte

---

# 🛡️ 2. Conformité & Réglementaire

---

# KYC

**Know Your Customer**

Identifier le client et maintenir les données à jour.

## À maîtriser

- collecte des infos
- justificatifs
- relances
- contrôles
- actualisation périodique

---

# AML / LCB-FT

**Anti Money Laundering**  
**Lutte Contre le Blanchiment et le Financement du Terrorisme**

## À comprendre

- détection anomalies
- alertes
- surveillance
- déclarations réglementaires

---

# FATCA / CRS / EAI

Réglementations fiscales internationales.

## Important

Gestion de :

- résidence fiscale
- nationalité
- TIN
- déclarations

---

# PPE / Sanctions

- Personnes Politiquement Exposées
- pays sensibles
- listes de sanctions

## Impacts IT

- workflows renforcés
- validations manuelles
- blocages éventuels

---

# RGPD

Protection des données personnelles.

## À maîtriser

- minimisation des données
- consentement
- traçabilité
- droit à l’oubli (si applicable)
- sécurité

---

# 🔐 3. Sécurité Applicative

---

# Authentification

Qui est l’utilisateur ?

Exemples :

- login / mot de passe
- SSO
- MFA
- OAuth2
- OpenID Connect

---

# Autorisation

Que peut faire l’utilisateur ?

- rôles
- permissions
- habilitations agence / siège / admin

---

# Sécurité OWASP

Connaître les bases :

- injection SQL
- XSS
- CSRF
- fuite de données
- auth faible

---

# Chiffrement

- données sensibles
- mots de passe hashés
- TLS / HTTPS

---

# Journalisation sécurisée

Logs exploitables sans exposer de données sensibles.

---

# 🏗️ 4. Architecture Applicative

---

# APIs REST

Indispensable en fullstack.

## À maîtriser

- GET / POST / PUT / DELETE
- statuts HTTP
- pagination
- validation
- versionning

---

# Architecture distribuée

Souvent présente :

- microservices
- services internes
- batchs
- brokers
- API gateways

---

# Couplage faible

Créer des modules maintenables et évolutifs.

---

# Legacy + Modernisation

Très fréquent en banque :

- ancien SI
- nouvelles APIs
- coexistence technologies

---

# 🗄️ 5. Données & Bases de Données

---

# SQL solide

Compétence majeure.

## À maîtriser

- joins
- index
- transactions
- contraintes
- performance
- requêtes complexes

---

# Intégrité des données

Les données bancaires doivent être fiables.

- unicité
- cohérence
- historisation
- non-régression

---

# Transactions ACID

Important pour :

- virements
- modifications sensibles
- synchronisation de données

---

# Historisation / Audit

Savoir :

- qui a changé quoi
- quand
- ancienne valeur / nouvelle valeur

---

# ⚙️ 6. Industrialisation & Delivery

---

# Git

- branches
- merge request
- code review

---

# CI/CD

Comprendre pipelines :

- build
- tests
- quality gate
- déploiement

---

# Environnements

- DEV
- TEST
- UAT
- PREPROD
- PROD

---

# Feature toggles

Déployer sans exposer immédiatement.

---

# 🧪 7. Qualité Logicielle

---

# Tests unitaires

Tester la logique métier.

---

# Tests d’intégration

Tester les échanges entre composants.

---

# Tests E2E

Tester le parcours utilisateur.

---

# Couverture de code

Souvent suivie via SonarQube.

---

# Clean Code

- lisibilité
- simplicité
- maintenabilité

---

# Dette technique

Savoir l’identifier et la réduire progressivement.

---

# 📈 8. Performance & Résilience

---

# Performance

- temps de réponse
- optimisation SQL
- cache
- lazy loading front

---

# Résilience

Le système doit supporter :

- erreurs réseau
- timeouts
- indisponibilité service tiers

---

# Monitoring

Connaître :

- logs
- métriques
- alerting
- dashboards

---

# 🔄 9. Processus Métier & Workflow

---

# Workflow

Très fréquent :

- dossier à compléter
- validation
- rejet
- correction
- approbation
- clôture

---

# Statuts métier

Toujours importants :

- brouillon
- en attente
- validé
- rejeté
- clôturé

---

# Gestion documentaire

Documents :

- justificatifs identité
- statuts société
- avis fiscal
- pièces KYC

---

# 👥 10. Soft Skills Cruciales

---

# Communication

Expliquer la technique au métier.

---

# Analyse

Comprendre le vrai besoin.

---

# Rigueur

Une erreur peut avoir un impact réel.

---

# Priorisation

Tout est urgent en banque → savoir arbitrer.

---

# Travail transverse

Avec :

- BA
- PO
- conformité
- support
- production
- offshore

---

# 🤖 11. IA Générative (de plus en plus demandée)

---

# Cas utiles

- génération de code
- tests
- SQL
- documentation
- refactoring
- analyse incident

---

# Bonne posture

L’IA aide, mais la responsabilité reste humaine.

---

# 📚 12. Concepts Bonus Très Valorisés

---

# Domain Driven Design (DDD)

Aligner code et métier.

---

# Event Driven Architecture

Événements métier.

---

# BPM / Workflow Engine

Camunda, etc.

---

# Accessibilité

Applications internes inclusives.

---

# UX Métier

Fluidifier les écrans complexes.

---

# 🚀 13. Ce qu’un bon développeur banque doit démontrer

---

# Il sait coder

Oui, mais aussi :

- sécuriser
- tester
- tracer
- comprendre le métier
- communiquer
- livrer proprement
- maintenir dans le temps

---

# 💬 Phrase forte en entretien

> Dans un contexte bancaire, un bon développement ne se limite pas à fonctionner : il doit être fiable, traçable, sécurisé, conforme et maintenable.

---

# 📝 Checklist Personnelle

- [ ] Je maîtrise REST
- [ ] Je maîtrise SQL
- [ ] Je comprends KYC
- [ ] Je comprends sécurité
- [ ] Je sais tester
- [ ] Je sais analyser un besoin
- [ ] Je sais travailler en Agile
- [ ] Je sais expliquer simplement
- [ ] Je sais intervenir sur legacy
- [ ] Je sais livrer proprement

---