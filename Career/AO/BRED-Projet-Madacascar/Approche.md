La mission ne consiste pas uniquement à « modifier du code avec une IA ». Il faut mettre en place une **chaîne de migration industrialisée, traçable et reproductible**, applicable à plusieurs dizaines d’applications.

Le contexte impose trois trajectoires possibles pour chaque application :

1. **Décommissionnement** ;
2. **Migration vers Redbox à Madagascar** ;
3. **Migration vers les datacenters de Rennes**.

La mission couvre aussi le cadrage, l’estimation, le développement, les tests, la correction des anomalies, la documentation et la communication avec les équipes françaises et malgaches. L’utilisation de l’IA est explicitement obligatoire pour accélérer les développements. 

# 1. Positionner correctement l’IA

L’IA doit être utilisée comme un **assistant de migration**, et non comme un décideur autonome.

Elle peut accélérer :

* l’exploration du code existant ;
* la génération de cartographies applicatives ;
* la détection de dépendances obsolètes ;
* la proposition de plans de migration ;
* la modernisation Java, Spring et Angular ;
* la génération de tests ;
* la génération de scripts ;
* la documentation ;
* l’analyse d’erreurs ;
* la préparation des revues de code.

Mais les décisions suivantes doivent rester humaines :

* choix de l’architecture cible ;
* traitement des données sensibles ;
* validation des règles métier ;
* décision de décommissionnement ;
* acceptation d’un risque ;
* validation d’une migration ;
* mise en production.

Le principe à appliquer est :

> **IA pour accélérer l’analyse et l’exécution, humain pour décider, vérifier et assumer la responsabilité.**

# 2. Première étape : clarifier le cadre d’utilisation de l’IA

Avant de transmettre le moindre code à un outil d’IA, il faudra obtenir des réponses formelles sur :

* les outils autorisés ;
* les modèles autorisés ;
* l’hébergement des modèles ;
* la conservation des prompts ;
* l’utilisation éventuelle des données pour l’entraînement ;
* les types de code pouvant être transmis ;
* les données interdites dans les prompts ;
* les règles concernant les secrets ;
* les obligations de journalisation ;
* les modalités de revue du code généré.

Dans une banque, il faut partir du principe que les éléments suivants ne doivent jamais être envoyés à un outil public sans autorisation explicite :

* données clients ;
* données personnelles ;
* secrets d’infrastructure ;
* mots de passe ;
* tokens ;
* certificats ;
* clés privées ;
* chaînes de connexion ;
* fichiers de production ;
* extraits de logs contenant des identifiants ;
* code propriétaire sensible.

## Fichier à créer

```text
docs/ai/AI-USAGE-POLICY.md
```

Contenu recommandé :

```markdown
# Politique d’utilisation de l’IA

## Outils autorisés
## Outils interdits
## Types de données autorisées
## Types de données interdites
## Règles d’anonymisation
## Règles de conservation des prompts
## Validation humaine obligatoire
## Traçabilité du code généré
## Processus en cas d’incident
## Contacts sécurité et conformité
```

# 3. Mettre en place une chaîne de migration par application

Chaque application doit passer par le même pipeline.

```text
Inventaire
   ↓
Qualification
   ↓
Décision de trajectoire
   ↓
Analyse de l’existant
   ↓
Conception de la cible
   ↓
Plan de migration
   ↓
Modification du code
   ↓
Tests
   ↓
Migration technique
   ↓
Recette
   ↓
Mise en production
   ↓
Stabilisation ou décommissionnement
```

Il ne faut pas commencer par demander à l’IA :

> « Migre cette application. »

Il faut lui fournir un contexte progressif et lui demander des productions contrôlables.

# 4. Phase 1 — Inventaire et qualification

Pour chaque application, récupérer au minimum :

* nom et finalité ;
* propriétaire métier ;
* référent technique ;
* utilisateurs ;
* criticité ;
* disponibilité attendue ;
* technologies ;
* versions Java, Spring et Angular ;
* base de données ;
* systèmes externes ;
* flux entrants et sortants ;
* traitements batch ;
* ordonnanceurs ;
* volumes ;
* exigences de performance ;
* exigences de sécurité ;
* environnements ;
* méthode de déploiement ;
* supervision ;
* sauvegardes ;
* documentation existante ;
* état des tests ;
* obsolescences connues.

