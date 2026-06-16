# Concurrence, Multithreading, JVM et Gestion Mémoire

---

# Introduction

Java a été conçu dès l'origine pour exécuter plusieurs tâches simultanément tout en offrant une gestion automatique de la mémoire. Pour maîtriser les performances d'une application Java, il est indispensable de comprendre :

1. La **concurrence et le multithreading**
2. L'architecture de la **JVM**
3. Le fonctionnement du **Garbage Collector**
4. Les techniques de **gestion mémoire et d'optimisation**

---

# Partie I — Concurrence et Multithreading

---

## 1. Processus vs Thread

### Processus

Un processus est une instance d'un programme en cours d'exécution.

Exemple :

* Chrome = plusieurs processus
* IntelliJ = un processus
* JVM = un processus

Chaque processus possède :

* sa mémoire
* ses ressources système
* son espace d'adressage

---

### Thread

Un thread est une unité d'exécution à l'intérieur d'un processus.

Dans une JVM :

```text
Processus JVM
│
├── Thread principal (main)
├── Thread Garbage Collector
├── Thread JIT Compiler
├── Thread utilisateur 1
├── Thread utilisateur 2
└── ...
```

Tous les threads :

* partagent le Heap
* possèdent leur propre Stack

---

## 2. Pourquoi utiliser plusieurs threads ?

### Exemple

Téléchargement d'un fichier :

```java
downloadFile();
```

Version mono-thread :

```text
Téléchargement
↓
Analyse
↓
Affichage
```

Version multi-thread :

```text
Thread 1 → téléchargement
Thread 2 → analyse
Thread 3 → affichage
```

Avantages :

* meilleure réactivité
* utilisation optimale des CPU multicœurs
* parallélisation

---

## 3. Création d'un Thread

### Hériter de Thread

```java
class MyThread extends Thread {
    public void run() {
        System.out.println("Hello");
    }
}
```

Utilisation :

```java
MyThread t = new MyThread();
t.start();
```

---

### Implémenter Runnable

Méthode recommandée :

```java
class Task implements Runnable {

    @Override
    public void run() {
        System.out.println("Task");
    }
}
```

```java
Thread t = new Thread(new Task());
t.start();
```

---

### Avec Lambda

```java
Thread t = new Thread(() -> {
    System.out.println("Hello");
});

t.start();
```

---

## 4. Cycle de vie d'un Thread

```text
NEW
 │
 ▼
RUNNABLE
 │
 ▼
RUNNING
 │
 ├── WAITING
 ├── BLOCKED
 │
 ▼
TERMINATED
```

États :

| État       | Description   |
| ---------- | ------------- |
| NEW        | Créé          |
| RUNNABLE   | Prêt          |
| RUNNING    | En exécution  |
| BLOCKED    | Verrou occupé |
| WAITING    | En attente    |
| TERMINATED | Terminé       |

---

# 5. Les problèmes de concurrence

---

## Race Condition

Deux threads modifient une même donnée.

```java
counter++;
```

Ce n'est pas atomique.

Décomposition :

```text
Lire counter
+1
Réécrire counter
```

Deux threads peuvent écraser leurs résultats.

---

### Exemple

```java
class Counter {

    int value = 0;

    void increment() {
        value++;
    }
}
```

Résultat attendu :

```text
100000
```

Résultat réel :

```text
87321
```

---

# 6. Synchronization

---

## synchronized

Permet de verrouiller une ressource.

```java
public synchronized void increment() {
    value++;
}
```

Ou :

```java
synchronized(this) {
    value++;
}
```

---

## Monitor

Chaque objet Java possède un monitor.

```java
synchronized(obj) {
    // section critique
}
```

Un seul thread peut entrer.

---

# 7. Volatile

---

## Problème de visibilité

CPU modernes :

```text
RAM
↓
Cache CPU
```

Un thread peut ne pas voir les modifications d'un autre.

---

### Solution

```java
private volatile boolean running;
```

Garantit :

* visibilité mémoire
* lecture depuis la mémoire principale

Ne garantit PAS :

* l'atomicité

---

# 8. Atomic Classes

Package :

```java
java.util.concurrent.atomic
```

Exemple :

```java
AtomicInteger counter =
        new AtomicInteger();
```

```java
counter.incrementAndGet();
```

Sans verrou explicite.

---

# 9. Executor Framework

---

