# Les Design Patterns les plus utilisés dans le code en Java

# 🏗️ 1. <ins>Creational Patterns (création d’objets)</ins>

## Singleton
👉 Une seule instance dans toute l’application<br>
**Quand ?**<br>
Connexion DB, config globale, logger…

```java
public class Singleton {
    private static final Singleton INSTANCE = new Singleton();

    private Singleton() {}

    public static Singleton getInstance() {
        return INSTANCE;
    }
}

```

## Factory Method
👉 Créer des objets sans exposer la logique de création<br>
**Quand ?**<br>
Quand le type exact de l’objet dépend du contexte.

```java 
interface Animal {
    void speak();
}

class Dog implements Animal {
    public void speak() { System.out.println("Woof"); }
}

class Cat implements Animal {
    public void speak() { System.out.println("Meow"); }
}

class AnimalFactory {
    static Animal create(String type) {
        return switch (type) {
            case "dog" -> new Dog();
            case "cat" -> new Cat();
            default -> throw new IllegalArgumentException();
        };
    }
}

```

## Builder
👉 Construire des objets complexes pas à pas<br>
**Quand ?**<br>
Beaucoup de paramètres optionnels.

```java 
class User {
    String name;
    int age;

    static class Builder {
        private final User user = new User();

        Builder name(String n) { user.name = n; return this; }
        Builder age(int a) { user.age = a; return this; }
        User build() { return user; }
    }
}

```

# 🧩 2. <ins>Structural Patterns (structure du code)</ins>

## Adapter
👉 Adapter une interface existante à une autre<br>
**Quand ?**<br>
Intégrer une lib externe.

```java 
class OldService {
    void oldRequest() {}
}

interface NewService {
    void request();
}

class Adapter implements NewService {
    private final OldService old = new OldService();
    public void request() {
        old.oldRequest();
    }
}

```

## Decorator
👉 Ajouter des comportements sans modifier la classe<br>
**Quand ?**<br>
Fonctionnalités dynamiques (logs, sécurité, cache…)

```java
interface Coffee {
    int cost();
}

class SimpleCoffee implements Coffee {
    public int cost() { return 5; }
}

class MilkDecorator implements Coffee {
    private final Coffee coffee;
    MilkDecorator(Coffee c) { coffee = c; }
    public int cost() { return coffee.cost() + 2; }
}
```

## Facade
👉 Simplifier l’accès à un système complexe

```java
class Engine { void start() {} }
class Lights { void on() {} }

class CarFacade {
    Engine engine = new Engine();
    Lights lights = new Lights();

    void startCar() {
        engine.start();
        lights.on();
    }
}
```

# 🔄 3. <ins>Behavioral Patterns (comportement)</ins>

## Strategy
👉 Changer un comportement à l’exécution<br>
**Quand ?**<br>
Algorithmes interchangeables.

```java
interface PaymentStrategy {
    void pay(int amount);
}

class CardPayment implements PaymentStrategy {
    public void pay(int amount) {}
}

class CashPayment implements PaymentStrategy {
    public void pay(int amount) {}
}
```

## Observer
👉 Notifier automatiquement des changements<br>
**Quand ?**<br>
Events, listeners, UI, pub/sub.

```java
interface Observer {
    void update();
}

class Subject {
    List<Observer> observers = new ArrayList<>();
    void add(Observer o) { observers.add(o); }
    void notifyAllObservers() {
        observers.forEach(Observer::update);
    }
}
```

## Command
👉 Encapsuler une action dans un objet<br>
**Quand ?**<br>
Undo/Redo, boutons, files de commandes.

```java
interface Command {
    void execute();
}

class Light {
    void on() {}
}

class LightOnCommand implements Command {
    Light light;
    LightOnCommand(Light l) { light = l; }
    public void execute() { light.on(); }
}
```

## Template Method
👉 Définir le squelette d’un algorithme

```java
abstract class Game {
    final void play() {
        start();
        playTurn();
        end();
    }
    abstract void start();
    abstract void playTurn();
    abstract void end();
}
```

# 📌 Les PLUS utilisés en vrai projet Java
Si tu devais retenir 10 essentiels :
- Singleton
- Factory / Abstract Factory
- Builder
- Strategy
- Observer
- Decorator
- Adapter
- Facade
- Command
- Template Method

# Tableau récapitulatif ULTRA-SYNTHÈSE
| Pattern             | Catégorie    | Problème résolu              | Mot-clé à retenir | Exemple concret              |
| ------------------- | ------------ | ---------------------------- | ----------------- | ---------------------------- |
| **Singleton**       | Création     | Une seule instance           | instance unique   | Bean Spring par défaut       |
| **Factory**         | Création     | Centraliser la création      | new caché         | `BeanFactory`                |
| **Builder**         | Création     | Objet complexe               | fluide            | `UriComponentsBuilder`       |
| **Adapter**         | Structure    | Interfaces incompatibles     | adapter           | `HandlerAdapter`             |
| **Decorator**       | Structure    | Ajouter comportement         | wrapper           | `HttpServletRequestWrapper`  |
| **Facade**          | Structure    | Simplifier système           | point d’entrée    | `JdbcTemplate`               |
| **Strategy**        | Comportement | Algorithmes interchangeables | interchangeable   | `PaymentStrategy`            |
| **Observer**        | Comportement | Notification                 | event             | `ApplicationListener`        |
| **Command**         | Comportement | Action encapsulée            | action objet      | `Runnable`, `@Transactional` |
| **Template Method** | Comportement | Squelette commun             | étapes fixes      | `JdbcTemplate`               |

