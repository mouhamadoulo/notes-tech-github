# Tutoriel complet : les raccourcis et plugins indispensables d'IntelliJ IDEA pour un développeur Java Spring Boot

Si tu développes en Java/Spring Boot, maîtriser IntelliJ IDEA peut facilement te faire gagner plusieurs heures par semaine. L'objectif n'est pas seulement d'écrire du code plus vite, mais surtout de naviguer, refactorer, déboguer et comprendre une base de code efficacement.

Je vais me concentrer sur les raccourcis les plus utiles au quotidien et les plugins qui apportent une vraie valeur.

> Les raccourcis indiqués sont principalement pour Windows/Linux. Pour macOS, remplace généralement `Ctrl` par `⌘` et `Alt` par `⌥`.

---

# 1. Les raccourcis de navigation (les plus importants)

## Rechercher n'importe quoi

### Search Everywhere

```text
Double Shift
```

Permet de rechercher :

* Classes
* Fichiers
* Méthodes
* Actions IntelliJ
* Paramètres
* Plugins

Exemple :

Tape :

```text
UserController
```

ou

```text
application.yml
```

ou

```text
Maven Reload
```

C'est probablement le raccourci le plus utilisé.

---

## Ouvrir une classe

```text
Ctrl + N
```

Exemple :

```text
UserService
```

---

## Ouvrir un fichier

```text
Ctrl + Shift + N
```

Exemple :

```text
application-prod.yml
```

---

## Rechercher un symbole

```text
Ctrl + Alt + Shift + N
```

Recherche :

* méthodes
* variables
* constantes

Exemple :

```java
findUserByEmail
```

---

## Aller à la déclaration

```text
Ctrl + B
```

ou

```text
Ctrl + Click
```

Exemple :

```java
userRepository.save(user);
```

Tu arrives directement dans l'interface Repository.

---

## Voir l'implémentation

```text
Ctrl + Alt + B
```

Très utile avec Spring.

Exemple :

```java
UserService userService;
```

Tu vas directement vers :

```java
UserServiceImpl
```

---

## Revenir en arrière

```text
Ctrl + Alt + ←
```

---

## Revenir en avant

```text
Ctrl + Alt + →
```

Comme dans un navigateur.

---

# 2. Navigation Spring Boot

## Trouver les usages

```text
Alt + F7
```

Exemple :

```java
UserService
```

Tu vois partout où le service est utilisé.

---

## Hiérarchie des appels

```text
Ctrl + Alt + H
```

Permet de savoir :

```java
createUser()
```

qui appelle cette méthode.

Très utile sur les gros projets.

---

## Hiérarchie de type

```text
Ctrl + H
```

Exemple :

```java
JpaRepository
```

Tu vois toutes les classes/interfaces liées.

---

# 3. Écriture de code

## Générer du code

```text
Alt + Insert
```

Permet de générer :

* constructeur
* getter/setter
* equals
* hashCode
* toString

---

## Complétion intelligente

```text
Ctrl + Shift + Space
```

Exemple :

```java
User user =
```

IntelliJ propose directement le type le plus pertinent.

---

## Complétion standard

```text
Ctrl + Space
```

---

## Complétion de ligne

```text
Ctrl + Shift + Enter
```

Exemple :

Avant :

```java
if(user != null
```

Après :

```java
if(user != null) {

}
```

---

## Dupliquer une ligne

```text
Ctrl + D
```

---

## Supprimer une ligne

```text
Ctrl + Y
```

---

## Déplacer une ligne

Vers le haut :

```text
Alt + Shift + ↑
```

Vers le bas :

```text
Alt + Shift + ↓
```

---

# 4. Refactoring (essentiel)

## Renommer

```text
Shift + F6
```

Ne jamais faire :

```text
Ctrl + H
```

dans tout le projet.

Toujours utiliser :

```text
Shift + F6
```

IntelliJ met à jour :

* imports
* usages
* références

---

## Extraire une variable

```text
Ctrl + Alt + V
```

Avant :

```java
return repository.findById(id).orElseThrow();
```

Après :

```java
User user = repository.findById(id).orElseThrow();

return user;
```

---

## Extraire une méthode

```text
Ctrl + Alt + M
```

Très utilisé pour nettoyer les services Spring.

---

## Extraire un champ

```text
Ctrl + Alt + F
```

---

## Extraire une constante

```text
Ctrl + Alt + C
```

---

## Refactor This

```text
Ctrl + Alt + Shift + T
```

Menu complet des refactorings.

---

# 5. Spring Boot : raccourcis spécifiques

## Aller au Bean Spring

Double Shift puis :

```text
beans
```

ou

```text
spring
```

---

## Navigation entre endpoints

Recherche :

```java
@GetMapping
```

ou utilise :

```text
Navigate → Related Symbol
```

---

## Trouver les propriétés Spring

Dans :

```yaml
spring.datasource.url
```

Ctrl + Click permet d'aller directement à la documentation de la propriété.

---

# 6. Gestion Maven et Gradle

## Recharger Maven

```text
Ctrl + Shift + A
```

Puis :

```text
Reload Maven Project
```

---

## Exécuter Maven

```text
Ctrl + Shift + A
```

Puis :

```text
mvn clean install
```

---

## Ouvrir l'outil Maven

```text
Alt + 8
```

---

# 7. Débogage professionnel

## Mettre un breakpoint

```text
Ctrl + F8
```

---

## Lancer le debug

```text
Shift + F9
```

---

## Continuer

```text
F9
```