## Pourquoi ?

Créer des threads coûte cher.

Mauvaise pratique :

```java
for(int i=0;i<10000;i++) {
    new Thread(task).start();
}
```

---

### Pool de threads

```java
ExecutorService executor =
    Executors.newFixedThreadPool(4);
```

```java
executor.submit(task);
```

```java
executor.shutdown();
```

---

# 10. Future

---

Permet de récupérer un résultat.

```java
Future<Integer> future =
    executor.submit(() -> 42);
```

```java
Integer result = future.get();
```

---

# 11. CompletableFuture

Introduit en Java 8.

```java
CompletableFuture.supplyAsync(() -> {
    return calculate();
});
```

Chaînage :

```java
future
    .thenApply(x -> x * 2)
    .thenAccept(System.out::println);
```

Très utilisé dans :

* APIs asynchrones
* microservices
* applications réactives

---

# Partie II — Architecture JVM

---

# 1. Qu'est-ce que la JVM ?

Java Virtual Machine.

Rôle :

```text
Code Java
↓
Bytecode (.class)
↓
JVM
↓
Machine physique
```

Avantage :

```text
Write Once
Run Anywhere
```

---

# Architecture simplifiée

```text
                JVM
 ┌─────────────────────────┐
 │ Class Loader            │
 ├─────────────────────────┤
 │ Runtime Data Areas      │
 ├─────────────────────────┤
 │ Execution Engine        │
 └─────────────────────────┘
```

---

# 2. Heap

---

## Définition

Zone mémoire des objets.

```java
Person p = new Person();
```

```text
Stack → référence p
Heap → objet Person
```

---

### Caractéristiques

Partagée par tous les threads.

```text
Thread A
Thread B
Thread C
     ↓
    Heap
```

---

## Générations

```text
Heap
│
├── Young Generation
│    ├── Eden
│    ├── Survivor S0
│    └── Survivor S1
│
└── Old Generation
```

---

### Eden

Nouvel objet :

```java
new User();
```

→ Eden

---

### Survivor

Objets survivants après un GC.

---

### Old Generation

Objets longévifs.

```text
Cache
Singleton
Configuration
```

---

# 3. Stack

---

Chaque thread possède sa propre stack.

```text
Thread 1 Stack
Thread 2 Stack
Thread 3 Stack
```

---

## Stack Frame

Appel :

```java
foo();
```

Crée :

```text
Frame foo
```

Contient :

* variables locales
* paramètres
* adresse de retour

---

Exemple :

```java
void foo() {
    int x = 10;
}
```

```text
Stack Frame
├── x = 10
```

---

## StackOverflowError

Récursion infinie :

```java
void recurse() {
    recurse();
}
```

```text
StackOverflowError
```

---

# 4. Metaspace

Depuis Java 8.

Stocke :

* métadonnées des classes
* méthodes
* constantes

Avant Java 8 :

```text
PermGen
```

Après Java 8 :

```text
Metaspace
```

---

# 5. Execution Engine

---

## Interpréteur

Exécute le bytecode.

```text
Bytecode
↓
Machine Code
```

---

## JIT Compiler

Just-In-Time Compiler.

Compile les portions fréquemment utilisées.

```text
Méthode chaude
↓
Code natif
```

Gain énorme en performance.

---

# Partie III — Garbage Collector

---

# 1. Pourquoi un GC ?

En C :

```c
malloc()
free()
```

Java :

```java
new User();
```

La JVM récupère automatiquement la mémoire.

---

# 2. Objet éligible au GC

```java
User u = new User();

u = null;
```

Plus aucune référence.

```text
Objet → GC Candidate
```

---

# 3. Mark and Sweep

---

## Étape 1 : Mark

```text
Root
 │
 ▼
Objet A
 │
 ▼
Objet B
```

Marqués comme vivants.

---

## Étape 2 : Sweep

```text
Objets non marqués
↓
Suppression
```

---

# 4. GC Roots

Point de départ :

* variables locales
* threads
* classes statiques
* références JNI

---

# 5. Stop-The-World

Pendant certaines phases :

```text
Application
↓
Pause
↓
GC
↓
Reprise
```

Objectif :

Réduire les pauses.

---

# 6. Collecteurs modernes

---

## Serial GC

```text
1 thread GC
```

Simple.

---

## Parallel GC

```text
N threads GC
```

