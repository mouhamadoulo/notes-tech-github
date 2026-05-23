# 🚀 Synthèse Entretien – BRED Banque Populaire + Glossaire + Stack Technique

---

# 🏢 1. Ce qu’il faut savoir sur BRED Banque Populaire (minimum utile en entretien)

## Identité

- **BRED Banque Populaire** est une banque française coopérative.
- Elle fait partie du groupe :contentReference[oaicite:0]{index=0} (Banque Populaire + Caisse d’Épargne).
- C’est l’une des plus importantes banques du réseau Banque Populaire. :contentReference[oaicite:1]{index=1}

---

## Positionnement

BRED est active sur plusieurs métiers :

- Banque de détail (particuliers)
- Banque des professionnels
- Banque des entreprises
- Gestion privée / patrimoine
- Banque internationale
- Assurance
- Salle des marchés
- Financement du commerce international :contentReference[oaicite:2]{index=2}

---

## Présence géographique

En plus de la France :

- Île-de-France
- Normandie
- Outre-mer
- Asie du Sud-Est
- Pacifique
- Afrique
- Suisse

👉 Donc culture d’entreprise plus large qu’une banque locale. :contentReference[oaicite:3]{index=3}

---

## Taille / chiffres clés

- ~1,6 million de clients
- +7 000 collaborateurs
- Réseau de plusieurs centaines d’agences
- Forte activité en France et à l’international :contentReference[oaicite:4]{index=4}

---

## Culture à comprendre

## Banque coopérative

Cela signifie :

- orientation long terme
- relation client durable
- ancrage territorial
- sociétaires
- stabilité

👉 En entretien : parler de **qualité, confiance, relation durable, conformité**.

---

## Ce que cela implique côté IT

Le SI bancaire doit être :

- robuste
- sécurisé
- traçable
- réglementairement conforme
- maintenable
- évolutif

---

## Ce qu’ils attendent probablement d’un développeur

- sérieux
- autonomie
- qualité de code
- compréhension métier
- communication claire
- capacité à travailler sur du legacy + moderne
- livraison fiable

---

# 🧠 2. Glossaire des Abréviations de l’Appel d’Offres

---

# KYC

## Signification
**Know Your Customer**

## Définition
Ensemble des contrôles permettant d’identifier un client et de vérifier son profil.

## Cas d’usage

- identité client
- justificatifs
- revenus
- activité
- risque
- mise à jour périodique

## Techniquement

- formulaires
- workflows
- documents
- validations
- règles métier
- historisation

---

# CRM

## Signification
**Customer Relationship Management**

## Définition
Outil de gestion de la relation client.

## Cas d’usage

- fiche client
- historique interactions
- opportunités commerciales
- suivi conseiller
- segmentation

---

# PEA

## Signification
**Plan d'Épargne en Actions**

## Définition
Produit d’épargne / investissement en actions avec fiscalité avantageuse sous conditions.

## Dans l’AO

Contrôle de la **mono-détention** = vérifier qu’un client ne possède pas plusieurs PEA interdits par la réglementation.

---

# FATCA

## Signification
**Foreign Account Tax Compliance Act**

## Définition
Réglementation américaine obligeant les institutions financières à identifier les contribuables US.

## Cas d’usage

- client américain
- résidence fiscale
- déclaration réglementaire

---

# EAI

## Signification
**Échange Automatique d’Informations**

## Définition
Échange international de données fiscales entre États (équivalent global inspiré du CRS). :contentReference[oaicite:5]{index=5}

## Cas d’usage

- résidence fiscale
- collecte TIN
- reporting annuel

---

# DRC

## Interprétation probable (contexte bancaire)
Souvent utilisé pour :

**Dossier de Relation Client**  
ou référentiel client interne.

## Dans l’AO

- actualisation du DRC
- conformité du DRC
- données client à jour

👉 En entretien : parler de **qualité de donnée client**.

---

# SF

## Interprétation probable
Très probablement :

**Sécurité Financière**

Dans les banques françaises cela regroupe :

- LCB-FT
- sanctions
- PPE
- surveillance réglementaire
- contrôles conformité

👉 À confirmer en entretien.

---

# PPE

## Signification
**Personnes Politiquement Exposées**

## Définition
Clients exposés à des risques accrus de corruption / blanchiment.

## Cas d’usage

- contrôle renforcé
- alertes
- validations spécifiques

---

# BA

## Signification
**Business Analyst**

## Rôle

Interface entre métier et IT.

---