---

## Step Over

```text
F8
```

Passe à la ligne suivante.

---

## Step Into

```text
F7
```

Entre dans la méthode.

---

## Step Out

```text
Shift + F8
```

Sort de la méthode.

---

## Évaluer une expression

```text
Alt + F8
```

Exemple :

```java
user.getEmail()
```

pendant le debug.

---

# 8. Les inspections IntelliJ à connaître

## Afficher les intentions

```text
Alt + Enter
```

Le raccourci magique.

Exemples :

* créer une classe manquante
* ajouter un import
* convertir une boucle en Stream
* corriger une erreur

---

## Reformater le code

```text
Ctrl + Alt + L
```

À utiliser constamment.

---

## Optimiser les imports

```text
Ctrl + Alt + O
```

---

# 9. Plugins indispensables pour Java Spring Boot

## 1. Lombok

Indispensable si le projet utilise Lombok.

Permet :

```java
@Getter
@Setter
@Builder
@RequiredArgsConstructor
```

sans erreurs IDE.

Plugin :

* Lombok

---

## 2. SonarLint

Analyse statique en temps réel.

Détecte :

* bugs potentiels
* code smells
* problèmes de sécurité

Plugin :

[SonarLint](https://www.sonarsource.com/products/sonarlint/?utm_source=chatgpt.com)

---

## 3. Key Promoter X

Le meilleur plugin pour apprendre IntelliJ.

À chaque fois que tu utilises la souris, il affiche le raccourci clavier correspondant.

Plugin :

[Key Promoter X](https://plugins.jetbrains.com/plugin/9792-key-promoter-x?utm_source=chatgpt.com)

---

## 4. Rainbow Brackets

Coloration des parenthèses imbriquées.

Très pratique pour :

```java
if(...)
```

complexes.

Plugin :

[Rainbow Brackets](https://plugins.jetbrains.com/plugin/10080-rainbow-brackets?utm_source=chatgpt.com)

---

## 5. String Manipulation

Ajoute énormément d'outils :

* CamelCase
* SnakeCase
* UPPERCASE
* JSON formatting

Plugin :

[String Manipulation](https://plugins.jetbrains.com/plugin/2162-string-manipulation?utm_source=chatgpt.com)

---

## 6. GitToolBox

Améliore Git dans IntelliJ.

Affiche :

* auteur
* date du commit
* historique

Plugin :

[GitToolBox](https://plugins.jetbrains.com/plugin/7499-gittoolbox?utm_source=chatgpt.com)

---

## 7. .ignore

Génération facile de :

```text
.gitignore
```

Plugin :

[.ignore](https://plugins.jetbrains.com/plugin/7495--ignore?utm_source=chatgpt.com)

---

## 8. Maven Helper

Très utile pour résoudre :

```text
dependency conflicts
```

dans les gros projets Spring.

Plugin :

[Maven Helper](https://plugins.jetbrains.com/plugin/7179-maven-helper?utm_source=chatgpt.com)

---

## 9. JPA Buddy

Excellent pour :

* Hibernate
* JPA
* Spring Data

Fonctionnalités :

* génération d'entités
* diagrammes
* génération de repositories

Plugin :

[JPA Buddy](https://jpa-buddy.com/?utm_source=chatgpt.com)

---

## 10. Database Tools

Souvent déjà inclus dans IntelliJ IDEA Ultimate.

Permet :

* connexion PostgreSQL
* MySQL
* Oracle
* SQL Server

Très utile pour tester les requêtes sans quitter l'IDE.

---

# 10. Git : raccourcis utiles

## Commit

```text
Ctrl + K
```

---

## Push

```text
Ctrl + Shift + K
```

---

## Pull

```text
Ctrl + T
```

---

## Historique Git

```text
Alt + 9
```

---

## Annotate (blame)

Clic droit :

```text
Git → Annotate
```

---

# 11. Configuration IntelliJ recommandée pour Spring Boot

## Activer l'import automatique Maven

Settings → Maven

Cocher :

```text
Automatically Reload Projects
```

---

## Activer les paramètres Spring

Installer :

* Spring
* Spring Boot
* Spring Data JPA

(normalement inclus dans IDEA Ultimate)

---

## Configurer le formatage

Settings → Editor → Code Style → Java

Importer le style de l'équipe si disponible.

---

# Les 15 raccourcis à mémoriser en priorité

| Raccourci            | Usage                        |
| -------------------- | ---------------------------- |
| Double Shift         | Recherche globale            |
| Ctrl + N             | Classe                       |
| Ctrl + Shift + N     | Fichier                      |
| Ctrl + B             | Aller à la définition        |
| Alt + F7             | Trouver usages               |
| Shift + F6           | Renommer                     |
| Ctrl + Alt + M       | Extraire méthode             |
| Ctrl + D             | Dupliquer                    |
| Alt + Enter          | Correction rapide            |
| Ctrl + Alt + L       | Formatter                    |
| Ctrl + Shift + Space | Auto-complétion intelligente |
| Ctrl + F8            | Breakpoint                   |
| Shift + F9           | Debug                        |
| Ctrl + K             | Commit Git                   |
| Ctrl + Shift + A     | Toutes les actions           |

Si tu maîtrises déjà ces 15 raccourcis et les plugins **Lombok**, **SonarLint**, **Key Promoter X**, **GitToolBox** et **JPA Buddy**, tu es déjà au niveau d'utilisation qu'ont beaucoup de développeurs Spring Boot expérimentés sur IntelliJ IDEA.
