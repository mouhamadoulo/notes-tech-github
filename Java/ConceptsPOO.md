# Les Concepts clés de la POO (Programmation Orientée Objet) illustrée en Java 

## Classe  
C'est une description d'objets d'un certain type, comme un modèle commun composé de champs, de méthodes et d'un constructeur

**Exemple**
```java
public class Voiture {

    // Attributs (champs)
    String marque;
    int vitesse;

    // Constructeur
    public Voiture(String marque, int vitesse) {
        this.marque = marque;
        this.vitesse = vitesse;
    }

    // Méthode
    void accelerer() {
        vitesse += 10;
    }
}

```

## Objet  
instance d'une classe

**Exemple**
```java
public class Main {
    public static void main(String[] args) {

        // Création d'un objet Voiture
        Voiture v1 = new Voiture("Toyota", 50);

        System.out.println(v1.marque);   // Toyota
        System.out.println(v1.vitesse);  // 50

        v1.accelerer();
        System.out.println(v1.vitesse);  // 60
    }
}

```
 
## Abstraction 
C'est le principe qui consiste à identifier les caractéristiques et les informations les plus importantes d'un objet.
Elle peut également être définie comme une méthode de représentation des éléments d'une tâche du monde réel 
en tant qu'objets dans un programme. 
L'abstraction est toujours associée à la généralisation de certaines propriétés d'un objet, 
donc l'essentiel est de séparer les informations significatives de l'insignifiant dans le contexte de la tâche à accomplir

**Exemple**
```java
public abstract class Vehicule {

    protected int vitesse;

    public Vehicule(int vitesse) {
        this.vitesse = vitesse;
    }

    // Méthode abstraite (sans implémentation)
    public abstract void seDeplacer();

    public int getVitesse() {
        return vitesse;
    }
}

```

## Encapsulation  
Ce principe stipule que les attributs et le comportement d'un objet sont combinés dans une seule classe, 
L'implémentation interne de l'objet est cachée à l'utilisateur et une interface publique est fournie pour travailler avec l'objet. 
La tâche du programmeur est de déterminer lesquels des attributs et des méthodes d'un objet doivent être accessibles au public, 
et quels sont les détails d'implémentation internes qui doivent être inaccessibles.
L'accès à l'état et au comportement d'un objet est contrôlé à l'aide de modificateurs appliqués aux champs et aux méthodes : privé, protégé, public et par défaut.
(private, protected, public , defaut)

**Exemple**
```java
public class CompteBancaire {

    // Attribut privé (inaccessible directement)
    private double solde;

    public CompteBancaire(double soldeInitial) {
        this.solde = soldeInitial;
    }

    // Méthode publique pour accéder au solde
    public double getSolde() {
        return solde;
    }

    // Méthode publique contrôlée
    public void deposer(double montant) {
        if (montant > 0) {
            solde += montant;
        }
    }
}

```
		
## Héritage 
En programmation, l'héritage consiste à utiliser des classes existantes pour en définir de nouvelles.
La nouvelle classe hérite des propriétés et méthodes de la classe parente, et peut ajouter ou modifier ceux-ci.
L'héritage définit des relations hiérarchiques entre les classes, de sorte que les attributs et méthodes communs peuvent être réutilisés. 
En définissant des attributs et des comportements de base dans une classe, il est possible de créer des classes secondaires qui étendent 
la fonctionnalité de la classe principale et ajoutent des attributs et des comportements supplémentaires. C'est l'une des clés de la programmation orientée objet.

**Exemple**
```java
public class Voiture extends Vehicule {

    public Voiture(int vitesse) {
        super(vitesse);
    }

    @Override
    public void seDeplacer() {
        System.out.println("La voiture roule à " + vitesse + " km/h");
    }
}

```
```java
public class Avion extends Vehicule {

    public Avion(int vitesse) {
        super(vitesse);
    }

    @Override
    public void seDeplacer() {
        System.out.println("L'avion vole à " + vitesse + " km/h");
    }
}

```

## Polymorphisme 
Le polymorphisme est un concept clé en programmation orientée objet (POO) qui permet aux objets de différentes classes 
d'utiliser des méthodes portant le même nom mais de se comporter de manière différente selon la classe à laquelle ils appartiennent. 
Ce concept permet une interaction plus flexible et plus intuitive avec les objets dans un système informatique.