## Décision attendue

Chaque application doit être classée :

```text
DECOMMISSION
MIGRATE_REDBOX
MIGRATE_RENNES
MODERNIZE_THEN_MIGRATE
ASSESSMENT_REQUIRED
```

## Utilisation de l’IA

L’IA peut :

* synthétiser la documentation existante ;
* analyser les fichiers Maven, Gradle, npm et Angular ;
* identifier les dépendances ;
* générer une première fiche applicative ;
* identifier les informations manquantes ;
* proposer une liste de questions ;
* signaler les composants obsolètes.

## Fichiers à créer

```text
docs/inventory/APPLICATION-INVENTORY.xlsx
docs/inventory/<application>/APPLICATION-FICHE.md
docs/inventory/<application>/DEPENDENCY-INVENTORY.md
docs/inventory/<application>/INTERFACE-INVENTORY.md
docs/inventory/<application>/DATA-INVENTORY.md
docs/inventory/<application>/OPEN-QUESTIONS.md
```

Exemple de fiche :

```markdown
# Fiche application

## Identification
- Nom :
- Code application :
- Responsable métier :
- Responsable technique :

## Finalité métier

## Criticité

## Utilisateurs

## Stack actuelle
- Java :
- Spring :
- Angular :
- Base de données :
- Serveur :
- OS :

## Interfaces
- APIs :
- MQ :
- Fichiers :
- Batchs :

## Données sensibles

## Déploiement actuel

## Supervision actuelle

## Tests existants

## Documentation disponible

## Trajectoire proposée

## Informations manquantes
```

# 5. Phase 2 — Analyse automatisée de l’existant

Il faut construire un diagnostic reproductible plutôt qu’une analyse manuelle différente pour chaque application.

## Contrôles à automatiser

### Java et Spring

* version du JDK ;
* version de Spring ou Spring Boot ;
* dépendances obsolètes ;
* dépendances vulnérables ;
* usages d’API supprimées ;
* bibliothèques propriétaires ;
* dépendances au système d’exploitation ;
* chemins absolus ;
* accès au système de fichiers ;
* configuration embarquée dans le code ;
* accès directs aux bases ;
* transactions ;
* batchs ;
* threads manuels ;
* appels réseau ;
* dépendances aux serveurs d’application.

### Angular

* version Angular ;
* version TypeScript ;
* dépendances npm ;
* modules dépréciés ;
* composants fortement couplés ;
* appels HTTP ;
* URLs codées en dur ;
* configuration d’environnement ;
* compatibilité des bibliothèques ;
* couverture de tests ;
* failles npm.

### Infrastructure

* scripts de démarrage ;
* variables d’environnement ;
* ports ;
* certificats ;
* stockage local ;
* partages réseau ;
* DNS ;
* proxy ;
* ordonnanceur ;
* supervision ;
* logs ;
* sauvegardes ;
* dépendances spécifiques à ITIAB.

## Outils possibles

Selon les outils autorisés :

* Maven Dependency Plugin ;
* Maven Enforcer ;
* OpenRewrite ;
* SonarQube ;
* OWASP Dependency-Check ;
* Snyk ou équivalent interne ;
* Trivy ;
* npm audit ;
* ESLint ;
* Semgrep ;
* scripts Shell ou Python ;
* outils de Software Composition Analysis de la banque.

## Fichiers générés

```text
reports/<application>/dependency-report.json
reports/<application>/vulnerability-report.json
reports/<application>/static-analysis-report.json
reports/<application>/test-coverage-report.xml
reports/<application>/obsolete-api-report.md
reports/<application>/configuration-report.md
reports/<application>/migration-readiness-report.md
```

# 6. Phase 3 — Établir un score de migrabilité

Pour prioriser plusieurs dizaines d’applications, je recommande un score commun.

## Critères

Noter chaque critère de 1 à 5 :

* criticité métier ;
* obsolescence technique ;
* complexité du code ;
* nombre d’interfaces ;
* qualité des tests ;
* qualité de la documentation ;
* sensibilité des données ;
* dépendance à l’infrastructure locale ;
* complexité de la base ;
* volumétrie ;
* contraintes de disponibilité ;
* effort estimé ;
* niveau d’incertitude.

Exemple :

