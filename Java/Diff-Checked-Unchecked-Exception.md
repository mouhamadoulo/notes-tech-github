En Java, la différence entre une **checked exception** et une **unchecked exception** est principalement liée à la manière dont le compilateur les traite.

| Critère                                       | Checked Exception                     | Unchecked Exception                       |
| --------------------------------------------- | ------------------------------------- | ----------------------------------------- |
| Vérification par le compilateur               | Oui                                   | Non                                       |
| Obligation de gérer (`try-catch` ou `throws`) | Oui                                   | Non                                       |
| Hérite de                                     | `Exception` (hors `RuntimeException`) | `RuntimeException` et ses sous-classes    |
| Cause habituelle                              | Erreurs externes ou récupérables      | Erreurs de programmation ou d'utilisation |

### 1. Checked Exception

Une checked exception **doit être gérée** ou déclarée avec `throws`. Le compilateur génère une erreur si ce n'est pas fait.

Exemple :

```java
import java.io.FileReader;
import java.io.IOException;

public class Exemple {
    public static void main(String[] args) {
        try {
            FileReader fr = new FileReader("test.txt");
        } catch (IOException e) {
            e.printStackTrace();
        }
    }
}
```

Quelques checked exceptions courantes :

* `IOException`
* `SQLException`
* `ClassNotFoundException`

---

### 2. Unchecked Exception

Les unchecked exceptions **ne sont pas vérifiées par le compilateur**. Vous pouvez les intercepter, mais ce n'est pas obligatoire.

Exemple :

```java
public class Exemple {
    public static void main(String[] args) {
        int a = 10;
        int b = 0;

        System.out.println(a / b); // ArithmeticException
    }
}
```

Autres unchecked exceptions fréquentes :

* `NullPointerException`
* `ArrayIndexOutOfBoundsException`
* `IllegalArgumentException`
* `NumberFormatException`

---

### Hiérarchie des exceptions

```
Throwable
├── Error
└── Exception
    ├── RuntimeException
    │   ├── NullPointerException
    │   ├── ArithmeticException
    │   └── IllegalArgumentException
    └── IOException
    └── SQLException
    └── ClassNotFoundException
```

* Les classes qui héritent de **`RuntimeException`** sont des **unchecked exceptions**.
* Les autres classes qui héritent de **`Exception`** (mais pas de `RuntimeException`) sont des **checked exceptions**.

### Quand utiliser l'une ou l'autre ?

* **Checked exception** : lorsque l'erreur est **prévisible et potentiellement récupérable**, par exemple un fichier absent, une erreur réseau ou un problème d'accès à une base de données.
* **Unchecked exception** : lorsqu'il s'agit d'une **erreur de programmation** (paramètre invalide, accès à un objet `null`, index hors limites, etc.), que le développeur doit corriger plutôt que gérer systématiquement.

En résumé :

* ✅ **Checked** = le compilateur vous oblige à gérer l'exception (`try-catch` ou `throws`).
* ✅ **Unchecked** = le compilateur ne vous y oblige pas ; elles signalent généralement des erreurs de programmation.