# PO

## Signification
**Product Owner**

## Rôle

Priorise le backlog et porte la vision produit.

---

# DTSO

## Probable signification interne
Nom de direction interne BRED.

👉 Pas nécessaire de connaître précisément. Dire :
> Je comprends qu’il s’agit de votre direction de delivery / transformation SI.

---

# 🛠️ 3. Technologies Demandées – Définition + Cas d’Usage

---

# Java (8 à 25)

## Définition
Langage backend orienté objet très utilisé en entreprise.

## Cas d’usage

- APIs REST
- règles métier
- batchs
- sécurité
- services critiques

## Pourquoi en banque ?

- stabilité
- performance
- écosystème mature

---

# Spring Boot

## Définition
Framework Java pour créer rapidement des applications backend.

## Cas d’usage

- microservices
- APIs REST
- sécurité
- injection dépendances
- configuration simplifiée

---

# Spring Batch

## Définition
Framework Java dédié aux traitements batch.

## Cas d’usage

- traitements de masse
- imports
- reporting
- mises à jour KYC
- synchronisations nocturnes

---

# Angular (7 à 18)

## Définition
Framework frontend TypeScript de :contentReference[oaicite:6]{index=6}.

## Cas d’usage

- applications métier internes
- formulaires complexes
- dashboards
- interfaces riches

## Dans cette mission

Très utile pour :

- ouverture de compte
- KYC
- CRM
- back office

---

# TypeScript

## Définition
JavaScript typé.

## Cas d’usage

- code plus robuste
- maintenabilité
- gros projets front

---

# REST API

## Définition
Interface de communication entre applications via HTTP.

## Cas d’usage

- front Angular ↔ backend Java
- échanges inter-SI
- exposition de services

---

# SQL

## Définition
Langage de manipulation des bases de données.

## Cas d’usage

- lecture données client
- mises à jour
- reporting
- analyses incidents
- performance tuning

---

# DB2

## Définition
Base de données IBM souvent présente dans les grands SI historiques.

## Cas d’usage

- legacy bancaire
- gros volumes
- systèmes critiques

---

# Oracle

## Définition
SGBD entreprise très répandu.

## Cas d’usage

- applications critiques
- transactions
- sécurité
- performance

---

# PostgreSQL

## Définition
Base open-source puissante.

## Cas d’usage

- applications modernes
- APIs
- analytics
- nouveaux services

---

# GitLab

## Définition
Plateforme DevOps / Git.

## Cas d’usage

- versioning
- merge requests
- CI/CD
- pipelines

---

# Jira

## Définition
Outil de gestion Agile.

## Cas d’usage

- backlog
- tickets
- tests
- sprint tracking

---

# Teams

## Définition
Outil collaboration Microsoft.

## Cas d’usage

- réunions
- chat
- partage

---

# Outlook

## Définition
Messagerie Microsoft.

---

# DBeaver

## Définition
Client SQL multi-bases.

## Cas d’usage

- requêtes SQL
- analyse données
- debugging

---

# GWT

## Signification
**Google Web Toolkit**

## Définition
Ancienne techno front Java compilée en JavaScript.

## Cas d’usage

- anciennes applications internes

👉 Indice de legacy.

---

# Struts

## Définition
Ancien framework Java web MVC.

## Cas d’usage

- applications historiques

👉 Beaucoup de maintenance possible.

---

# Cobol

## Définition
Langage historique des systèmes bancaires.

## Cas d’usage

- mainframe
- traitements cœur bancaire

👉 Même si tu n’en fais pas, comprendre l’intégration est un plus.

---

# IA Générative

## Définition
Outils type :contentReference[oaicite:7]{index=7} ChatGPT, Copilot, etc.

## Cas d’usage dans la mission

- génération de code
- tests
- documentation
- refactoring
- analyse d’incidents
- SQL
- productivité

---

# 🎯 4. Ce qu’il faut retenir pour l’entretien

## Si on te demande ce que tu as compris :

> La mission porte sur des applications cœur relation client : ouverture de compte, conformité KYC, qualité de données client et outils back-office. Le contexte demande à la fois une forte rigueur technique, une compréhension métier bancaire et la capacité à accélérer la delivery avec des pratiques modernes comme l’IA générative.

---

# 💬 5. Mini phrase qui fait la différence

> J’ai bien compris que l’enjeu n’est pas seulement de développer, mais de sécuriser des parcours sensibles liés au client et à la conformité, tout en améliorant la vélocité de delivery.

---