```text
Migration Readiness Score =

Documentation
+ Couverture de tests
+ Portabilité
+ Maîtrise des dépendances
+ Automatisation du déploiement
- Obsolescence
- Couplage à l’infrastructure
- Incertitudes
```

L’IA peut préparer le score à partir des éléments collectés, mais chaque note doit être justifiée et validée par l’équipe.

## Fichier

```text
docs/inventory/<application>/MIGRATION-SCORING.md
```

# 7. Phase 4 — Définir l’architecture cible

Avant de modifier le code, établir une comparaison explicite entre l’existant et la cible.

## Documents à produire

### Architecture actuelle

```text
docs/architecture/AS-IS-ARCHITECTURE.md
```

Doit contenir :

* composants ;
* flux ;
* interfaces ;
* bases ;
* batchs ;
* serveurs ;
* contraintes ;
* points de défaillance ;
* dépendances ITIAB.

### Architecture cible

```text
docs/architecture/TO-BE-ARCHITECTURE.md
```

Doit préciser :

* emplacement cible ;
* runtimes ;
* réseau ;
* stockage ;
* base de données ;
* authentification ;
* secrets ;
* logs ;
* supervision ;
* haute disponibilité ;
* sauvegardes ;
* reprise ;
* CI/CD.

### Écart entre les deux

```text
docs/architecture/GAP-ANALYSIS.md
```

Exemple de tableau :

| Domaine       | Existant        | Cible                | Écart  | Action                |
| ------------- | --------------- | -------------------- | ------ | --------------------- |
| Java          | Java 8          | Java 21              | Majeur | Migration progressive |
| Configuration | Fichier local   | Variables sécurisées | Moyen  | Externaliser          |
| Logs          | Fichiers locaux | Centralisation       | Moyen  | Adapter Logback       |
| Stockage      | Disque ITIAB    | Stockage cible       | Majeur | Adapter le composant  |
| Déploiement   | Manuel          | Pipeline CI/CD       | Majeur | Créer pipeline        |

# 8. Phase 5 — Produire un plan de migration versionné

Le plan doit être relu avant toute modification importante.

## Fichier central

```text
MIGRATION-PLAN.md
```

Structure :

```markdown
# Plan de migration

## 1. Objectif

## 2. Périmètre

## 3. Hors périmètre

## 4. Architecture actuelle

## 5. Architecture cible

## 6. Prérequis

## 7. Hypothèses

## 8. Risques

## 9. Étapes techniques

## 10. Changements Java

## 11. Changements Angular

## 12. Changements base de données

## 13. Changements infrastructure

## 14. Stratégie de tests

## 15. Stratégie de déploiement

## 16. Stratégie de rollback

## 17. Critères d’acceptation

## 18. Estimation

## 19. Responsabilités

## 20. Points ouverts
```

Chaque étape doit correspondre à un ticket Jira identifiable.

# 9. Phase 6 — Encadrer les travaux réalisés avec l’IA

Il faut donner à l’IA des règles propres au projet.

## Fichier `AGENTS.md`

À placer à la racine du dépôt :

```markdown
# AGENTS.md

## Contexte du projet

## Objectif de la migration

## Architecture actuelle

## Architecture cible

## Stack autorisée

## Versions obligatoires

## Commandes de build

## Commandes de test

## Conventions Java

## Conventions Angular

## Règles de sécurité

## Données interdites dans les prompts

## Contraintes de compatibilité

## Fichiers à ne jamais modifier

## Processus de migration

## Definition of Done

## Format des comptes rendus attendus
```

Ce fichier évite de répéter le contexte à chaque session et réduit les décisions incohérentes.

## Autres fichiers de règles

```text
ARCHITECTURE.md
SECURITY.md
CODING-STANDARDS.md
TESTING-STRATEGY.md
CONTRIBUTING.md
REVIEW-CHECKLIST.md
```

Pour un dépôt Java/Angular :

```text
docs/standards/JAVA-STANDARDS.md
docs/standards/ANGULAR-STANDARDS.md
docs/standards/API-STANDARDS.md
docs/standards/DATABASE-STANDARDS.md
docs/standards/LOGGING-STANDARDS.md
docs/standards/ERROR-HANDLING-STANDARDS.md
```

# 10. Prompts de travail à utiliser

Éviter les demandes vagues. Utiliser des prompts structurés.

## Prompt d’analyse initiale

