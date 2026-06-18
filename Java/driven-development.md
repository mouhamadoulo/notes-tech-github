
# SDD, TDD et DDD

Ce tutoriel couvre trois approches complémentaires de l'ingénierie logicielle. Elles ne s'opposent pas : on peut écrire une **spécification** (SDD), la faire piloter l'écriture des **tests** (TDD), et structurer le code métier produit selon le **Domain Driven Design** (DDD). Un fil rouge unique est utilisé dans tous les exemples : un domaine de **commande e-commerce** (`Order`).

---

## Table des matières

1. [Spec Driven Development (SDD)](#1-spec-driven-development-sdd)
2. [Test Driven Development (TDD)](#2-test-driven-development-tdd)
3. [Domain Driven Design (DDD)](#3-domain-driven-design-ddd)
4. [Combiner les trois approches](#4-combiner-les-trois-approches)
5. [Récapitulatif et ressources](#5-récapitulatif-et-ressources)

---

## 1. Spec Driven Development (SDD)

### 1.1 Définition

Le **Spec Driven Development** consiste à écrire d'abord une **spécification exécutable et non ambiguë** du comportement attendu, *avant* d'écrire le code. La spécification devient la source de vérité : elle est lisible par le métier, vérifiable par la machine, et sert de critère d'acceptation.

On distingue trois familles de specs en Java :

| Famille | Outil typique | Forme de la spec |
|---|---|---|
| Spec comportementale (BDD) | Cucumber / Gherkin | Scénarios `Given / When / Then` |
| Spec de contrat (API-first) | OpenAPI, JSON Schema | Description du contrat HTTP |
| Spec de programme (Design by Contract) | Préconditions / postconditions | Invariants dans le code |

### 1.2 Principe clé

> La spécification décrit **quoi** (le comportement observable), jamais **comment** (l'implémentation). Le code est ensuite écrit pour satisfaire la spec.

### 1.3 Exemple : spécification comportementale avec Cucumber (Gherkin)

On commence par écrire la spec en langage naturel structuré. Ce fichier est compréhensible par un analyste métier.

**Fichier `src/test/resources/features/order_discount.feature`**

```gherkin
# language: fr
Fonctionnalité: Application d'une remise sur une commande

  Règle: une remise de 10% est appliquée dès 100€ d'achat

    Scénario: Commande au-dessus du seuil de remise
      Étant donné une commande vide
      Quand j'ajoute un article "Clavier" à 60.00 € en quantité 2
      Alors le total avant remise est 120.00 €
      Et le total après remise est 108.00 €

    Scénario: Commande sous le seuil de remise
      Étant donné une commande vide
      Quand j'ajoute un article "Souris" à 30.00 € en quantité 1
      Alors le total avant remise est 30.00 €
      Et le total après remise est 30.00 €
```

**Configuration Maven (`pom.xml`, extrait)**

```xml
<dependencies>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-java</artifactId>
        <version>7.18.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>io.cucumber</groupId>
        <artifactId>cucumber-junit-platform-engine</artifactId>
        <version>7.18.0</version>
        <scope>test</scope>
    </dependency>
    <dependency>
        <groupId>org.junit.platform</groupId>
        <artifactId>junit-platform-suite</artifactId>
        <version>1.10.2</version>
        <scope>test</scope>
    </dependency>
</dependencies>
```

**Les "step definitions" : le pont entre la spec et le code**

Chaque phrase Gherkin est reliée à une méthode Java. C'est ici que la spec devient *exécutable*.

```java
package shop.order;

import io.cucumber.java.fr.Etantdonné;
import io.cucumber.java.fr.Quand;
import io.cucumber.java.fr.Alors;

import static org.assertj.core.api.Assertions.assertThat;

public class OrderDiscountSteps {

    private Order order;

    @Etantdonné("une commande vide")
    public void uneCommandeVide() {
        // À ce stade, la classe Order n'existe pas encore :
        // la spec PILOTE la création du code.
        order = Order.empty();
    }

    @Quand("j'ajoute un article {string} à {double} € en quantité {int}")
    public void jAjouteUnArticle(String nom, double prix, int quantite) {
        order.addLine(nom, Money.euros(prix), quantite);
    }

    @Alors("le total avant remise est {double} €")
    public void leTotalAvantRemiseEst(double attendu) {
        assertThat(order.subtotal()).isEqualTo(Money.euros(attendu));
    }

    @Alors("le total après remise est {double} €")
    public void leTotalApresRemiseEst(double attendu) {
        assertThat(order.totalWithDiscount()).isEqualTo(Money.euros(attendu));
    }
}
```

**Explication détaillée**

- Le fichier `.feature` est la **spécification** : il définit les critères d'acceptation de façon non technique.
- `OrderDiscountSteps` traduit chaque phrase en appel de méthode. Tant que `Order` n'existe pas, **les scénarios échouent** : c'est normal et voulu.
- L'implémentation de `Order` n'a qu'un seul but : faire passer ces scénarios au vert.
- Avantage majeur : la documentation métier *est* la suite de tests. Elle ne peut pas mentir, car elle s'exécute à chaque build.

### 1.4 Exemple : Design by Contract (spec au niveau du programme)

Le SDD peut aussi s'exprimer comme un **contrat** dans le code lui-même : préconditions, postconditions et invariants. En Java, on les exprime souvent avec des assertions ou des annotations de validation.

```java
package shop.order;

import java.util.Objects;

public final class Quantity {

    private final int value;

    private Quantity(int value) {
        // PRÉCONDITION : le contrat refuse toute donnée invalide à la frontière.
        if (value <= 0) {
            throw new IllegalArgumentException("La quantité doit être strictement positive, reçu : " + value);
        }
        this.value = value;
    }

    public static Quantity of(int value) {
        return new Quantity(value);
    }

    public int value() {
        return value;
    }

    public Quantity plus(Quantity other) {
        // POSTCONDITION implicite : la somme de deux quantités valides reste valide.
        return new Quantity(this.value + other.value);
    }

    @Override
    public boolean equals(Object o) {
        return (o instanceof Quantity q) && q.value == this.value;
    }

    @Override
    public int hashCode() {
        return Objects.hash(value);
    }
}
```

**Explication**

- La **précondition** (`value > 0`) est vérifiée au seul point d'entrée (le constructeur privé). Aucune `Quantity` invalide ne peut exister dans le système.
- C'est une spec *intégrée au type* : le compilateur et le runtime garantissent l'invariant. On parle de *making illegal states unrepresentable*.

---

## 2. Test Driven Development (TDD)

### 2.1 Définition et cycle

Le **TDD** est une discipline où l'on écrit le test **avant** le code de production, par petites itérations. Le cycle est **Red → Green → Refactor** :

1. **Red** : écrire un test qui échoue (le comportement n'existe pas encore).
2. **Green** : écrire le code minimal pour faire passer le test.
3. **Refactor** : améliorer le code sans changer son comportement, les tests servant de filet de sécurité.

> Différence avec le SDD : le SDD spécifie *quoi* construire au niveau métier ; le TDD est une méthode *bas niveau* qui pilote le design des classes pas à pas. Les deux se complètent.

### 2.2 Exemple complet : un calculateur de remise, développé en TDD

On va construire la logique de remise étape par étape. Configuration JUnit 5 :

```xml
<dependency>
    <groupId>org.junit.jupiter</groupId>
    <artifactId>junit-jupiter</artifactId>
    <version>5.10.2</version>
    <scope>test</scope>
</dependency>
<dependency>
    <groupId>org.assertj</groupId>
    <artifactId>assertj-core</artifactId>
    <version>3.25.3</version>
    <scope>test</scope>
</dependency>
```

#### Itération 1 — RED : on écrit le premier test

```java
package shop.pricing;

import org.junit.jupiter.api.Test;
import static org.assertj.core.api.Assertions.assertThat;

class DiscountPolicyTest {

    private final DiscountPolicy policy = new DiscountPolicy();

    @Test
    void aucune_remise_en_dessous_de_100_euros() {
        Money result = policy.apply(Money.euros(80));
        assertThat(result).isEqualTo(Money.euros(80));
    }
}
```

À ce stade, `DiscountPolicy` n'existe pas : **le projet ne compile pas**. C'est l'état "Red".

#### Itération 1 — GREEN : code minimal

```java
package shop.pricing;

public class DiscountPolicy {
    public Money apply(Money amount) {
        return amount; // Implémentation la plus simple qui passe le test
    }
}
```

Le test passe. On résiste à la tentation d'en faire plus que nécessaire.

#### Itération 2 — RED : on ajoute le comportement de remise

```java
@Test
void remise_de_10_pourcent_a_partir_de_100_euros() {
    Money result = policy.apply(Money.euros(100));
    assertThat(result).isEqualTo(Money.euros(90));
}
```

Ce test échoue : `apply(100)` renvoie `100` au lieu de `90`.

#### Itération 2 — GREEN : on généralise juste assez

```java
package shop.pricing;

import java.math.BigDecimal;

public class DiscountPolicy {

    private static final Money THRESHOLD = Money.euros(100);
    private static final BigDecimal RATE = new BigDecimal("0.10");

    public Money apply(Money amount) {
        if (amount.isGreaterThanOrEqualTo(THRESHOLD)) {
            return amount.minus(amount.multipliedBy(RATE));
        }
        return amount;
    }
}
```

Les deux tests passent.

#### Itération 3 — REFACTOR : on nettoie, les tests garantissent la non-régression

Supposons que l'on veuille rendre le taux et le seuil configurables. On refactore en gardant les tests verts :

```java
package shop.pricing;

import java.math.BigDecimal;

public class DiscountPolicy {

    private final Money threshold;
    private final BigDecimal rate;

    // Constructeur par défaut : conserve le comportement testé existant
    public DiscountPolicy() {
        this(Money.euros(100), new BigDecimal("0.10"));
    }

    public DiscountPolicy(Money threshold, BigDecimal rate) {
        this.threshold = threshold;
        this.rate = rate;
    }

    public Money apply(Money amount) {
        return amount.isGreaterThanOrEqualTo(threshold)
                ? amount.minus(amount.multipliedBy(rate))
                : amount;
    }
}
```

**Explication du cycle**

- Chaque test n'ajoute qu'un seul comportement. On n'écrit jamais de code non couvert par un test.
- Le refactoring de l'itération 3 est *sûr* : si une régression était introduite, un test virerait au rouge immédiatement.
- Le design émerge des tests plutôt que d'être deviné à l'avance.

### 2.3 TDD avec des dépendances : les doublures de test (Mockito)

Quand la classe testée dépend d'une collaboration (base de données, service externe), on isole le comportement avec un *mock*.

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.11.0</version>
    <scope>test</scope>
</dependency>
```

```java
package shop.order;

import org.junit.jupiter.api.Test;
import org.junit.jupiter.api.extension.ExtendWith;
import org.mockito.InjectMocks;
import org.mockito.Mock;
import org.mockito.junit.jupiter.MockitoExtension;

import static org.mockito.Mockito.*;
import static org.assertj.core.api.Assertions.assertThat;

@ExtendWith(MockitoExtension.class)
class PlaceOrderServiceTest {

    @Mock
    private OrderRepository repository;          // collaboration simulée

    @Mock
    private PaymentGateway paymentGateway;       // service externe simulé

    @InjectMocks
    private PlaceOrderService service;           // classe sous test

    @Test
    void une_commande_payee_est_sauvegardee() {
        Order order = Order.empty();
        order.addLine("Clavier", Money.euros(120), 1);

        // On définit le comportement attendu de la dépendance
        when(paymentGateway.charge(any(Money.class))).thenReturn(true);

        service.place(order);

        // On vérifie l'interaction attendue (test d'état + test de comportement)
        verify(paymentGateway).charge(Money.euros(108)); // 120 - 10%
        verify(repository).save(order);
        assertThat(order.status()).isEqualTo(OrderStatus.PAID);
    }

    @Test
    void un_paiement_refuse_n_enregistre_pas_la_commande() {
        Order order = Order.empty();
        order.addLine("Souris", Money.euros(20), 1);

        when(paymentGateway.charge(any(Money.class))).thenReturn(false);

        service.place(order);

        verify(repository, never()).save(any()); // jamais sauvegardé
        assertThat(order.status()).isEqualTo(OrderStatus.PAYMENT_FAILED);
    }
}
```

**Explication**

- `@Mock` crée des doublures : `repository` et `paymentGateway` ne touchent ni la base ni un vrai service de paiement. Le test reste rapide et déterministe.
- `when(...).thenReturn(...)` programme la réponse de la dépendance (*stubbing*).
- `verify(...)` vérifie qu'une interaction a (ou n'a pas) eu lieu : c'est du **test de comportement**, utile quand l'effet n'est pas observable via l'état.
- On teste *l'unité* `PlaceOrderService` en isolation totale.

---

## 3. Domain Driven Design (DDD)

### 3.1 Définition

Le **Domain Driven Design** est une approche de conception centrée sur le **domaine métier**. Son idée maîtresse : le code doit refléter fidèlement le langage et les règles des experts métier, plutôt que des structures techniques.

### 3.2 Les briques tactiques du DDD

| Concept | Rôle | Identité ? | Mutable ? |
|---|---|---|---|
| **Value Object** | Décrit une caractéristique (montant, adresse) | Non (égalité par valeur) | Non (immuable) |
| **Entity** | Objet avec une identité propre dans le temps | Oui (égalité par ID) | Oui |
| **Aggregate** | Groupe d'objets traités comme une unité cohérente | Via la racine | Oui, via la racine |
| **Aggregate Root** | Point d'entrée unique d'un agrégat, garant des invariants | Oui | Oui |
| **Repository** | Abstraction de persistance d'un agrégat | — | — |
| **Domain Service** | Logique métier ne tenant dans aucune entité | — | — |
| **Domain Event** | Fait métier qui s'est produit | — | Immuable |

Concepts stratégiques importants : le **Ubiquitous Language** (un vocabulaire partagé entre devs et métier, reflété dans le code) et le **Bounded Context** (frontière à l'intérieur de laquelle un modèle a un sens unique).

### 3.3 Value Object : `Money`

Un *Value Object* est **immuable**, sans identité, et comparé par valeur. `Money` est l'exemple canonique. (Cette classe est aussi celle utilisée dans les sections précédentes.)

```java
package shop.order;

import java.math.BigDecimal;
import java.math.RoundingMode;
import java.util.Currency;
import java.util.Objects;

public final class Money {

    private static final Currency EUR = Currency.getInstance("EUR");

    private final BigDecimal amount;
    private final Currency currency;

    private Money(BigDecimal amount, Currency currency) {
        this.amount = amount.setScale(2, RoundingMode.HALF_EVEN);
        this.currency = currency;
    }

    public static Money euros(double value) {
        return new Money(BigDecimal.valueOf(value), EUR);
    }

    public Money plus(Money other) {
        requireSameCurrency(other);
        return new Money(this.amount.add(other.amount), currency);
    }

    public Money minus(Money other) {
        requireSameCurrency(other);
        return new Money(this.amount.subtract(other.amount), currency);
    }

    public Money multipliedBy(BigDecimal factor) {
        return new Money(this.amount.multiply(factor), currency);
    }

    public Money multipliedBy(int factor) {
        return new Money(this.amount.multiply(BigDecimal.valueOf(factor)), currency);
    }

    public boolean isGreaterThanOrEqualTo(Money other) {
        requireSameCurrency(other);
        return this.amount.compareTo(other.amount) >= 0;
    }

    private void requireSameCurrency(Money other) {
        if (!this.currency.equals(other.currency)) {
            throw new IllegalArgumentException("Devises incompatibles");
        }
    }

    @Override
    public boolean equals(Object o) {
        if (!(o instanceof Money money)) return false;
        // Égalité PAR VALEUR : deux Money de même montant/devise sont égaux
        return amount.compareTo(money.amount) == 0 && currency.equals(money.currency);
    }

    @Override
    public int hashCode() {
        return Objects.hash(amount.stripTrailingZeros(), currency);
    }

    @Override
    public String toString() {
        return amount + " " + currency.getSymbol();
    }
}
```

**Explication**

- `final` sur la classe et sur les champs : l'objet est **immuable**. Toute opération (`plus`, `minus`) retourne une *nouvelle* instance.
- `equals` compare le **montant et la devise**, pas une référence : deux billets de 10 € sont interchangeables.
- On utilise `BigDecimal` et jamais `double` pour l'argent (les `double` introduisent des erreurs d'arrondi). C'est une règle métier matérialisée dans le type.

### 3.4 Entity et Value Object combinés : `OrderLine` et `OrderId`

```java
package shop.order;

import java.util.Objects;
import java.util.UUID;

// Value Object : identifiant typé, immuable, sans logique métier propre
public record OrderId(UUID value) {
    public static OrderId newId() {
        return new OrderId(UUID.randomUUID());
    }
}

// Value Object : une ligne de commande, définie entièrement par ses attributs
public final class OrderLine {

    private final String productName;
    private final Money unitPrice;
    private final int quantity;

    public OrderLine(String productName, Money unitPrice, int quantity) {
        if (quantity <= 0) {
            throw new IllegalArgumentException("Quantité invalide");
        }
        this.productName = Objects.requireNonNull(productName);
        this.unitPrice = Objects.requireNonNull(unitPrice);
        this.quantity = quantity;
    }

    public Money lineTotal() {
        return unitPrice.multipliedBy(quantity);
    }

    public String productName() {
        return productName;
    }
}
```

**Explication**

- `OrderId` utilise un `record` Java : parfait pour un Value Object (immuable, `equals`/`hashCode` générés). Typer l'identifiant (`OrderId` plutôt qu'un simple `UUID`) évite de confondre un ID de commande avec un ID de client.
- `OrderLine` encapsule le calcul `lineTotal()` : la règle « total de ligne = prix unitaire × quantité » vit *dans le domaine*, pas dans une couche service.

### 3.5 Aggregate Root : `Order`

L'agrégat `Order` est le **gardien des invariants**. On ne modifie jamais une `OrderLine` directement de l'extérieur : tout passe par la racine, qui garantit la cohérence.

```java
package shop.order;

import java.util.ArrayList;
import java.util.Collections;
import java.util.List;

public class Order {

    private final OrderId id;
    private final List<OrderLine> lines = new ArrayList<>();
    private OrderStatus status = OrderStatus.DRAFT;
    private final List<DomainEvent> events = new ArrayList<>();

    private final DiscountPolicy discountPolicy;

    private Order(OrderId id, DiscountPolicy discountPolicy) {
        this.id = id;
        this.discountPolicy = discountPolicy;
    }

    public static Order empty() {
        return new Order(OrderId.newId(), new DiscountPolicy());
    }

    // L'unique façon d'ajouter une ligne : la racine contrôle l'invariant
    public void addLine(String productName, Money unitPrice, int quantity) {
        if (status != OrderStatus.DRAFT) {
            throw new IllegalStateException("On ne peut modifier qu'une commande en brouillon");
        }
        lines.add(new OrderLine(productName, unitPrice, quantity));
    }

    public Money subtotal() {
        return lines.stream()
                .map(OrderLine::lineTotal)
                .reduce(Money.euros(0), Money::plus);
    }

    public Money totalWithDiscount() {
        return discountPolicy.apply(subtotal());
    }

    // Transition d'état métier, produisant un événement de domaine
    public void markPaid() {
        if (status != OrderStatus.DRAFT) {
            throw new IllegalStateException("Transition de paiement invalide depuis " + status);
        }
        this.status = OrderStatus.PAID;
        this.events.add(new OrderPaid(id, totalWithDiscount()));
    }

    public void markPaymentFailed() {
        this.status = OrderStatus.PAYMENT_FAILED;
    }

    public OrderStatus status() {
        return status;
    }

    public OrderId id() {
        return id;
    }

    // Exposition en LECTURE SEULE : on protège l'invariant de l'agrégat
    public List<OrderLine> lines() {
        return Collections.unmodifiableList(lines);
    }

    public List<DomainEvent> pullEvents() {
        List<DomainEvent> pulled = List.copyOf(events);
        events.clear();
        return pulled;
    }
}
```

**Les types associés :**

```java
package shop.order;

public enum OrderStatus { DRAFT, PAID, PAYMENT_FAILED }

// Domain Event : fait métier passé, immuable
public interface DomainEvent { }

public record OrderPaid(OrderId orderId, Money amountPaid) implements DomainEvent { }
```

**Explication**

- **Encapsulation forte** : la liste interne `lines` n'est jamais exposée en écriture. `lines()` renvoie une vue non modifiable. Impossible pour un appelant de violer une règle en bidouillant la collection.
- **Invariants centralisés** : `addLine` refuse de modifier une commande déjà payée. La règle est appliquée à l'unique point de mutation.
- **Domain Events** : `markPaid()` enregistre un `OrderPaid`. Le reste du système (envoi d'email, facturation) pourra réagir sans coupler le domaine à l'infrastructure.

### 3.6 Repository : abstraction de persistance

Le **Repository** est défini par une *interface dans la couche domaine*. L'implémentation technique (JPA, JDBC...) vit en dehors, ce qui garde le domaine pur.

```java
package shop.order;

import java.util.Optional;

// Interface dans le DOMAINE : aucune dépendance technique ici
public interface OrderRepository {
    void save(Order order);
    Optional<Order> findById(OrderId id);
}
```

Une implémentation possible (couche infrastructure), simplifiée :

```java
package shop.infrastructure;

import shop.order.Order;
import shop.order.OrderId;
import shop.order.OrderRepository;

import java.util.Map;
import java.util.Optional;
import java.util.concurrent.ConcurrentHashMap;

public class InMemoryOrderRepository implements OrderRepository {

    private final Map<OrderId, Order> store = new ConcurrentHashMap<>();

    @Override
    public void save(Order order) {
        store.put(order.id(), order);
    }

    @Override
    public Optional<Order> findById(OrderId id) {
        return Optional.ofNullable(store.get(id));
    }
}
```

**Explication**

- Le domaine dépend de l'**interface**, jamais de l'implémentation : c'est l'inversion de dépendance. On peut remplacer `InMemoryOrderRepository` par une version JPA sans toucher au domaine.
- Le Repository manipule l'agrégat **entier** (`Order`), pas des fragments : il respecte la frontière de cohérence de l'agrégat.

### 3.7 Domain Service / Application Service : `PlaceOrderService`

Quand une opération coordonne plusieurs collaborateurs (paiement, persistance), elle ne tient dans aucune entité : on en fait un service. (C'est cette classe qui était testée en TDD dans la section 2.3.)

```java
package shop.order;

public class PlaceOrderService {

    private final OrderRepository repository;
    private final PaymentGateway paymentGateway;

    public PlaceOrderService(OrderRepository repository, PaymentGateway paymentGateway) {
        this.repository = repository;
        this.paymentGateway = paymentGateway;
    }

    public void place(Order order) {
        boolean paid = paymentGateway.charge(order.totalWithDiscount());
        if (paid) {
            order.markPaid();      // l'agrégat applique sa propre règle d'état
            repository.save(order);
        } else {
            order.markPaymentFailed();
        }
    }
}
```

```java
package shop.order;

// Port vers un système externe, défini par le domaine
public interface PaymentGateway {
    boolean charge(Money amount);
}
```

**Explication**

- Le service **orchestre** mais ne contient pas de règle métier : décider qu'une commande devient `PAID` reste la responsabilité de l'agrégat (`order.markPaid()`).
- `PaymentGateway` est un **port** : le domaine définit le besoin (« encaisser un montant »), l'infrastructure fournit l'adaptateur. C'est le cœur de l'architecture hexagonale, naturellement compatible avec le DDD.

---

## 4. Combiner les trois approches

Les trois pratiques forment une chaîne cohérente du métier vers le code :

```text
┌─────────────────────────────────────────────────────────────┐
│  SDD   : "Une remise de 10% s'applique dès 100€"             │
│          (spec exécutable Gherkin = critère d'acceptation)   │
└───────────────────────────┬─────────────────────────────────┘
                            │ pilote
┌───────────────────────────▼─────────────────────────────────┐
│  TDD   : Red → Green → Refactor sur DiscountPolicy, Order…   │
│          (tests unitaires qui font émerger le design)        │
└───────────────────────────┬─────────────────────────────────┘
                            │ produit du code structuré selon
┌───────────────────────────▼─────────────────────────────────┐
│  DDD   : Money (Value Object), Order (Aggregate Root),       │
│          OrderRepository (Port), PlaceOrderService…          │
│          (le code reflète le langage métier)                 │
└─────────────────────────────────────────────────────────────┘
```

Workflow concret pour développer une fonctionnalité :

1. **SDD** — Écrire le scénario Gherkin avec le métier. Il échoue (Red au niveau acceptation).
2. **TDD** — Descendre au niveau unitaire : écrire un test unitaire, le rendre vert, refactorer. Répéter jusqu'à ce que le scénario Gherkin passe.
3. **DDD** — Pendant le TDD, nommer et structurer les classes selon le *Ubiquitous Language* : `Money`, `Order`, `DiscountPolicy`, etc. Le design émergent est rangé dans les bonnes briques tactiques.

### 4.1 Test d'acceptation et test unitaire : niveaux différents

| Critère | Test d'acceptation (SDD/Cucumber) | Test unitaire (TDD/JUnit) |
|---|---|---|
| Granularité | Comportement métier complet | Une classe / une méthode |
| Audience | Métier + dev | Dev |
| Vitesse | Plus lent | Très rapide |
| Couplage | Vue boîte noire | Vue boîte blanche |

Les deux sont complémentaires : les tests d'acceptation prouvent qu'on **construit le bon produit** ; les tests unitaires prouvent qu'on **construit le produit correctement**.

---

## 5. Récapitulatif et ressources

### 5.1 Points clés à retenir

- **SDD** : la spécification (Gherkin, OpenAPI, contrats) est écrite en premier et reste exécutable ; elle est la source de vérité partagée entre métier et développeurs.
- **TDD** : discipline Red-Green-Refactor ; on n'écrit jamais de code sans test préalable ; le design émerge des tests ; les doublures (Mockito) isolent l'unité.
- **DDD** : le code reflète le métier ; on distingue Value Objects (immuables, égalité par valeur), Entities et Aggregate Roots (gardiens des invariants), Repositories et Domain Services ; le domaine ne dépend jamais de l'infrastructure.

### 5.2 Anti-patterns à éviter

| Approche | Anti-pattern fréquent |
|---|---|
| SDD | Specs Gherkin qui décrivent l'implémentation (`Quand je clique sur le bouton id=submit`) au lieu du comportement métier. |
| TDD | Écrire le code puis le test (ce n'est plus du TDD) ; tester des détails d'implémentation plutôt que le comportement. |
| DDD | *Anemic Domain Model* : entités sans logique, avec seulement des getters/setters, toute la logique étant dans des services. |

### 5.3 Pour aller plus loin

- *Test-Driven Development: By Example*, Kent Beck — référence fondatrice du TDD.
- *Domain-Driven Design*, Eric Evans — l'ouvrage d'origine (le « Blue Book »).
- *Implementing Domain-Driven Design*, Vaughn Vernon — plus pratique, orienté implémentation.
- *Growing Object-Oriented Software, Guided by Tests*, Freeman & Pryce — la jonction entre TDD et conception dirigée par les tests d'acceptation.
- Documentation Cucumber-JVM, JUnit 5 et Mockito pour les outils Java utilisés ci-dessus.
