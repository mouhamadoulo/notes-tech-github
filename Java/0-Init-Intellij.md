# Tutoriel IntelliJ IDEA : créer des projets Java et Spring Boot de A à Z

L'objectif est de savoir créer un projet depuis IntelliJ IDEA, le configurer correctement, écrire du code, le lancer, le tester et préparer une structure professionnelle adaptée à un développeur Java senior.

Nous allons voir deux cas :

- Projet Java classique : pour apprendre le langage, les algorithmes, les collections, les threads ou créer un programme autonome.
- Projet Spring Boot : pour développer une API REST, une application métier, des microservices ou un backend connecté à une base de données.

Les étapes ci-dessous sont adaptées à IntelliJ IDEA sous Windows 11, avec un JDK récent. Les menus peuvent légèrement varier selon la version d'IntelliJ et l'édition utilisée.

## 1. Préparer IntelliJ IDEA

[Run IntelliJ IDEA for the first time | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/qeeOy29UImlfg96FWuPf3xW8aIl3JpB1cOhKuzvYXypxRmrlflcy6WWhZoV_CA7g0qU_XLJsNsre_-JKjDONc6oRm0j1BpWOz-R4aJo4jg3ROps288QHqnOvCYTISJa5BQmwjFlE_XXmoF9IwnsdxbPnuruAaHok1KnEBJ7uwhY?purpose=inline)

Étape 1 — Ouvrir IntelliJ IDEA

Sur l'écran d'accueil, tu trouveras généralement `New Project`, `Open` et `Get from VCS`. Si un projet est déjà ouvert, passe par `File > New > Project`.

[SDKs | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/ohNtTTr8GCDJ2wm44YiaylucxrG83yKadNh17Gfdz5R1Ue2PRAvfhMNU8qECAC-cY81-WCWycacHplcmY2JYiQCe_fKOBst79xuXfX_ULbT07--I8eSJLxHm9QFuSLYfVqHD-cdxQ_qNdMqWm-17S-DjRV8pJdu3y8Cc9bWndLU?purpose=inline)

Étape 2 — Vérifier le JDK

Va dans `File > Project Structure > Project`, puis vérifie le champ `SDK` et le `Language level`. Pour un projet récent, Java 21 ou Java 25 sont de bons choix selon les contraintes du projet.

[Maven | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/OqS4m9-SzQNBTtytxZQqd_gYczDNGT_JD_t4f-kn4g3u8bGwLVlk2098ryCiSFXrOIjM3w85IgIEE2cw5Mlhgs9-XEIPFWEa4rR0FUKXOjj3ksKCkR0LPfknRzm3cmVZtwzG2zyswMAiGAHdxw7LihO0CTVY1atgp-QywNF8ByY?purpose=inline)

Étape 3 — Vérifier Maven

Dans `Settings > Build, Execution, Deployment > Build Tools > Maven`, vérifie le Maven utilisé et le JDK de l'importeur Maven. Le Maven Wrapper (`mvnw`) du projet permettra ensuite de fixer une version reproductible pour l'équipe.

### Quel JDK choisir ?

| Version | Quand la choisir                                                                     |
| ------- | ------------------------------------------------------------------------------------ |
| Java 21 | Très bon choix pour les projets d'entreprise et les virtual threads                  |
| Java 25 | Adapté aux nouveaux projets si les bibliothèques et l'environnement sont compatibles |
| Java 17 | Encore fréquent dans les applications existantes                                     |

Important : le JDK qui exécute IntelliJ n'est pas nécessairement le JDK utilisé pour compiler et lancer ton projet. C'est le Project SDK et la configuration du build qui comptent.

## 2. Créer ton premier projet Java classique

[Create your first Java application | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/osFFVPBHaa0X1W6MzydK_I2DB86bv91pog0c2byz5mDqhCqeK12h6wxRbPgW7fQXsMNT5cdaev-sPqS41tSt32GbhHKlAHgzFuyo8g941APKcjUTJgJ_7kmeQsAVC3AjgWsx57rd-jKxcL8DFuqFUo-AsRDy5BG8fJwI5DS68TU?purpose=inline)