```text
Analyse cette application sans modifier le code.

Objectifs :
1. Identifier son architecture.
2. Identifier ses points d’entrée.
3. Lister ses dépendances externes.
4. Identifier ses flux entrants et sortants.
5. Identifier ses dépendances à l’infrastructure actuelle.
6. Identifier les risques de migration.
7. Identifier les informations manquantes.

Contraintes :
- Ne fais aucune hypothèse silencieuse.
- Distingue les faits observés des suppositions.
- Cite les fichiers et les lignes concernés.
- Ne propose pas encore de refactoring.

Produis :
- une synthèse ;
- une cartographie ;
- une liste de risques ;
- une liste de questions ;
- un rapport Markdown.
```

## Prompt de planification

```text
À partir de AS-IS-ARCHITECTURE.md, TO-BE-ARCHITECTURE.md et
GAP-ANALYSIS.md, propose un plan de migration incrémental.

Pour chaque étape, indique :
- objectif ;
- fichiers concernés ;
- prérequis ;
- risque ;
- tests à exécuter ;
- critère d’acceptation ;
- procédure de rollback.

Ne génère aucun code.
```

## Prompt d’implémentation

```text
Implémente uniquement l’étape MIG-04 du plan de migration.

Contraintes :
- ne modifier que les fichiers nécessaires ;
- préserver le comportement métier ;
- ne pas changer les contrats API sans autorisation ;
- ajouter ou adapter les tests ;
- ne pas introduire de nouvelle dépendance sans justification ;
- respecter SECURITY.md et CODING-STANDARDS.md.

À la fin, fournis :
- les fichiers modifiés ;
- les décisions prises ;
- les tests exécutés ;
- les résultats ;
- les risques résiduels ;
- les éléments non vérifiés.
```

## Prompt de revue

```text
Effectue une revue critique de cette modification.

Recherche notamment :
- régressions fonctionnelles ;
- erreurs de concurrence ;
- problèmes transactionnels ;
- failles de sécurité ;
- erreurs de gestion des exceptions ;
- problèmes de performance ;
- rupture de compatibilité ;
- tests manquants ;
- configuration spécifique à un environnement.

Classe les observations en :
- bloquant ;
- majeur ;
- mineur ;
- suggestion.

Ne corrige rien avant validation.
```

# 11. Processus Git recommandé

Ne jamais laisser l’IA produire directement une grosse migration sur la branche principale.

## Organisation

```text
main
develop
migration/<application>/<ticket>
fix/<application>/<ticket>
```

Exemple :

```text
migration/compta/MIG-142-java-21
migration/compta/MIG-156-externalize-config
migration/compta/MIG-163-database-driver
```

## Taille des changements

Une Pull Request doit idéalement correspondre à une seule intention :

* mise à jour Java ;
* mise à jour Spring ;
* migration Angular ;
* externalisation de configuration ;
* adaptation d’une interface ;
* ajout de tests ;
* modification du déploiement.

Éviter une Pull Request unique contenant :

* migration Java ;
* refonte métier ;
* changement de base ;
* mise à jour Angular ;
* réécriture de l’architecture.

Cela rendrait les régressions et les rollbacks difficiles.

# 12. Processus de validation du code généré

Tout code produit ou modifié avec une IA doit passer par les mêmes contrôles qu’un code écrit manuellement, voire davantage.

## Pipeline minimum

```text
Compilation
→ Analyse statique
→ Tests unitaires
→ Tests d’intégration
→ Scan des dépendances
→ Scan de sécurité
→ Vérification des secrets
→ Revue humaine
→ Déploiement en environnement technique
→ Tests de non-régression
```

## Règle des deux validations

Pour les composants critiques :

1. validation par les tests automatisés ;
2. validation par un développeur ou référent autre que l’auteur.

## Fichier de revue

```text
REVIEW-CHECKLIST.md
```

Contenu :

