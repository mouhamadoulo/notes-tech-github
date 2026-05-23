# JENKINS

# 🔧 Jenkins : c’est quoi exactement ?
Jenkins est un outil d’intégration continue (CI) et de déploiement continu (CD).<br>
👉 Son rôle :
- automatiser les builds
- lancer les tests
- analyser la qualité du code
- créer des artefacts (JAR, WAR, Docker image)
- déployer automatiquement (serveur, cloud, Kubernetes…)

En gros :
```À chaque fois que tu pushes du code, Jenkins travaille pour toi.```

# 🧠 Jenkins dans un projet Java / Spring Boot
Dans un projet Spring Boot, Jenkins sert typiquement à :
- 1.Récupérer le code (Git)
- 2.Compiler le projet (Maven ou Gradle)
- 3.Lancer les tests (JUnit, Mockito…)
- 4.Vérifier la qualité (SonarQube)
- 5.Générer le .jar
- 6.Déployer l’application (Docker, serveur, cloud)

🏗️ Architecture simple
```mathematica
Développeur → Git (GitHub/GitLab)
                  ↓
               Jenkins
                  ↓
      Build → Tests → Package → Deploy
```

# 🧩 Concepts clés de Jenkins (indispensables)
## 1. Job / Pipeline
- Job freestyle : ancien style (UI, clics)
- Pipeline : moderne, recommandé, versionné dans le code

👉 En Spring Boot, on utilise quasi toujours un pipeline

## 2. Jenkinsfile (LE CŒUR ❤️)
C’est un fichier texte dans ton repo qui décrit toutes les étapes.

📄 Jenkinsfile

Deux styles :
- Declarative pipeline (le plus utilisé)
- Scripted pipeline (plus flexible mais plus complexe)

On va rester en Declarative 👍

### 📦 Exemple réel : projet Spring Boot + Maven
**Structure du projet**
```css
springboot-app/
├── src/
├── pom.xml
├── Dockerfile
└── Jenkinsfile
```

### 🧪 Jenkinsfile simple (build + tests)
```groovy
pipeline {
    agent any

    tools {
        maven 'Maven-3.9'
        jdk 'Java-17'
    }

    stages {
        stage('Checkout') {
            steps {
                git 'https://github.com/mon-org/springboot-app.git'
            }
        }

        stage('Build') {
            steps {
                sh 'mvn clean compile'
            }
        }

        stage('Tests') {
            steps {
                sh 'mvn test'
            }
        }
    }

    post {
        success {
            echo '✅ Build réussi'
        }
        failure {
            echo '❌ Build échoué'
        }
    }
}

```
📝 **Ce que Jenkins fait ici :**
- télécharge le code
- compile
- lance les tests JUnit
- affiche le résultat

### 🧪 Tests Spring Boot (JUnit)
Exemple de test exécuté par Jenkins :
```java
@SpringBootTest
class UserServiceTest {

    @Test
    void shouldCreateUser() {
        assertTrue(true);
    }
}
```

👉 Si un test échoue → pipeline cassée ❌

### 📦 Génération du JAR Spring Boot
Ajoutons une étape package :
```groovy
stage('Package') {
    steps {
        sh 'mvn clean package -DskipTests'
    }
}
```

Résultat :
```bash
target/springboot-app-0.0.1-SNAPSHOT.jar
```

### 📊 Analyse qualité avec SonarQube (très courant)
**Jenkinsfile (extrait)**
```groovy
stage('SonarQube Analysis') {
    environment {
        SONAR_TOKEN = credentials('sonar-token')
    }
    steps {
        sh """
        mvn sonar:sonar \
        -Dsonar.projectKey=springboot-app \
        -Dsonar.host.url=http://sonarqube:9000 \
        -Dsonar.login=$SONAR_TOKEN
        """
    }
}
```

👉 Jenkins bloque le pipeline si :
- couverture de tests trop basse
- bugs critiques
- code smells

### 🐳 Jenkins + Docker + Spring Boot (très demandé)
**Dockerfile Spring Boot**
```dockerfile
FROM eclipse-temurin:17-jre
COPY target/app.jar app.jar
ENTRYPOINT ["java","-jar","/app.jar"]
```

**Jenkinsfile (build image)**
```groovy
stage('Docker Build') {
    steps {
        sh 'docker build -t myapp:latest .'
    }
}

stage('Docker Run') {
    steps {
        sh 'docker run -d -p 8080:8080 myapp:latest'
    }
}
```

👉 Résultat : ton Spring Boot tourne automatiquement 🟢

### 🌍 Déploiement (exemples)
Selon le contexte :
- serveur Linux (SSH + systemd)
- Docker / Docker Compose
- Kubernetes
- AWS / Azure / GCP

Exemple simple SSH :
```groovy
stage('Deploy') {
    steps {
        sh '''
        scp target/app.jar user@server:/apps/
        ssh user@server "systemctl restart springboot-app"
        '''
    }
}
```

### 🔐 Gestion des secrets (TRÈS IMPORTANT)
Jenkins gère :
- mots de passe
- tokens
- clés SSH

Via :
```nginx
Manage Jenkins → Credentials
```

Utilisation :
```groovy
environment {
    DB_PASSWORD = credentials('db-password')
}
```

👉 **Jamais de secrets dans Git**


# ⚠️ Bonnes pratiques Jenkins + Spring Boot
✅ Jenkinsfile versionné<br>
✅ Pipelines courts et rapides<br>
✅ Tests automatiques obligatoires<br>
✅ Qualité du code (Sonar)<br>
✅ Déploiement automatisé<br>
❌ Pas de clics manuels<br>
❌ Pas de secrets en clair<br>


# 🧠 Résumé mental à retenir
**Jenkins = chef d’orchestre**
- Spring Boot = application
- Maven = build
- JUnit = tests
- Docker = packaging
- Jenkins = automate tout