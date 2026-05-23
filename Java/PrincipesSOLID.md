# Principes SOLID
Les principes SOLID sont 5 règles de conception en programmation orientée objet qui aident à écrire du code maintenable,<br>
extensible et testable. Ils sont très utilisés en Java et dans les architectures modernes.

# 1. S — Single Responsibility Principle (SRP)
➡️ Une classe doit avoir une seule responsabilité.

Si une classe fait plusieurs choses, elle devient difficile à modifier et à tester.

❌ **Mauvais exemple**
```java
class Report {
    public void generateReport() {
        // logique de génération
    }

    public void saveToFile() {
        // sauvegarde dans un fichier
    }
}
```
La classe génère le rapport et le sauvegarde.

✅ **Bon exemple**
```java
class ReportGenerator {
    public void generateReport() {
        // génération
    }
}

class ReportSaver {
    public void saveToFile() {
        // sauvegarde
    }
}
```
✔ Chaque classe a une seule responsabilité.

# 2. O — Open/Closed Principle (OCP)
➡️ Les classes doivent être ouvertes à l’extension mais fermées à la modification.

On doit pouvoir ajouter du comportement sans modifier le code existant.

❌ **Mauvais exemple**
```java 
class PaymentService {
    public void pay(String type) {
        if(type.equals("card")) {
            System.out.println("Pay by card");
        } else if(type.equals("paypal")) {
            System.out.println("Pay by paypal");
        }
    }
}
```
Si on ajoute ApplePay, il faut modifier la classe.

✅ **Bon exemple**
```java
interface PaymentMethod {
    void pay();
}

class CardPayment implements PaymentMethod {
    public void pay() {
        System.out.println("Pay by card");
    }
}

class PaypalPayment implements PaymentMethod {
    public void pay() {
        System.out.println("Pay by paypal");
    }
}
```
Utilisation :
```java 
class PaymentService {
    public void pay(PaymentMethod method) {
        method.pay();
    }
}
```
✔ Pour ajouter un paiement → nouvelle classe seulement.

# 3. L — Liskov Substitution Principle (LSP)
➡️ Une classe fille doit pouvoir remplacer sa classe mère sans casser le programme.

❌ **Mauvais exemple**
```java
class Bird {
    void fly() {}
}

class Penguin extends Bird {
    void fly() {
        throw new UnsupportedOperationException();
    }
}
```
Un pingouin ne vole pas, donc ça viole LSP.

✅ **Bon exemple**
```java 
class Bird {}

interface FlyingBird {
    void fly();
}

class Sparrow extends Bird implements FlyingBird {
    public void fly() {}
}

class Penguin extends Bird {}
```
✔ On respecte les capacités réelles des objets.

# 4. I — Interface Segregation Principle (ISP)
➡️ Ne pas forcer une classe à implémenter des méthodes qu’elle n’utilise pas.

❌ **Mauvais exemple**
```java 
interface Worker {
    void work();
    void eat();
}

class Robot implements Worker {
    public void work() {}

    public void eat() {
        throw new UnsupportedOperationException();
    }
}
```

✅ **Bon exemple**
```java 
interface Workable {
    void work();
}

interface Eatable {
    void eat();
}

class Human implements Workable, Eatable {
    public void work() {}
    public void eat() {}
}

class Robot implements Workable {
    public void work() {}
}
```
✔ Interfaces plus petites et spécialisées.

# 5. D — Dependency Inversion Principle (DIP)
➡️ Dépendre des abstractions, pas des implémentations.

❌ **Mauvais exemple**
```java
class MySQLDatabase {
    void save(String data) {}
}

class UserService {
    private MySQLDatabase db = new MySQLDatabase();

    void saveUser(String user) {
        db.save(user);
    }
}
```
UserService dépend directement de MySQLDatabase.

✅ **Bon exemple**
```java
interface Database {
    void save(String data);
}

class MySQLDatabase implements Database {
    public void save(String data) {}
}

class UserService {
    private Database db;

    public UserService(Database db) {
        this.db = db;
    }

    void saveUser(String user) {
        db.save(user);
    }
}
```
✔ On peut remplacer MySQL → Mongo → API → Mock test facilement.

# Résumé simple
| Principe | Idée                                        |
| -------- | ------------------------------------------- |
| **S**    | Une classe = une responsabilité             |
| **O**    | Étendre sans modifier                       |
| **L**    | Les sous-classes doivent être substituables |
| **I**    | Interfaces petites et spécifiques           |
| **D**    | Dépendre d'abstractions                     |

✅ Pourquoi SOLID est important :
- code plus maintenable
- moins de bugs
- facile à tester
- architecture évolutive