Optimisé débit.

---

## G1 GC (défaut moderne)

Découpe le heap :

```text
Region 1
Region 2
Region 3
...
```

Avantages :

* pauses prévisibles
* gros heaps

---

## ZGC

Très faible latence.

Pauses :

```text
< 1 ms
```

Pour :

* applications temps réel
* très gros heaps

---

## Shenandoah

Objectif :

```text
GC concurrent
```

Réduction maximale des pauses.

---

# Partie IV — Gestion mémoire et optimisation

---

# 1. Fuites mémoire (Memory Leaks)

Même avec GC.

---

### Exemple

```java
List<Object> cache =
    new ArrayList<>();
```

```java
cache.add(bigObject);
```

Jamais supprimé.

Le GC ne pourra jamais le récupérer.

---

# 2. Mauvais Singleton

```java
public class Cache {

    static List<Object> data =
            new ArrayList<>();
}
```

Références permanentes.

---

# 3. WeakReference

---

Permet au GC de supprimer l'objet.

```java
WeakReference<User> ref =
    new WeakReference<>(user);
```

Utilisé dans :

* caches
* frameworks

---

# 4. Profilage mémoire

Outils :

* VisualVM
* JProfiler
* YourKit
* Java Mission Control

Permettent :

* analyse heap
* détection de leaks
* suivi GC

---

# 5. Optimisations du Heap

---

## Taille initiale

```bash
-Xms512m
```

---

## Taille maximale

```bash
-Xmx4g
```

---

Exemple :

```bash
java -Xms2g -Xmx8g app.jar
```

---

# 6. Réduction des allocations

Mauvais :

```java
for(int i=0;i<1000000;i++) {
    String s = new String("abc");
}
```

---

Meilleur :

```java
String s = "abc";
```

---

# 7. StringBuilder

Mauvais :

```java
String result = "";

for(int i=0;i<1000;i++)
    result += i;
```

Complexité :

```text
O(n²)
```

---

Bon :

```java
StringBuilder sb =
        new StringBuilder();

for(int i=0;i<1000;i++)
    sb.append(i);
```

---

# 8. Object Pooling

Réutilisation d'objets coûteux.

Exemple :

* connexions BD
* threads

```java
Connection Pool
Thread Pool
```

---

# 9. Bonnes pratiques de performance

### Concurrence

✅ Utiliser `ExecutorService`

✅ Utiliser `CompletableFuture`

✅ Limiter les verrous

✅ Utiliser les structures concurrentes :

```java
ConcurrentHashMap
BlockingQueue
```

---

### Mémoire

✅ Réduire les allocations inutiles

✅ Éviter les objets temporaires

✅ Utiliser `StringBuilder`

✅ Surveiller le Heap

✅ Détecter les memory leaks

---

### JVM

✅ Configurer correctement :

```bash
-Xms
-Xmx
```

✅ Choisir le GC adapté :

| Cas                 | GC       |
| ------------------- | -------- |
| Application simple  | G1       |
| Très faible latence | ZGC      |
| Débit maximal       | Parallel |
| Petites JVM         | Serial   |

---

# Schéma global à retenir

```text
                 JVM
┌─────────────────────────────────┐
│                                 │
│           Heap                  │
│                                 │
│  Young Gen  →  Old Gen          │
│                                 │
└─────────────────────────────────┘
           ▲
           │
       Garbage
       Collector

──────────────────────────────────

Thread 1
   └── Stack

Thread 2
   └── Stack

Thread 3
   └── Stack

──────────────────────────────────

Concurrence
│
├── Thread
├── Runnable
├── ExecutorService
├── Future
├── CompletableFuture
├── synchronized
├── volatile
└── AtomicInteger
```

## Conclusion

Pour être performant en Java, il faut comprendre que :

* **Les objets vivent dans le Heap**, partagé entre tous les threads.
* **Chaque thread possède sa Stack**, où résident les appels de méthodes et variables locales.
* **Le Garbage Collector gère automatiquement la mémoire**, mais ne supprime pas les objets encore référencés.
* **La concurrence permet d'exploiter plusieurs cœurs CPU**, mais introduit des risques (race conditions, deadlocks, problèmes de visibilité).
* Les outils modernes comme **ExecutorService**, **CompletableFuture**, **G1GC** et **ZGC** sont aujourd'hui au cœur des applications Java performantes et scalables.
