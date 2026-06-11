# Tutoriel complet CI/CD Java avec Spring Boot, GitLab CI, GitHub Actions et Jenkins

# Objectif

Dans ce tutoriel, nous allons construire une chaîne CI/CD complète autour d'une application Spring Boot.

Nous verrons :

1. Gestion du code source avec Git
2. Compilation Maven
3. Exécution des tests
4. Analyse qualité de code
5. Packaging de l'application
6. Construction d'une image Docker
7. Publication de l'image
8. Déploiement automatique
9. Implémentation avec :
   - GitLab CI/CD
   - GitHub Actions
   - Jenkins

---

# Architecture globale

```text
Développeur
    |
    v
Git Push
    |
    v
CI Pipeline
 ├── Build
 ├── Tests
 ├── Analyse qualité
 ├── Packaging
 ├── Docker Build
 └── Docker Push
    |
    v
CD Pipeline
 ├── Staging
 └── Production
```

---

# Le projet Spring Boot

## Structure

```text
springboot-cicd-demo
│
├── src
│   ├── main
│   │   └── java
│   └── test
│
├── Dockerfile
├── pom.xml
└── README.md
```

---

# Étape 1 : Création du projet Spring Boot

## Controller

```java
package com.example.demo;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/")
    public String hello() {
        return "Hello CI/CD";
    }
}
```

---

# Étape 2 : Configuration Maven

## pom.xml

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0">

    <modelVersion>4.0.0</modelVersion>

    <groupId>com.example</groupId>
    <artifactId>springboot-cicd-demo</artifactId>
    <version>1.0.0</version>

    <parent>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-parent</artifactId>
        <version>3.5.0</version>
    </parent>

    <dependencies>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-web</artifactId>
        </dependency>

        <dependency>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-starter-test</artifactId>
            <scope>test</scope>
        </dependency>

    </dependencies>

</project>
```

---

# Étape 3 : Tests unitaires

## HelloControllerTest

```java
package com.example.demo;

import org.junit.jupiter.api.Test;

import static org.junit.jupiter.api.Assertions.*;

public class HelloControllerTest {

    @Test
    void shouldReturnMessage() {

        HelloController controller = new HelloController();

        assertEquals(
                "Hello CI/CD",
                controller.hello()
        );
    }
}
```

---

# Comprendre les étapes CI/CD

## 1. Build

Compilation du code.

```bash
mvn compile
```

Objectif :

- détecter les erreurs de compilation
- valider le code source

---

## 2. Tests

```bash
mvn test
```

Objectif :

- vérifier les règles métier
- empêcher les régressions

---

## 3. Analyse qualité

Exemple avec SonarQube :

```bash
mvn sonar:sonar
```

Contrôle :

- bugs
- code smells
- vulnérabilités
- couverture de tests

---

## 4. Packaging

Création du JAR.

```bash
mvn package
```

Résultat :

```text
target/
 └── springboot-cicd-demo.jar
```

---

## 5. Docker Build

Création d'une image Docker.

---

## 6. Docker Push

Publication dans un registre :

- Docker Hub
- GitLab Registry
- GitHub Container Registry

---

## 7. Déploiement

Exemple :

- VM Linux
- Kubernetes
- OpenShift
- AWS ECS

---

# Dockerisation

## Dockerfile

```dockerfile
FROM eclipse-temurin:21-jre

WORKDIR /app

