Voici une architecture de référence **Enterprise** pour une stack **Java Spring Boot 4 + Angular 21** avec une **chaîne DevSecOps / CI-CD complète** intégrant toutes les technologies demandées.

## Architecture globale

```mermaid
flowchart LR

    %% Developers
    DEV[Développeur]

    %% SCM
    GIT[GitLab Repository]

    %% CI
    CI[GitLab CI/CD Pipeline]

    %% Backend
    MAVEN[Maven]
    JUNIT[JUnit 5]
    MOCKITO[Mockito]

    %% Frontend
    NPM[NPM]
    ANGULAR[Angular 21]
    JEST[Jest]
    KARMA[Karma]

    %% Quality
    SONAR[SonarQube]
    SNYK[Snyk]
    TRIVY[Trivy]

    %% Packaging
    DOCKER[Docker Build]
    REGISTRY[GitLab Container Registry]
    ARTIFACTORY[JFrog Artifactory]

    %% Deployment
    K8S[Kubernetes Cluster]

    %% Runtime
    SPRING[Spring Boot 4 API]
    FRONT[Angular Web App]

    %% Tests
    SELENIUM[Selenium E2E]

    %% Monitoring
    PROM[Prometheus]
    GRAF[Grafana]

    DEV -->|Commit / Merge Request| GIT
    GIT --> CI

    CI --> MAVEN
    MAVEN --> JUNIT
    MAVEN --> MOCKITO

    CI --> NPM
    NPM --> ANGULAR
    ANGULAR --> JEST
    ANGULAR --> KARMA

    MAVEN --> SONAR
    ANGULAR --> SONAR

    SONAR --> SNYK
    SNYK --> TRIVY

    TRIVY --> DOCKER

    DOCKER --> REGISTRY
    MAVEN --> ARTIFACTORY

    REGISTRY --> K8S
    ARTIFACTORY --> K8S

    K8S --> SPRING
    K8S --> FRONT

    SPRING --> SELENIUM
    FRONT --> SELENIUM

    SPRING --> PROM
    PROM --> GRAF
```

---

# Pipeline CI/CD complet

```mermaid
flowchart TD

A[Developer Push] --> B[GitLab]

B --> C[GitLab Runner]

C --> D1[Build Backend Maven]
C --> D2[Build Frontend NPM]

D1 --> E1[JUnit5]
E1 --> E2[Mockito]

D2 --> F1[Jest]
F1 --> F2[Karma]

E2 --> G[SonarQube Analysis]
F2 --> G

G --> H[Snyk Scan]

H --> I[Build Docker Image]

I --> J[Trivy Image Scan]

J --> K{Scan OK ?}

K -->|No| STOP[Pipeline Failed]

K -->|Yes| L[Push Docker Image]

L --> M[GitLab Registry]

D1 --> N[Publish JAR]
N --> O[JFrog Artifactory]

M --> P[Deploy Kubernetes]

P --> Q[Smoke Tests]

Q --> R[Selenium E2E]

R --> S{Tests OK ?}

S -->|No| FAIL[Rollback]

S -->|Yes| PROD[Production]

PROD --> T[Prometheus]

T --> U[Grafana Dashboards]
```

---

# Architecture Kubernetes

```mermaid
flowchart TB

subgraph Kubernetes Cluster

Ingress[Ingress Controller]

subgraph Frontend
AngularPod[Angular Pod]
end

subgraph Backend
SpringPod1[SpringBoot Pod]
SpringPod2[SpringBoot Pod]
SpringPod3[SpringBoot Pod]
end

ServiceAPI[Spring Service]

Prometheus[Prometheus]
Grafana[Grafana]

Ingress --> AngularPod

AngularPod --> ServiceAPI

ServiceAPI --> SpringPod1
ServiceAPI --> SpringPod2
ServiceAPI --> SpringPod3

SpringPod1 --> Prometheus
SpringPod2 --> Prometheus
SpringPod3 --> Prometheus

Prometheus --> Grafana

end
```

---

# Chaîne DevSecOps complète

```text
Developer
      │
      ▼
GitLab Repository
      │
      ▼
GitLab CI/CD
      │
      ├─────────────── Build Backend (Maven)
      │                    │
      │                    ├── Compilation
      │                    ├── JUnit5
      │                    ├── Mockito
      │                    └── Packaging JAR
      │
      ├─────────────── Build Frontend (NPM)
      │                    │
      │                    ├── npm install
      │                    ├── Angular Build
      │                    ├── Jest
      │                    └── Karma
      │
      ▼
SonarQube
      │
      ▼
Snyk
      │
      ▼
Docker Build
      │
      ▼
Trivy Scan
      │
      ▼
GitLab Container Registry
      │
      ├──────────────► Kubernetes
      │
      ▼
JFrog Artifactory
      │
      ▼
Release Management
      │
      ▼
Deployment Kubernetes
      │
      ▼
Selenium E2E
      │
      ▼
Production
      │
      ▼
Spring Boot Actuator (/actuator/prometheus)
      │
      ▼
Prometheus
      │
      ▼
Grafana
```

---

# Ordonnancement idéal du pipeline GitLab

| Stage                | Outils               | Objectif                         |
| -------------------- | -------------------- | -------------------------------- |
| 1. Checkout          | GitLab               | Récupération du code             |
| 2. Backend Build     | Maven                | Compilation Spring Boot          |
| 3. Backend Tests     | JUnit5 + Mockito     | Tests unitaires                  |
| 4. Front Build       | NPM + Angular CLI    | Compilation Angular              |
| 5. Front Tests       | Jest + Karma         | Tests unitaires Front            |
| 6. Quality Gate      | SonarQube            | Qualité, couverture, duplication |
| 7. Security Scan     | Snyk                 | Vulnérabilités des dépendances   |
| 8. Docker Build      | Docker               | Création de l'image              |
| 9. Image Scan        | Trivy                | Scan des images Docker           |
| 10. Publish          | GitLab Registry      | Publication de l'image           |
| 11. Publish Artifact | JFrog Artifactory    | Archivage des JAR                |
| 12. Deploy           | Kubernetes           | Déploiement                      |
| 13. E2E              | Selenium             | Validation fonctionnelle         |
| 14. Monitoring       | Prometheus + Grafana | Supervision                      |

---

## Flux de bout en bout

```text
Développeur
      │
      ▼
 GitLab
      │
      ▼
 GitLab CI/CD
      │
      ├── Maven
      ├── NPM
      ├── JUnit5
      ├── Mockito
      ├── Jest
      ├── Karma
      ├── SonarQube
      ├── Snyk
      ├── Docker Build
      ├── Trivy
      ├── GitLab Registry
      ├── JFrog Artifactory
      ├── Kubernetes
      ├── Selenium
      └── Production
                  │
                  ▼
        Spring Boot Actuator
                  │
                  ▼
            Prometheus
                  │
                  ▼
               Grafana
```

Cette architecture suit les pratiques DevSecOps modernes : qualité de code (SonarQube), tests unitaires (JUnit 5, Mockito, Jest, Karma), sécurité des dépendances (Snyk), sécurité des images (Trivy), gestion des artefacts (JFrog Artifactory), conteneurisation (Docker + GitLab Registry), orchestration (Kubernetes), tests de bout en bout (Selenium) et observabilité (Prometheus + Grafana).