```markdown
## Fonctionnel
- [ ] Le comportement métier est préservé
- [ ] Les contrats d’interface sont préservés
- [ ] Les cas limites sont testés

## Technique
- [ ] Le projet compile
- [ ] Les tests passent
- [ ] Les dépendances sont justifiées
- [ ] Aucun chemin local n’est codé en dur
- [ ] La configuration est externalisée

## Sécurité
- [ ] Aucun secret n’est présent
- [ ] Les entrées sont validées
- [ ] Les logs ne contiennent pas de données sensibles
- [ ] Les dépendances ont été analysées

## Migration
- [ ] Le rollback est documenté
- [ ] La compatibilité avec la cible est vérifiée
- [ ] Les scripts sont rejouables
- [ ] Les contrôles post-déploiement sont définis

## IA
- [ ] Le code généré a été relu
- [ ] Les hypothèses de l’IA ont été vérifiées
- [ ] Les tests ne reproduisent pas aveuglément l’implémentation
```

# 13. Stratégie de tests pour une migration

L’objectif principal est de démontrer que le comportement n’a pas changé involontairement.

## Avant migration

Créer un filet de sécurité :

* tests de caractérisation ;
* tests des contrats API ;
* tests des échanges MQ ;
* tests des batchs ;
* tests SQL ;
* jeux de données anonymisés ;
* mesure des performances actuelles ;
* captures des résultats de référence.

Les tests de caractérisation sont particulièrement importants sur une application ancienne peu documentée : ils figent le comportement existant, même si le code n’est pas idéal.

## Pendant la migration

* tests unitaires ;
* tests d’intégration ;
* tests de compatibilité ;
* tests de migration de données ;
* tests des flux ;
* tests d’idempotence ;
* tests d’erreur ;
* tests de reprise.

## Après migration

* comparaison avant/après ;
* smoke tests ;
* tests de non-régression ;
* tests de charge ;
* tests de résilience ;
* tests de supervision ;
* tests de sauvegarde et restauration ;
* tests de rollback.

## Fichiers

```text
docs/testing/TESTING-STRATEGY.md
docs/testing/TEST-CASES.md
docs/testing/TEST-DATA-STRATEGY.md
docs/testing/NON-REGRESSION-MATRIX.md
docs/testing/PERFORMANCE-BASELINE.md
docs/testing/TEST-REPORT.md
```

# 14. Gérer les migrations de données

Pour toute application avec base de données :

* inventorier les données ;
* identifier les données sensibles ;
* définir les règles de transformation ;
* analyser la volumétrie ;
* mesurer la durée de migration ;
* rendre les scripts rejouables ;
* conserver les traces ;
* vérifier les comptages ;
* définir la réconciliation ;
* prévoir le rollback.

## Fichiers

```text
docs/data/DATA-MIGRATION-PLAN.md
docs/data/DATA-MAPPING.md
docs/data/DATA-VALIDATION-RULES.md
docs/data/DATA-RECONCILIATION.md
docs/data/DATA-ROLLBACK-PLAN.md
```

Exemples de contrôles :

* nombre de lignes source et cible ;
* sommes de montants ;
* détection des doublons ;
* clés étrangères orphelines ;
* valeurs nulles inattendues ;
* encodage ;
* fuseaux horaires ;
* précision des montants ;
* dates et formats.

L’IA peut générer des requêtes de contrôle, mais les règles de réconciliation doivent être validées avec les responsables métier.

# 15. Prévoir le déploiement et le rollback dès le départ

Une migration n’est pas terminée lorsque le code compile.

Il faut définir :

* procédure de déploiement ;
* fenêtre de migration ;
* prérequis ;
* sauvegarde ;
* arrêt des flux ;
* ordre de démarrage ;
* contrôles techniques ;
* contrôles métier ;
* critères d’arrêt ;
* procédure de retour arrière ;
* personnes responsables ;
* canal de communication.

## Fichiers

```text
docs/runbooks/DEPLOYMENT-RUNBOOK.md
docs/runbooks/ROLLBACK-RUNBOOK.md
docs/runbooks/POST-MIGRATION-CHECKLIST.md
docs/runbooks/INCIDENT-RUNBOOK.md
docs/runbooks/OPERATIONS-RUNBOOK.md
```

# 16. Définir une Definition of Done spécifique aux migrations

Une application ne doit pas être considérée comme migrée uniquement parce qu’elle démarre sur la cible.

## Exemple

