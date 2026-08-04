Sur un projet de développement logiciel, le **PRD (Product Requirements Document)** n’est qu’un des documents de référence. Selon la taille du projet et l’organisation de l’équipe, il est généralement utile de rédiger et stocker plusieurs autres documents pour éviter les ambiguïtés entre produit, design, développement, QA et exploitation.

Voici une organisation courante :

| Document                                                        | Objectif                                                                                           | Qui le rédige ?                 | Où le stocker ?                                         |
| --------------------------------------------------------------- | -------------------------------------------------------------------------------------------------- | ------------------------------- | ------------------------------------------------------- |
| **PRD (Product Requirements Document)**                         | Décrit le besoin utilisateur, les objectifs, les fonctionnalités attendues, les critères de succès | Product Manager / Product Owner | Notion, Confluence, Google Docs, Jira Product Discovery |
| **BRD (Business Requirements Document)**                        | Explique le contexte business, les enjeux, ROI, contraintes métier                                 | Métier + Product                | Confluence, Notion, SharePoint                          |
| **User Stories + Acceptance Criteria**                          | Découpe le besoin en tâches développables et testables                                             | Product Owner avec l’équipe     | Jira, Linear, Azure DevOps                              |
| **Spécifications fonctionnelles (Functional Spec)**             | Décrit précisément les comportements attendus (écrans, règles métier, cas limites)                 | Product + UX + Tech Lead        | Confluence, Notion                                      |
| **Spécifications techniques / Technical Design Document (TDD)** | Explique l’architecture, les choix techniques, les API, la base de données, les dépendances        | Tech Lead / Architecte          | Confluence, Git repository (`/docs`), Notion            |
| **Architecture Decision Records (ADR)**                         | Garde la trace des décisions techniques importantes et de leurs raisons                            | Développeurs / Architectes      | Dans le dépôt Git (`/docs/adr`)                         |
| **Design System / UI Specifications**                           | Définit les composants UI, règles graphiques, prototypes                                           | Designer UX/UI                  | Figma + documentation associée                          |
| **API Documentation**                                           | Décrit les endpoints, paramètres, réponses, erreurs                                                | Développeurs                    | Swagger/OpenAPI, Postman, Git                           |
| **Data Model / Database Schema**                                | Décrit les tables, relations, règles de données                                                    | Backend / Data Engineer         | Git, outil de modélisation, Confluence                  |
| **Test Plan + Test Cases**                                      | Définit la stratégie de test et les scénarios                                                      | QA + Product                    | TestRail, Xray Jira, Zephyr, Confluence                 |
| **Release Notes / Changelog**                                   | Historique des versions livrées                                                                    | Dev + Product                   | GitHub/GitLab Releases, Confluence                      |
| **Runbook / Documentation d’exploitation**                      | Explique le déploiement, monitoring, incidents, maintenance                                        | DevOps / SRE                    | Git, Confluence                                         |
| **Security Documentation**                                      | Menaces, règles sécurité, conformité, accès                                                        | Security + Tech                 | Confluence, Git privé                                   |

---

## Une structure simple et efficace

Un bon compromis pour une équipe moderne :

```
Projet/
│
├── Product/
│   ├── PRD.md
│   ├── User-Stories.md
│   └── Roadmap.md
│
├── Design/
│   ├── Figma links
│   └── UX Specifications
│
├── Technical/
│   ├── Architecture.md
│   ├── Database.md
│   ├── API.md
│   └── ADR/
│       ├── ADR-001-database-choice.md
│       └── ADR-002-authentication.md
│
├── QA/
│   ├── Test-Plan.md
│   └── Test-Cases.md
│
└── Operations/
    ├── Deployment.md
    └── Monitoring.md
```

---

## Où rédiger ?

### Option 1 — Petite équipe / startup

**Notion + GitHub/GitLab**

* Notion :

  * PRD
  * roadmap
  * specs fonctionnelles
  * décisions produit
  * comptes rendus

* GitHub/GitLab :

  * documentation technique
  * architecture
  * ADR
  * API
  * scripts d’installation

Avantage : simple et rapide.

---

### Option 2 — Équipe professionnelle / entreprise

**Confluence + Jira + GitLab/GitHub + Figma**

* Atlassian :

  * Jira → tickets, backlog, sprint
  * Confluence → documentation projet

* [GitHub](https://github.com?utm_source=chatgpt.com) ou [GitLab](https://about.gitlab.com?utm_source=chatgpt.com) :

  * code
  * documentation technique versionnée

* [Figma](https://www.figma.com?utm_source=chatgpt.com) :

  * maquettes et design system

---

## Où stocker la "source de vérité" ?

Une règle utile :

* **Ce qui change souvent et concerne le produit → outil documentaire**

  * PRD, roadmap, décisions produit → Notion/Confluence

* **Ce qui concerne le code ou l’architecture → dépôt Git**

  * architecture, API, ADR → Git

* **Ce qui concerne l’exécution → outil spécialisé**

  * tickets → Jira
  * tests → outil QA
  * design → Figma

Évitez d’avoir le même document à trois endroits différents. Chaque information importante devrait avoir **un emplacement officiel unique**.

---

Pour un projet de développement classique, le minimum viable de documentation serait :

1. PRD
2. User Stories + critères d’acceptation
3. Spécifications fonctionnelles
4. Architecture technique (TDD)
5. ADR pour les décisions importantes
6. Documentation API
7. Plan de tests
8. Guide de déploiement

C’est généralement suffisant pour permettre à une nouvelle personne de comprendre le projet sans dépendre uniquement de l’oral.