[Project tool window | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/LQrqypkTLOgZrejObmdeiRglKg0LIJ8_ZLK-JOLpjofbzYGBYthhE4LWgrKQkE3swOfLYf_HX2reJ4JuIFHMzDRK4g-gku8fx2-RQZmckMtzcu8nCI07zKyHjV2k0L-1Q8rtupS-yJOm-pnEDuHtILqLFFkr7CHnEX7KX9UqfFI?purpose=inline)

[Project tool window | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/uG9uqrmE2E4jMuNywpr8R74nFk1mHYTySDxGXPZkh6-xpNo5mPO1jGZpD6deL424Ons_Bo9exYNe9nmQTdh-eADKbO1VtbUbBVZbTLlDamia9sogRMHe_FQS4IDO_V5EknwDl-fjxmklKoMkZfYQfFUZ0-LJa2bY0nYgPuDrluo?purpose=inline)

5

Nous allons créer un programme simple qui affiche un message dans la console.

### Étape 1 — Créer le projet

1. Ouvre IntelliJ IDEA.
2. Clique sur `New Project`.
3. Sélectionne `Java` dans la liste des langages.
4. Renseigne les paramètres suivants.

Name

java-demo

Location

`C:\dev\java-demo`

Build system

IntelliJ (pour débuter)

JDK

Java 21 ou 25

Add sample code

Activé, si proposé

5\. Clique sur `Create`.

Le système de build IntelliJ suffit pour un exercice simple. Pour un vrai projet d'équipe, Maven ou Gradle sera généralement préférable.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.jetbrains.com\&sz=32)

IntelliJ IDEA Documentation

+1



### Étape 2 — Comprendre la structure

Tu obtiendras une structure proche de celle-ci :

java-demo/ ├── .idea/ ├── src/ │   └── Main.java └── java-demo.iml

- `src/` : code source.
- `Main.java` : classe contenant le point d'entrée du programme.
- `.idea/` : configuration locale de l'IDE.

Dans un projet Maven ou Gradle, tu retrouveras plutôt `src/main/java` et `src/test/java`.

### Étape 3 — Créer un package et une classe

Dans le panneau `Project` à gauche :

1. Clique droit sur `src`.
2. Choisis `New > Package`.
3. Saisis `com.example.demo`.
4. Clique droit sur le package.
5. Choisis `New > Java Class`.
6. Nomme la classe `Main`.

Ajoute le code suivant :

```

package com.example.demo;

public class Main {

    public static void main(String[] args) {
        System.out.println("Bonjour depuis IntelliJ IDEA !");
    }
}

```

Le point d'entrée est la méthode `main`. La JVM commence l'exécution du programme à cet endroit.

### Étape 4 — Exécuter le programme

[Tutorial: Run a Java application | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/DsbhvAgAC76M7CPNnRrJNS8jdfVhdYopLVrPdKsu3U7BYYdQnkCPz-rTqEhU_XAc1nlR2jk_Z0BnSAQM9wYd7RA-IeXjLdFansypCLTxBzinPHJ1toM1SOPcX5b7IcM3q8Oj7bCSNAPfxFKMgYKF0jfNys-JJvYaFgsXlTyzJE0?purpose=inline)

