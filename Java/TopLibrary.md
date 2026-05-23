# TOP LIBRARIES EVERY JAVA DEVELOPER SHOULD KNOW

# JUnit
JUnit est la librairie de tests unitaires la plus utilisée en Java.<br>
Elle permet de vérifier automatiquement que ton code fonctionne comme prévu.

**Cas d’utilisation :**
- Tester une méthode
- Éviter les régressions quand tu modifies ton code
- Sécuriser les refactorings

**Exemple**
```java
@Test
void additionTest() {
    assertEquals(5, Calculator.add(2, 3));
}

```
👉 Ici, on vérifie que la méthode add retourne bien 5.

# Mockito
Mockito sert à simuler (mock) des dépendances lors des tests.

**Pourquoi ?**<br>
Pour tester une classe sans dépendre :
- d’une base de données
- d’un service externe
- d’une API

**Cas d’utilisation :**
- Tester un service sans appeler réellement un repository
- Simuler des comportements spécifiques

**Exemple**
```java
UserRepository repo = mock(UserRepository.class);
when(repo.findById(1L)).thenReturn(new User("Alice"));

```
👉 Ici, on simule le repository sans accéder à une vraie base de données.

# Jackson
Jackson permet de convertir des objets Java en JSON et inversement.

**Très utilisé dans :**
- APIs REST (Spring Boot)
- Microservices

**Cas d’utilisation :**
- Envoyer des données JSON à un front
- Lire une réponse JSON d’une API

**Exemple**
```java
ObjectMapper mapper = new ObjectMapper();
String json = mapper.writeValueAsString(user);
```
👉 Transforme un objet User en JSON.

# SLF4J 
SLF4J est une **façade de logging** (journalisation).

👉 Elle ne log pas elle-même, mais permet de brancher :
- Logback
- Log4j
- java.util.logging

**Cas d’utilisation :**
- Tracer l’exécution d’une application
- Déboguer
- Auditer

**Exemple**
```java 
private static final Logger log = LoggerFactory.getLogger(MyClass.class);

log.info("Application démarrée");
log.error("Erreur critique", exception);

```

# Lombok
Lombok permet de **réduire énormément le code boilerplate**.

**Il génère automatiquement**
- getters / setters
- constructeurs
- toString(), equals(), hashCode()

**Cas d’utilisation :**
- Modèles (DTO, Entity)
- Code plus lisible et plus court

**Exemple**
```java 
@Data
public class User {
    private String name;
    private int age;
}

```
👉 Lombok génère tout automatiquement 🎉

# Apache Commons
Apache Commons est un ensemble d’utilitaires pour Java.

**Modules populaires**
- commons-lang → String, Number, Date
- commons-io → fichiers, streams
- commons-collections

**Cas d’utilisation :**
- Éviter de réécrire du code utilitaire
- Simplifier des opérations courantes

**Exemple**
```java 
StringUtils.isBlank(str);
FileUtils.readFileToString(file, "UTF-8");

```

# Hibernate ORM
Hibernate est un ORM (Object Relational Mapping).

👉 Il fait le lien entre :
- objets Java
- tables SQL

**Cas d’utilisation :**
- Applications avec base de données
- CRUD sans écrire trop de SQL

**Exemple**
```java 
@Entity
public class User {
    @Id
    @GeneratedValue
    private Long id;
    private String name;
}

```
```java 
entityManager.persist(user);

```
👉 Hibernate s’occupe de l’INSERT SQL pour toi.

# JAXB
JAXB permet de **convertir des objets Java en XML** et inversement.

**Cas d’utilisation :**
- Web services SOAP
- Échanges XML
- Configurations legacy

**Exemple**
```java 
@XmlRootElement
public class User {
    public String name;
}
```
```java 
marshaller.marshal(user, System.out);
```
👉 Convertit l’objet Java en XML.


# 🧠 Résumé rapide
| Librairie      | Utilité principale       |
| -------------- | ------------------------ |
| JUnit          | Tests unitaires          |
| Mockito        | Mock pour les tests      |
| Jackson        | JSON ↔ Java              |
| SLF4J          | Logging                  |
| Lombok         | Réduction du boilerplate |
| Apache Commons | Utilitaires              |
| Hibernate      | ORM (Java ↔ SQL)         |
| JAXB           | XML ↔ Java               |
