# Tutoriel complet : Les nouveautés de Java 25 (LTS)

Java 25 est la nouvelle version **LTS (Long Term Support)** de Java. Si tu viens de Java 21 (l'ancienne LTS), c'est la version vers laquelle la plupart des entreprises vont progressivement migrer. Java 25 apporte principalement des améliorations de productivité, des évolutions du langage, de meilleures performances de la JVM ainsi que plusieurs API modernes. ([OpenJDK][1])

Dans ce tutoriel, nous allons découvrir les nouveautés les plus intéressantes pour un développeur avec des exemples de code.

---

# 1. Les Compact Source Files (JEP 512)

L'une des nouveautés les plus visibles.

Avant Java 25, même un petit programme nécessitait une classe :

```java
public class HelloWorld {

    public static void main(String[] args) {
        System.out.println("Bonjour !");
    }

}
```

Avec Java 25 :

```java
void main() {
    IO.println("Bonjour !");
}
```

Plus besoin de :

* créer une classe
* déclarer `public static`
* utiliser `System.out.println()`

Le programme peut être exécuté directement :

```bash
java Hello.java
```

### Pourquoi ?

Très pratique pour :

* scripts
* démonstrations
* exercices
* prototypes

Cette fonctionnalité rapproche Java de Python ou Kotlin tout en conservant la compatibilité avec les projets classiques. ([OpenJDK][1])

---

# 2. Module Import Declarations (JEP 511)

Avant :

```java
import java.util.List;
import java.util.Map;
import java.util.ArrayList;
import java.util.HashMap;
import java.time.LocalDate;
```

Avec Java 25 :

```java
import module java.base;
```

ou

```java
import module java.sql;
```

Toutes les classes publiques du module deviennent disponibles.

Exemple :

```java
import module java.base;

void main() {

    List<String> list = new ArrayList<>();

    LocalDate date = LocalDate.now();

    Map<String, Integer> map = new HashMap<>();
}
```

Moins d'importations, code plus lisible. ([Oracle Docs][2])

---

# 3. Primitive Types dans Pattern Matching (Preview)

Le Pattern Matching fonctionne maintenant avec les types primitifs.

Avant :

```java
switch (score) {

    case 0 -> ...
    case 1 -> ...
}
```

On ne pouvait pas vraiment exploiter des patterns.

Maintenant :

```java
switch (value) {

    case int i when i > 100 ->
        System.out.println("Grand entier");

    case int i ->
        System.out.println("Entier");

    default ->
        System.out.println("Autre");
}
```

Même principe avec `instanceof`.

Exemple :

```java
Object obj = 42;

if (obj instanceof int i) {
    System.out.println(i);
}
```

Cette fonctionnalité est encore **Preview**, il faut compiler avec :

```bash
--enable-preview
```

Elle rend le Pattern Matching plus homogène pour tous les types. ([Oracle Docs][3])

---

# 4. Flexible Constructor Bodies (JEP 513)

Avant Java 25 :

```java
class Person {

    String name;

    Person(String name) {
        super();
        this.name = name;
    }

}
```

Le premier appel devait obligatoirement être :

* `super()`
* ou `this(...)`

Java 25 assouplit cette contrainte.

On peut effectuer des traitements avant.

Exemple :

```java
class Person {

    String name;

    Person(String name) {

        if (name == null) {
            throw new IllegalArgumentException();
        }

        super();

        this.name = name;
    }

}
```

Le constructeur devient beaucoup plus naturel à écrire. ([OpenJDK][1])

---

# 5. Scoped Values (JEP 506)

Les `ThreadLocal` sont souvent difficiles à maintenir.

Avant :

```java
ThreadLocal<String> user = new ThreadLocal<>();

user.set("Alice");
```

Avec Scoped Values :

```java
static final ScopedValue<String> USER = ScopedValue.newInstance();

ScopedValue.runWhere(USER, "Alice", () -> {

    System.out.println(USER.get());

});
```

Avantages :

* immutable
* plus rapide
* plus sûr
* parfait avec les Virtual Threads

Très utilisé dans les frameworks modernes. ([OpenJDK][1])

---

# 6. Structured Concurrency (Preview)

Si plusieurs tâches doivent être exécutées en parallèle.

Avant :

```java
ExecutorService executor = Executors.newFixedThreadPool(2);

Future<User> user = executor.submit(...);
Future<Order> order = executor.submit(...);
```

Java 25 :

```java
try (var scope = new StructuredTaskScope.ShutdownOnFailure()) {

    var user = scope.fork(this::loadUser);

    var orders = scope.fork(this::loadOrders);

    scope.join();

    System.out.println(user.get());
    System.out.println(orders.get());

}
```

Les tâches sont gérées comme un bloc unique.

Si une tâche échoue :

* toutes les autres sont annulées.

Le code devient beaucoup plus simple. ([OpenJDK][1])

---

# 7. Stable Values (Preview)

Nouvelle façon de créer des objets initialisés une seule fois.

Avant :

```java
private volatile Configuration config;
```

Avec beaucoup de synchronisation.

Maintenant :

```java
StableValue<Configuration> config =
        StableValue.of(Configuration::load);

Configuration c = config.get();
```

Le chargement est :

* thread-safe
* paresseux
* sans code compliqué

Très utile pour les singletons ou les caches. ([OpenJDK][1])

---

# 8. Nouvelle API de dérivation de clés

Java 25 ajoute une API standard pour dériver des clés cryptographiques.

Avant :

Chaque bibliothèque utilisait sa propre implémentation.

Maintenant :

```java
SecretKey key = ...
```

Les algorithmes comme HKDF deviennent disponibles directement dans le JDK.

C'est particulièrement utile pour :

* OAuth
* TLS
* chiffrement
* applications de sécurité. ([OpenJDK][1])

---

# 9. Shenandoah Générationnel

Le Garbage Collector Shenandoah devient générationnel.

Conséquences :

* moins de pauses
* meilleure mémoire
* meilleures performances
* plus adapté aux gros serveurs

Aucune modification de code n'est nécessaire.

Simplement lancer :

```bash
-XX:+UseShenandoahGC
```

Puis profiter des optimisations. ([OpenJDK][1])

---

# 10. Compact Object Headers

La JVM peut réduire la taille des en-têtes d'objets.

Exemple :

```java
List<Person> persons = new ArrayList<>();
```

Chaque objet consomme moins de mémoire.

Résultat :

* moins de RAM
* meilleur cache CPU
* meilleures performances

Le code Java reste inchangé. ([OpenJDK][1])

---

# 11. Flight Recorder amélioré

Le JDK Flight Recorder gagne plusieurs nouveautés :

* CPU profiling
* Method timing
* Cooperative Sampling

Pour lancer un enregistrement :

```bash
java \
-XX:StartFlightRecording=filename=profiling.jfr \
MonApplication
```

Puis analyser avec **JDK Mission Control**.

Très utile pour le diagnostic des applications en production. ([OpenJDK][1])

---

# Nouveautés les plus utiles au quotidien

| Fonctionnalité          | Intérêt | Maturité |
| ----------------------- | ------- | -------- |
| Compact Source Files    | ⭐⭐⭐⭐⭐   | Standard |
| Module Imports          | ⭐⭐⭐⭐☆   | Standard |
| Flexible Constructors   | ⭐⭐⭐⭐☆   | Standard |
| Scoped Values           | ⭐⭐⭐⭐⭐   | Standard |
| Primitive Patterns      | ⭐⭐⭐☆☆   | Preview  |
| Structured Concurrency  | ⭐⭐⭐⭐⭐   | Preview  |
| Stable Values           | ⭐⭐⭐⭐☆   | Preview  |
| Compact Object Headers  | ⭐⭐⭐⭐☆   | JVM      |
| Generational Shenandoah | ⭐⭐⭐⭐☆   | JVM      |
| JFR amélioré            | ⭐⭐⭐⭐☆   | Standard |

# Faut-il migrer vers Java 25 ?

Si votre projet est actuellement en Java 21 LTS, Java 25 constitue une évolution naturelle avec :

* une syntaxe plus concise (compact source files, imports de modules, constructeurs plus flexibles) ;
* de meilleurs outils pour la concurrence (`ScopedValue`, Structured Concurrency) ;
* des optimisations JVM et GC pour les applications exigeantes ;
* une base LTS qui sera supportée plusieurs années. ([OpenJDK][1])

Pour un nouveau projet, Java 25 est un excellent choix si votre écosystème (frameworks, bibliothèques et outils de build) est compatible. Les fonctionnalités en **Preview** restent à réserver aux expérimentations ou aux projets où leur activation est acceptable.

[1]: https://openjdk.org/projects/jdk/25/?utm_source=chatgpt.com "JDK 25"
[2]: https://docs.oracle.com/en/java/javase/25/language/java-language-changes-summary.html?utm_source=chatgpt.com "Java Language Changes Summary"
[3]: https://docs.oracle.com/en/java/javase/25/migrate/significant-changes-jdk-25.html?utm_source=chatgpt.com "Significant Changes in JDK 25 Release"