[jetbrains.com](https://www.jetbrains.com/help/idea/run-java-applications.html)

1. Clique sur le triangle vert à gauche de la méthode `main`.
2. Sélectionne `Run 'Main.main()'`.
3. Observe la fenêtre `Run` en bas.

Résultat attendu :

```
Bonjour depuis IntelliJ IDEA !
```

Tu viens de créer et d'exécuter ton premier projet Java.

### Étape 5 — Déboguer le programme

Le débogueur est essentiel pour comprendre et diagnostiquer le code.

1. Clique dans la marge à gauche d'une ligne exécutable pour placer un breakpoint (point rouge).
2. Clique droit sur la classe et choisis `Debug`.
3. Le programme s'arrêtera au breakpoint.
4. Utilise `Step Over` pour exécuter la ligne suivante, `Step Into` pour entrer dans une méthode et `Resume` pour poursuivre l'exécution.
5. Observe les variables dans la fenêtre Debug.

Raccourcis utiles sous Windows :

| Action                    | Raccourci courant  |
| ------------------------- | ------------------ |
| Recherche de fichier      | `Ctrl + Shift + N` |
| Rechercher partout        | `Double Maj`       |
| Exécuter la configuration | `Shift + F10`      |
| Déboguer la configuration | `Shift + F9`       |
| Rechercher une action     | `Ctrl + Shift + A` |

Les raccourcis peuvent varier selon le keymap configuré.

## 3. Créer un projet Java avec Maven

Maven est très utilisé en entreprise pour gérer les dépendances, compiler le code, exécuter les tests et produire un JAR.

[Building a Java application in IntelliJ IDEA - Dev.java](https://images.openai.com/static-rsc-4/VEI7OAr79vwXK1HnisRBy0XqDBq7xXRdDYDqkKgPgdfKD6HHf0AyMAMrD98dxr3Zj5ZAyju1UEkaKY8xZsO1txEmt5iw6P2Cnv1O7gCFWTC-ZbqrQab53xK64aaE-x8cr_c0l-d2HAG5kOZeMnQW5DcRhb-AhCxyvYeNbsEsNOg?purpose=inline)

[dev.java](https://dev.java/learn/intellij-idea/)

### Étape 1 — Créer le projet

Dans `File > New > Project` :

- Name : `java-maven-demo`
- Language : Java
- Build system : Maven
- JDK : Java 21 ou 25
- GroupId : `com.example`
- ArtifactId : `java-maven-demo`

Si IntelliJ ne propose pas les champs Maven dans cette fenêtre, tu peux aussi générer le projet via un archétype Maven ou utiliser Spring Initializr pour un projet Spring Boot.

### Étape 2 — Comprendre la structure Maven

java-maven-demo/ ├── pom.xml ├── mvnw ├── mvnw\.cmd ├── .mvn/ ├── src/ │   ├── main/ │   │   ├── java/ │   │   └── resources/ │   └── test/ │       └── java/ └── target/

- `pom.xml` : dépendances et configuration de build.
- `src/main/java` : code applicatif.
- `src/main/resources` : fichiers de configuration et ressources.
- `src/test/java` : tests.
- `target/` : fichiers générés par Maven.
- `mvnw` et `mvnw.cmd` : Maven Wrapper, qui permet d'utiliser la version Maven prévue par le projet.

### Étape 3 — Ajouter une dépendance

Dans `pom.xml`, Maven gère les bibliothèques du projet. Par exemple, pour ajouter JUnit 5, tu peux utiliser une dépendance de test comme celle-ci :

```

<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.12.2</version>
    <scope>test</scope>
</dependency>

```

Cet exemple montre la syntaxe Maven ; dans un projet d'équipe, préfère une version validée par le projet ou gérée par un BOM.

Après modification, clique sur `Load Maven Changes` si IntelliJ affiche une notification. Tu peux également ouvrir la fenêtre `Maven` et lancer les objectifs du projet.

### Étape 4 — Lancer les commandes Maven

Ouvre l'onglet `Terminal` dans IntelliJ, à la racine du projet.

Sous Windows :

```
.\mvnw.cmd clean test
```

Pour créer le package :

```
.\mvnw.cmd clean package
```

Les principales phases sont :

- `clean` : supprime les résultats de build précédents.
- `test` : compile et exécute les tests.
- `package` : construit l'artefact, par exemple un JAR.

Pour un projet professionnel, retiens cette règle : le build doit pouvoir fonctionner en ligne de commande, pas seulement depuis le bouton Run d'IntelliJ.

## 4. Créer un projet Spring Boot depuis IntelliJ IDEA

C'est la méthode à privilégier pour créer un backend Java professionnel. IntelliJ s'appuie sur Spring Initializr, qui génère la structure du projet, le fichier Maven ou Gradle et la classe de démarrage.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.jetbrains.com\&sz=32)

IntelliJ IDEA Documentation

+1



[Tutorial: Create your first Spring application | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/f_cKHV1e1MmwgQJdsiu2l8UaxCmCg-Jws4l-sb4mn-wFUEKwAfd-ypjcWxBZcjv4yIB-rotXMvClgFMZM186iaHxR3WNdWYVmvewvlKCiW5HMgXCtQVi5G_0jk6w-ttvbbMu_aRdx_TIkwEvRzdK3fYZ6_RTo-ft3NzNDsfQetM?purpose=inline)

[Spring Boot project wizard | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/0wj8SeH5-EQrhd92ayQC814LGn8C6S_UZZORsvYUgIn4iVYGUuY5eZEEmRtdau6-tQN_RlfEUsKug0YCcI200DYJAHpDQ_JX-CKp_3Ay5hhCCB6At6R2gqVYC9yU866EEWSe6sP0aepy3OIVXy9lbOhE19Epgc9BPEQPwnbmvQM?purpose=inline)

### Étape 1 — Ouvrir l'assistant Spring Boot

1. Depuis l'écran d'accueil, clique sur `New Project`.
2. Dans les générateurs disponibles, sélectionne `Spring Boot`.
3. Si cette option n'apparaît pas, utilise directement le site officiel [start.spring.io](https://start.spring.io/), puis ouvre le projet généré dans IntelliJ.

Selon l'édition et la version d'IntelliJ, l'intégration Spring peut varier. Le générateur web reste une solution simple.

### Étape 2 — Renseigner les paramètres du projet

Pour notre exemple, créons une API de gestion des paiements.

## Configuration du projet

Nom du projet

Group

Version Java

Java 21

Java 25

Système de build

Maven

Gradle

Version Spring Boot souhaitée

Spring Boot 4.0.x

Spring Boot 3.5.x

Dernière version stable proposée

Choisis une version effectivement proposée par le générateur et compatible avec tes dépendances. Les numéros ci-dessus sont des familles de versions, pas des versions de correctif précises.

### Récapitulatif

Artifact : payment-api

Package racine : com.example.paymentapi

Build : Maven · Java 21 · Spring Boot 4.0.x

&#x20; Copier ma configuration

Voici la signification des champs :

| Champ        | Signification                                          |
| ------------ | ------------------------------------------------------ |
| Name         | Nom du projet dans IntelliJ                            |
| Group        | Identifiant du groupe, généralement un domaine inversé |
| Artifact     | Nom de l'artefact généré                               |
| Package name | Package Java racine                                    |
| Type         | Maven ou Gradle                                        |
| Packaging    | JAR pour une API autonome                              |
| Java         | Version du langage et du JDK ciblée                    |

Quel choix faire ? Pour suivre ce tutoriel, je recommande Maven et Java 21, qui est un bon compromis pour un projet d'entreprise. Spring Boot 4 demande au minimum Java 17 ; vérifie la compatibilité de chaque bibliothèque si tu utilises Java 25.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://docs.spring.io\&sz=32)

Home

+1



### Étape 3 — Sélectionner les dépendances

Clique sur `Next` si l'assistant affiche plusieurs écrans, puis recherche les dépendances suivantes.

Spring Web

Pour créer des contrôleurs REST et exposer des endpoints HTTP.

Validation

Pour valider les données entrantes, par exemple avec `@NotBlank` et `@Valid`.

Spring Boot DevTools — facultatif

Pour faciliter le développement local et les redémarrages.

Spring Boot Starter Test

Pour les tests JUnit, les assertions et les tests Spring.

Pour commencer, n'ajoute pas PostgreSQL, JPA, Kafka ou Spring Security sans besoin immédiat. Tu pourras les ajouter plus tard via `pom.xml` ou l'action `Add Starters` d'IntelliJ.&#x20;

[image](https://www.google.com/s2/favicons?domain=https://www.jetbrains.com\&sz=32)

IntelliJ IDEA Documentation

+1



Clique ensuite sur `Create`.

### Étape 4 — Comprendre les fichiers générés

payment-api/ ├── .mvn/ ├── mvnw ├── mvnw\.cmd ├── pom.xml ├── src/ │   ├── main/ │   │   ├── java/ │   │   │   └── com/example/paymentapi/ │   │   │       └── PaymentApiApplication.java │   │   └── resources/ │   │       └── application.properties │   └── test/ │       └── java/ └── target/

- `PaymentApiApplication.java` : point d'entrée Spring Boot.
- `application.properties` : configuration de l'application.
- `pom.xml` : dépendances, plugins et compilation.
- `src/test/java` : tests automatisés.
- `mvnw.cmd` : commande Maven Wrapper pour Windows.

La classe principale ressemble à ceci :

```

package com.example.paymentapi;

import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication
public class PaymentApiApplication {

    public static void main(String[] args) {
        SpringApplication.run(
            PaymentApiApplication.class, args
        );
    }
}

```

L'annotation `@SpringBootApplication` active notamment la configuration automatique et le scan des composants à partir du package de la classe principale et de ses sous-packages.

### Étape 5 — Créer ton premier endpoint REST

Dans `src/main/java`, crée un package `controller`, puis une classe `HelloController`.

```

package com.example.paymentapi.controller;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController
public class HelloController {

    @GetMapping("/api/hello")
    public String hello() {
        return "Bonjour depuis Spring Boot !";
    }
}

```

Explication :

- `@RestController` déclare un contrôleur REST.
- `@GetMapping("/api/hello")` associe une requête HTTP GET à la méthode.
- `return` renvoie le contenu de la réponse HTTP.

### Étape 6 — Lancer l'application

[Tutorial: Create your first Spring application | IntelliJ IDEA Documentation](https://images.openai.com/static-rsc-4/Cuc_SejkpsffBLlBKUrWAkJj6tpLoMldEVEi0VeSt_TxBOA2FE8WYgkdH0Q6aXVFyuhFnYr9G7pTqEX9n2V_vNIgouif6k0ooUpLOqYQnPdDSIPDpr715qU14Sla0_FidezxGwJDxxZrU8pZ3oa2_dalbzC94_Vx3XGUtCPxMsQ?purpose=inline)

[jetbrains.com](https://www.jetbrains.com/help/idea/your-first-spring-application.html)

1. Ouvre `PaymentApiApplication.java`.
2. Clique sur le triangle vert à gauche de la méthode `main`.
3. Choisis `Run 'PaymentApiApplication'`.
4. Attends le message indiquant que l'application a démarré.

Si aucun port n'a été personnalisé, le serveur web utilise normalement le port `8080`.

Teste ensuite cette URL dans ton navigateur :

[http://localhost:8080/api/hello](http://localhost:8080/api/hello)

Résultat attendu :

```
Bonjour depuis Spring Boot !
```

Tu as désormais une API REST qui fonctionne.

### Étape 7 — Configurer le port et le profil

Dans `src/main/resources/application.properties` :

```

spring.application.name=payment-api
server.port=8080

```

Pour un environnement local, tu peux créer un fichier `application-dev.properties` et activer le profil `dev` dans la configuration d'exécution IntelliJ, ou avec :

```
.\mvnw.cmd spring-boot:run "-Dspring-boot.run.profiles=dev"
```

Cette commande Maven permet de lancer l'application avec le profil `dev`. Pour des paramètres sensibles, utilise des variables d'environnement ou un gestionnaire de secrets, jamais des identifiants enregistrés dans Git.

## 5. Ajouter une architecture professionnelle

Une fois le premier endpoint fonctionnel, évite de placer toute la logique dans le contrôleur. Organise ton code par responsabilités.

Controller

HTTP, validation et DTO

Service

Logique métier et transactions

Repository

Accès aux données

Base de données

PostgreSQL, par exemple

Voici une structure cible pour une API métier :

```

com.example.paymentapi
├── PaymentApiApplication.java
├── controller/
│   └── PaymentController.java
├── service/
│   └── PaymentService.java
├── repository/
│   └── PaymentRepository.java
├── model/
│   └── Payment.java
├── dto/
│   ├── PaymentRequest.java
│   └── PaymentResponse.java
├── exception/
│   └── GlobalExceptionHandler.java
└── config/

```

Cette structure est une base, pas une règle absolue. Pour une application plus grande, une organisation par fonctionnalité métier peut être plus facile à maintenir.

## 6. Écrire et exécuter un test

Dans `src/test/java`, crée un test pour vérifier ton endpoint. Avec `spring-boot-starter-test` et Spring MVC, tu peux utiliser `@WebMvcTest`.

```

package com.example.paymentapi.controller;

import org.junit.jupiter.api.Test;
import org.springframework.beans.factory.annotation.Autowired;
import org.springframework.boot.test.autoconfigure.web.servlet.WebMvcTest;
import org.springframework.test.web.servlet.MockMvc;

import static org.springframework.test.web.servlet.request.MockMvcRequestBuilders.get;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.content;
import static org.springframework.test.web.servlet.result.MockMvcResultMatchers.status;

@WebMvcTest(HelloController.class)
class HelloControllerTest {

    @Autowired
    private MockMvc mockMvc;

    @Test
    void shouldReturnHelloMessage() throws Exception {
        mockMvc.perform(get("/api/hello"))
            .andExpect(status().isOk())
            .andExpect(content().string(
                "Bonjour depuis Spring Boot !"
            ));
    }
}

```

Clique sur le triangle vert à gauche de la méthode `shouldReturnHelloMessage`, puis sur `Run`.

Tu peux également lancer toute la suite de tests dans le terminal :

```
.\mvnw.cmd clean verify
```

Le build compile le projet, exécute les tests et vérifie les étapes de validation configurées dans Maven.

## 7. Que faire si IntelliJ ne propose pas Spring Boot ?

Tu peux utiliser le générateur officiel :

[Spring Initializr Reference Guide](https://images.openai.com/static-rsc-4/I7P7daQymbePQ4RCNUnl2Ix2hFVaQJW7JMbKZ5xNQSuygF40ScU0LR2QnwQvSkLBnM295Eq16G6_DyISRyYDnVUn4sMArRXndO6hZDsOEmrw7NLGypdqVpuYH_tfxN-2BCJ1gJd__2YHjeHmO2arJTq03mf_1bMAvYXnBPefbG0?purpose=inline)

Spring Initializr

Configure le projet, télécharge l'archive ZIP, décompresse-la, puis ouvre le dossier avec `File > Open` dans IntelliJ.

[Ouvrir start.spring.io](https://start.spring.io/)

Si IntelliJ ne reconnaît pas Maven, ouvre `pom.xml` et sélectionne `Load Maven Project` ou `Add as Maven Project`. Si le JDK est introuvable, passe par `File > Project Structure > SDKs` et ajoute-le depuis son dossier d'installation.

## 8. Checklist finale

Objectifs à maîtriser

0/8

Créer et exécuter une classe Java avec main()

Créer un projet Maven et comprendre pom.xml

Générer un projet Spring Boot avec Spring Initializr

Créer un contrôleur REST et tester un endpoint

Configurer le port et les profils

Écrire un test JUnit / MockMvc

Lancer clean verify depuis le terminal

Créer un dépôt Git avec un .gitignore adapté

### Documentation officielle à conserver

- [Créer une application Java dans IntelliJ IDEA](https://www.jetbrains.com/help/idea/creating-and-running-your-first-java-application.html)
- [Créer une application Spring Boot dans IntelliJ IDEA](https://www.jetbrains.com/help/idea/your-first-spring-application.html)
- [Générateur Spring Initializr](https://start.spring.io/)
- [Prérequis officiels de Spring Boot](https://docs.spring.io/spring-boot/system-requirements.html)

Le meilleur exercice pour consolider ces étapes : crée une API `payment-api` avec un endpoint `GET /api/payments`, puis ajoute progressivement un service métier, un repository JPA, PostgreSQL, la validation, la gestion des erreurs et des tests d'intégration. Tu passeras ainsi de la simple création de projet à un backend réaliste de niveau professionnel.
