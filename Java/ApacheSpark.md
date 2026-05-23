# 🚀 C’est quoi Apache Spark ?
Apache Spark est une librairie de calcul distribué pour traiter de très gros volumes de données, en mémoire, beaucoup plus vite que Hadoop MapReduce.

👉 En résumé :
- Traite Big Data
- Distribué (cluster de machines)
- In-memory (rapide)
- API en Java, Scala, Python, SQL

# 🧠 À quoi ça sert concrètement ?
- Traitement de fichiers énormes (logs, CSV, JSON…)
- Agrégations (count, sum, avg…)
- ETL (extract → transform → load)
- Machine Learning (Spark MLlib)
- Streaming (Spark Streaming)

# 🏗️ Architecture simplifiée
```bash
Driver (ton programme Java)
   |
   | envoie des jobs
   v
Executors (machines du cluster)
   |
   | traitent les données
   v
RAM (calcul en mémoire)
```

# 🧩 Concept clé n°1 : SparkSession
👉 Point d’entrée de Spark
```java
SparkSession spark = SparkSession.builder()
        .appName("DemoSpark")
        .master("local[*]")
        .getOrCreate();
```
local[*] = utilise tous les cœurs de ta machine (mode local).

# 🧩 Concept clé n°2 : Dataset / DataFrame
Spark travaille sur des collections distribuées.

🔹 Exemple : Lire un fichier texte et compter les lignes

```java
Dataset<String> lines = spark.read()
        .textFile("data.txt");

long count = lines.count();
System.out.println(count);
```
➡️ Le fichier peut faire 100 Go, Spark s’en fout : c’est distribué.

# 🧩 Concept clé n°3 : Transformations vs Actions
🔄 Transformations (lazy) -> ne déclenchent PAS de calcul
```java
Dataset<String> filtered = lines.filter(l -> l.contains("ERROR"));
```

⚡ Actions -> déclenchent réellement le calcul
```java
filtered.show();
filtered.count();
```

📌 Spark est lazy → super performant.

# Quelques Exemples

## Exemple : Word Count (Le classique)
```java
Dataset<String> words = spark.read()
        .textFile("text.txt")
        .flatMap(line -> Arrays.asList(line.split(" ")).iterator());

Dataset<Row> wordCount = words
        .groupBy("value")
        .count();

wordCount.show();

```

## Exemple : Lecture d’un CSV + filtrage
```java
Dataset<Row> users = spark.read()
        .option("header", true)
        .csv("users.csv");

users.filter("age > 18")
     .select("name", "age")
     .show();

```
➡️ API SQL-like, très lisible.

## Exemple : API SQL
```java
users.createOrReplaceTempView("users");

spark.sql("SELECT name FROM users WHERE age > 18")
     .show();

```
👉 Tu peux mixer Java + SQL.

## Exemple : Map / Reduce (style fonctionnel)
```java
Dataset<Integer> numbers = spark.createDataset(
        List.of(1, 2, 3, 4, 5),
        Encoders.INT()
);

int sum = numbers.reduce(Integer::sum);
System.out.println(sum);

```

# 🧩 Spark vs Java classique

| Java classique     | Spark                     |
| ------------------ | ------------------------- |
| Liste en mémoire   | Données distribuées       |
| Boucles `for`      | `map`, `filter`, `reduce` |
| Mono-machine       | Cluster                   |
| Lent sur gros data | Très rapide               |

# 📌 Quand utiliser Spark (et quand NON)
✅ Utilise Spark si :
- Données > plusieurs Go
- Besoin de scalabilité
- Calculs analytiques lourds

❌ N’utilise PAS Spark si :
- CRUD classique
- APIs REST
- Données petites ➡️ Spring Boot suffit largement 😉