```markdown
# Definition of Done — Migration

- [ ] Architecture cible validée
- [ ] Code livré et revu
- [ ] Build reproductible
- [ ] Tests unitaires réussis
- [ ] Tests d’intégration réussis
- [ ] Tests de non-régression réussis
- [ ] Tests de performance acceptés
- [ ] Vulnérabilités bloquantes corrigées
- [ ] Configuration externalisée
- [ ] Secrets gérés par le mécanisme cible
- [ ] Logs centralisés
- [ ] Monitoring opérationnel
- [ ] Alertes vérifiées
- [ ] Sauvegarde vérifiée
- [ ] Rollback testé ou simulé
- [ ] Documentation mise à jour
- [ ] Équipe Madagascar informée
- [ ] Recette métier validée
- [ ] Exploitation formée
- [ ] Ancienne infrastructure arrêtée ou planifiée
```

# 17. Tracer concrètement l’utilisation de l’IA

Puisque l’usage de l’IA est obligatoire, il est utile de démontrer son apport sans enregistrer des informations sensibles.

## Registre conseillé

```text
docs/ai/AI-USAGE-LOG.md
```

Exemple :

| Date  | Ticket  | Usage               | Outil         | Résultat              | Validation humaine |
| ----- | ------- | ------------------- | ------------- | --------------------- | ------------------ |
| 05/08 | MIG-142 | Analyse dépendances | Outil interne | 12 risques identifiés | Validé             |
| 06/08 | MIG-142 | Génération tests    | Outil interne | 8 tests ajoutés       | Corrigé et validé  |
| 07/08 | MIG-156 | Documentation       | Outil interne | Diagramme initial     | Validé             |

Il ne faut pas nécessairement conserver les prompts complets s’ils contiennent du code sensible. Le registre peut ne conserver que :

* le type d’usage ;
* le ticket ;
* l’outil ;
* le résultat ;
* la validation effectuée ;
* les corrections humaines.

## Mesures d’efficacité

Suivre quelques indicateurs :

* temps d’analyse avant/après IA ;
* temps de génération des tests ;
* couverture gagnée ;
* nombre de risques détectés ;
* taux de suggestions IA rejetées ;
* défauts découverts en revue ;
* défauts découverts en recette ;
* temps de rédaction documentaire ;
* délai moyen de résolution des anomalies.

Ces indicateurs permettront de montrer une utilisation réelle et maîtrisée de l’IA, et pas uniquement déclarative.

# 18. Organisation Jira recommandée

Créer une Epic par application ou par vague de migration.

```text
EPIC : Migration Application X
```

Stories ou tâches :

```text
MIG-001 — Inventaire de l’application
MIG-002 — Cartographie des flux
MIG-003 — Analyse de sécurité
MIG-004 — Architecture cible
MIG-005 — Tests de caractérisation
MIG-006 — Mise à jour Java
MIG-007 — Mise à jour Spring
MIG-008 — Adaptation Angular
MIG-009 — Migration base de données
MIG-010 — Pipeline CI/CD
MIG-011 — Déploiement environnement technique
MIG-012 — Tests de non-régression
MIG-013 — Préparation du rollback
MIG-014 — Recette
MIG-015 — Mise en production
MIG-016 — Stabilisation
MIG-017 — Décommissionnement de l’existant
```

Chaque ticket doit contenir :

* contexte ;
* objectif ;
* périmètre ;
* hors périmètre ;
* dépendances ;
* critères d’acceptation ;
* tests ;
* documentation attendue ;
* procédure de rollback si nécessaire.

# 19. Arborescence documentaire recommandée

Voici une structure complète réutilisable pour chaque application :