**Exemple**
```java
public class Main {
    public static void main(String[] args) {

        Vehicule v1 = new Voiture(80);
        Vehicule v2 = new Avion(600);

        v1.seDeplacer(); // La voiture roule à 80 km/h
        v2.seDeplacer(); // L'avion vole à 600 km/h
    }
}

```

## Interface
👉 Une interface définit ce qu’une classe doit faire, sans dire comment.
```java
public interface Volant {
    void voler();
}

```
```java
public class Avion implements Volant {

    @Override
    public void voler() {
        System.out.println("L'avion vole dans le ciel");
    }
}
```
✔ Favorise l'abstraction<br>
✔ Supporte le polymorphisme<br>
✔ Remplace l'héritage multiple en Java

## Méthodes abstraites et concrètes
👉 Une classe abstraite peut contenir les deux.
```java
public abstract class Animal {

    public abstract void faireDuBruit(); // abstraite

    public void dormir() { // concrète
        System.out.println("L'animal dort");
    }
}

```
```java
public class Chien extends Animal {

    @Override
    public void faireDuBruit() {
        System.out.println("Le chien aboie");
    }
}

```

## Redéfinition (Override)
👉 Permet à une classe fille de modifier un comportement hérité.
```java
public class Chat extends Animal {

    @Override
    public void faireDuBruit() {
        System.out.println("Le chat miaule");
    }
}

```
✔ Décision prise à l’exécution<br>
✔ Essentiel pour le polymorphisme

## Surcharge (Overloading)
👉 Plusieurs méthodes même nom, paramètres différents.
```java
public class Calculatrice {

    int addition(int a, int b) {
        return a + b;
    }

    double addition(double a, double b) {
        return a + b;
    }
}

```
✔ Décision prise à la compilation<br>
✔ Améliore la lisibilité

## Composition (HAS-A)
👉 Une classe contient une autre classe.
```java
public class Moteur {
    void demarrer() {
        System.out.println("Moteur démarré");
    }
}

```
```java
public class Voiture {

    private Moteur moteur = new Moteur();

    void demarrerVoiture() {
        moteur.demarrer();
    }
}

```
✔ Préférée à l’héritage dans beaucoup de cas <br>
✔ Relation "a un" et non "est un"

## Association / Agrégation
**Association**
```java
public class Professeur {
    String nom;
}

```
```java
public class Ecole {
    Professeur prof;
}

```
**Agrégation (forme faible de composition)**
```java
public class Equipe {
    List<Joueur> joueurs;
}

```
✔ Les objets peuvent exister indépendamment

## Couplage faible
👉 Les classes doivent dépendre d’abstractions, pas de classes concrètes.
```java
public interface Paiement {
    void payer();
}

```
```java
public class CarteBancaire implements Paiement {
    public void payer() {
        System.out.println("Paiement par carte");
    }
}

```
```java
public class Boutique {

    private Paiement paiement;

    public Boutique(Paiement paiement) {
        this.paiement = paiement;
    }

    public void effectuerPaiement() {
        paiement.payer();
    }
}

```
✔ Code plus flexible<br>
✔ Facile à modifier ou tester

## Classes immuables
👉 Un objet ne peut pas être modifié après création.
```java
public final class Point {

    private final int x;
    private final int y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public int getX() { return x; }
    public int getY() { return y; }
}

```
✔ Sécurité<br>
✔ Très utilisé (String, Integer…)

## Méthodes et classes final
👉 Empêche l’héritage ou la modification.
```java
public final class Utils {

    public static void afficher() {
        System.out.println("Utilitaire");
    }
}

```
✔ Sécurité<br>
✔ Performance<br>
✔ Clarté du design

## Principe de responsabilité unique (SRP)
👉 Une classe = une seule responsabilité

❌ Mauvais :
```java
class Utilisateur {
    void sauvegarderBDD() {}
    void envoyerEmail() {}
}
```

✅ Bon :
```java
class Utilisateur {}
class UtilisateurRepository {}
class EmailService {}
```