COPY target/*.jar app.jar

EXPOSE 8080

ENTRYPOINT ["java","-jar","app.jar"]
```

---

# Construction locale

```bash
mvn clean package

docker build -t springboot-cicd-demo:1.0 .
```

---

# Exécution locale

```bash
docker run -p 8080:8080 springboot-cicd-demo:1.0
```

---

# Partie 1 : GitLab CI/CD

# Fonctionnement

GitLab exécute les pipelines définis dans :

```text
.gitlab-ci.yml
```

---

# Stages GitLab

```text
build
test
quality
package
docker
deploy
```

---

# .gitlab-ci.yml complet

```yaml
image: maven:3.9.9-eclipse-temurin-21

stages:
  - build
  - test
  - package
  - docker
  - deploy

cache:
  paths:
    - .m2/repository

build:
  stage: build
  script:
    - mvn clean compile

test:
  stage: test
  script:
    - mvn test

package:
  stage: package
  script:
    - mvn package -DskipTests
  artifacts:
    paths:
      - target/*.jar

docker:
  stage: docker
  image: docker:latest

  services:
    - docker:dind

  before_script:
    - docker login -u $DOCKER_USER -p $DOCKER_PASSWORD

  script:
    - docker build -t myrepo/springboot-demo:$CI_COMMIT_SHA .
    - docker push myrepo/springboot-demo:$CI_COMMIT_SHA

deploy:
  stage: deploy

  script:
    - echo "Déploiement"

  only:
    - main
```

---

# Explication

## cache

```yaml
cache:
  paths:
    - .m2/repository
```

Évite de télécharger Maven à chaque pipeline.

---

## artifacts

```yaml
artifacts:
  paths:
    - target/*.jar
```

Conserve le JAR pour les étapes suivantes.

---

## variables GitLab

Dans :

```text
Settings
→ CI/CD
→ Variables
```

Créer :

```text
DOCKER_USER
DOCKER_PASSWORD
```

---

# Partie 2 : GitHub Actions

---

# Structure

```text
.github
└── workflows
    └── ci-cd.yml
```

---

# Workflow complet

```yaml
name: Spring Boot CI/CD

on:
  push:
    branches:
      - main

jobs:

  build:

    runs-on: ubuntu-latest

    steps:

      - name: Checkout
        uses: actions/checkout@v4

      - name: Setup Java
        uses: actions/setup-java@v4
        with:
          distribution: temurin
          java-version: 21

      - name: Cache Maven
        uses: actions/cache@v4
        with:
          path: ~/.m2
          key: maven-cache

      - name: Build
        run: mvn clean compile

      - name: Test
        run: mvn test

      - name: Package
        run: mvn package -DskipTests

      - name: Upload Artifact
        uses: actions/upload-artifact@v4
        with:
          name: jar
          path: target/*.jar
```

---

# Publication Docker Hub

Ajouter :

```yaml
      - name: Login Docker Hub
        uses: docker/login-action@v3
        with:
          username: ${{ secrets.DOCKER_USER }}
          password: ${{ secrets.DOCKER_PASSWORD }}

      - name: Build image
        run: |
          docker build \
            -t myrepo/demo:${{ github.sha }} .

      - name: Push image
        run: |
          docker push myrepo/demo:${{ github.sha }}
```

---

# Secrets GitHub

Dans :

```text
Settings
→ Secrets and variables
→ Actions
```

Ajouter :

```text
DOCKER_USER
DOCKER_PASSWORD
```

---

# Déploiement automatique

Exemple SSH :

```yaml
      - name: Deploy
        uses: appleboy/ssh-action@v1

        with:
          host: ${{ secrets.SERVER_HOST }}
          username: ubuntu
          key: ${{ secrets.SERVER_KEY }}

          script: |
            docker pull myrepo/demo:${{ github.sha }}

            docker stop demo || true

            docker rm demo || true

            docker run -d \
              --name demo \
              -p 8080:8080 \
              myrepo/demo:${{ github.sha }}
```

---

# Partie 3 : Jenkins

---

# Architecture Jenkins

```text
Git
 |
 v
Jenkins
 |
 +-- Build
 +-- Test
 +-- Package
 +-- Docker
 +-- Deploy
```

---

# Installation plugins

Installer :

```text
Pipeline
Git
Docker
Maven Integration
SonarQube Scanner
SSH Agent
```

---

# Jenkinsfile complet

```groovy
pipeline {

    agent any

    tools {
        maven 'Maven'
        jdk 'JDK21'
    }

    stages {

        stage('Checkout') {
            steps {
                git branch: 'main',
                    url: 'https://github.com/company/demo.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Test') {
            steps {
                sh 'mvn test'
            }
        }

        stage('Package') {
            steps {
                sh 'mvn package -DskipTests'
            }
        }

        stage('Docker Build') {
            steps {
                sh '''
                docker build \
                -t myrepo/demo:${BUILD_NUMBER} .
                '''
            }
        }

        stage('Docker Push') {
            steps {

                withCredentials([
                    usernamePassword(
                        credentialsId: 'dockerhub',
                        usernameVariable: 'USER',
                        passwordVariable: 'PASS'
                    )
                ]) {

                    sh '''
                    docker login \
                    -u $USER \
                    -p $PASS

                    docker push myrepo/demo:${BUILD_NUMBER}
                    '''
                }
            }
        }

        stage('Deploy') {

            steps {

                sshagent(['server-key']) {

                    sh '''
                    ssh ubuntu@server << EOF

                    docker pull myrepo/demo:${BUILD_NUMBER}

                    docker stop demo || true
                    docker rm demo || true

                    docker run -d \
                        --name demo \
                        -p 8080:8080 \
                        myrepo/demo:${BUILD_NUMBER}

                    EOF
                    '''
                }
            }
        }
    }
}
```

---

# Intégration SonarQube

## Analyse locale

```bash
mvn clean verify sonar:sonar
```

---

## Jenkins

```groovy
stage('SonarQube') {

    steps {

        withSonarQubeEnv('sonarqube') {

            sh '''
            mvn sonar:sonar
            '''
        }
    }
}
```

---

# Pipeline CI/CD industriel complet

```text
Git Push
    |
    v
Checkout
    |
    v
Build
    |
    v
Tests Unitaires
    |
    v
Tests d'Intégration
    |
    v
SonarQube
    |
    v
Package
    |
    v
Docker Build
    |
    v
Docker Scan
    |
    v
Docker Push
    |
    v
Déploiement Staging
    |
    v
Tests Automatiques
    |
    v
Validation Manuelle
    |
    v
Déploiement Production
```

---

# Bonnes pratiques CI/CD

## 1. Pipeline rapide

Objectif :

```text
< 10 minutes
```

---

## 2. Tests automatiques

Toujours exécuter :

```text
Tests unitaires
Tests intégration
Tests API
```

---

## 3. Build reproductible

Versionner :

```text
Dockerfile
Jenkinsfile
.gitlab-ci.yml
.github/workflows/*
```

---

## 4. Secrets

Ne jamais stocker :

```text
Mot de passe
Clé API
Token
```

dans le dépôt Git.

Utiliser :

```text
GitLab Variables
GitHub Secrets
Jenkins Credentials
```

---

## 5. Déploiement immutable

Toujours déployer :

```text
myrepo/demo:1.0.0
```

et éviter :

```text
latest
```

---

# Comparatif GitLab CI vs GitHub Actions vs Jenkins

| Critère | GitLab CI | GitHub Actions | Jenkins |
|----------|------------|----------------|----------|
| Installation | Très simple | Très simple | Complexe |
| Hébergement | SaaS ou Self-hosted | SaaS ou Self-hosted | Self-hosted |
| Maintenance | Faible | Faible | Élevée |
| Plugins | Moyen | Moyen | Très riche |
| Flexibilité | Élevée | Élevée | Très élevée |
| Courbe d'apprentissage | Faible | Faible | Moyenne |
| Usage entreprise | Très fréquent | Très fréquent | Extrêmement fréquent |

---

# Résumé de la chaîne CI/CD complète

```text
Développeur
    |
git push
    |
    v
CI
 ├─ Compile
 ├─ Test
 ├─ SonarQube
 ├─ Package
 └─ Docker Build
    |
    v
Registry Docker
    |
    v
CD
 ├─ Staging
 ├─ Validation
 └─ Production
    |
    v
Application Spring Boot en ligne
```

Cette architecture représente une chaîne CI/CD moderne utilisée dans la majorité des projets Java Spring Boot en entreprise, qu'ils soient exécutés sur GitLab CI/CD, GitHub Actions ou Jenkins.