```text
application-x/
├── AGENTS.md
├── README.md
├── ARCHITECTURE.md
├── SECURITY.md
├── CONTRIBUTING.md
├── CODING-STANDARDS.md
├── MIGRATION-PLAN.md
├── REVIEW-CHECKLIST.md
├── CHANGELOG.md
│
├── docs/
│   ├── inventory/
│   │   ├── APPLICATION-FICHE.md
│   │   ├── DEPENDENCY-INVENTORY.md
│   │   ├── INTERFACE-INVENTORY.md
│   │   ├── DATA-INVENTORY.md
│   │   ├── MIGRATION-SCORING.md
│   │   └── OPEN-QUESTIONS.md
│   │
│   ├── architecture/
│   │   ├── AS-IS-ARCHITECTURE.md
│   │   ├── TO-BE-ARCHITECTURE.md
│   │   ├── GAP-ANALYSIS.md
│   │   ├── DATA-FLOW.md
│   │   └── ADR/
│   │       ├── ADR-001-target-platform.md
│   │       └── ADR-002-migration-strategy.md
│   │
│   ├── ai/
│   │   ├── AI-USAGE-POLICY.md
│   │   ├── AI-USAGE-LOG.md
│   │   └── PROMPT-LIBRARY.md
│   │
│   ├── security/
│   │   ├── THREAT-MODEL.md
│   │   ├── SECURITY-CHECKLIST.md
│   │   └── VULNERABILITY-MANAGEMENT.md
│   │
│   ├── testing/
│   │   ├── TESTING-STRATEGY.md
│   │   ├── TEST-CASES.md
│   │   ├── NON-REGRESSION-MATRIX.md
│   │   ├── PERFORMANCE-BASELINE.md
│   │   └── TEST-REPORT.md
│   │
│   ├── data/
│   │   ├── DATA-MIGRATION-PLAN.md
│   │   ├── DATA-MAPPING.md
│   │   ├── DATA-VALIDATION-RULES.md
│   │   └── DATA-ROLLBACK-PLAN.md
│   │
│   └── runbooks/
│       ├── DEPLOYMENT-RUNBOOK.md
│       ├── ROLLBACK-RUNBOOK.md
│       ├── OPERATIONS-RUNBOOK.md
│       ├── INCIDENT-RUNBOOK.md
│       └── POST-MIGRATION-CHECKLIST.md
│
└── reports/
    ├── dependencies/
    ├── security/
    ├── tests/
    ├── performance/
    └── migration/
```

# 20. Processus conseillé pendant vos premières semaines

## Semaine 1 : comprendre et sécuriser

* obtenir la politique IA ;
* comprendre Redbox et Rennes ;
* identifier les interlocuteurs ;
* récupérer les standards internes ;
* comprendre Jira et les workflows ;
* inventorier les applications ;
* sélectionner une application pilote.

## Semaine 2 : analyser l’application pilote

* construire la fiche applicative ;
* produire l’architecture AS-IS ;
* analyser les dépendances ;
* identifier les flux ;
* évaluer les risques ;
* générer les tests de caractérisation manquants ;
* établir un premier score de migrabilité.

## Semaine 3 : concevoir la migration

* produire l’architecture TO-BE ;
* réaliser la GAP analysis ;
* rédiger les ADR ;
* construire le plan de migration ;
* découper le travail dans Jira ;
* valider les critères d’acceptation ;
* valider le rollback.

## Semaine 4 : réaliser une première migration contrôlée

* choisir une modification limitée ;
* utiliser l’IA pour analyser et préparer le changement ;
* générer ou compléter les tests ;
* réaliser une petite Pull Request ;
* mesurer le gain ;
* documenter les erreurs ;
* améliorer le processus.

Cette application pilote doit permettre de créer un **kit de migration réutilisable** pour les applications suivantes.

# 21. Les erreurs à éviter

Évitez particulièrement :

* envoyer tout le dépôt à un outil non autorisé ;
* accepter une analyse IA sans vérifier les fichiers concernés ;
* mélanger modernisation fonctionnelle et migration technique ;
* changer plusieurs composants majeurs simultanément ;
* générer des tests qui ne font que recopier l’implémentation ;
* laisser l’IA inventer les règles métier ;
* commencer sans baseline de tests ;
* migrer sans procédure de rollback ;
* documenter uniquement à la fin ;
* conserver des configurations spécifiques aux environnements ;
* intégrer une dépendance proposée par l’IA sans validation ;
* présenter un code comme « validé » uniquement parce qu’il compile.

# Recommandation centrale

Votre meilleure approche serait de proposer rapidement au responsable du projet un **Migration Factory Kit** commun :

```text
1. Un modèle de fiche applicative
2. Un script d’analyse technique
3. Un modèle AS-IS / TO-BE
4. Un modèle de plan de migration
5. Une stratégie de tests
6. Un modèle de runbook
7. Un AGENTS.md standard
8. Une bibliothèque de prompts approuvés
9. Une checklist de revue IA
10. Un tableau de bord Jira des migrations
```

Cela vous positionnera non seulement comme développeur Java/Angular, mais comme une personne capable d’**industrialiser les migrations**, de sécuriser l’usage de l’IA et de traiter les applications de manière cohérente de bout en bout.
