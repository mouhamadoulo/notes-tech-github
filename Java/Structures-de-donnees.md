# 🧠 1. <ins>C’est quoi une structure de données ?</ins>
Une structure de données = une façon d’organiser des données en mémoire pour :
- les stocker
- les accéder
- les modifier efficacement

👉 Exemple simple :
- Une liste de courses → liste
- Un dictionnaire → clé → valeur
- Une pile d’assiettes → pile

# 📦 2. <ins>Les structures de données principales en Java</ins>
Java fournit tout dans le package :<br>
👉 ```java.util```

On va voir les plus importantes :<br>
<div align="center">
	<img src="../images/strdata.png" height="500" width="500" />
</div>

# 📚 3. <ins>Liste (ArrayList)</ins>
✔️ **Idée**<br>
Une liste ordonnée d’éléments.

🧾 **Illustration**
```
Index :   0    1    2    3
Valeur: ["A", "B", "C", "D"]
```
👉 Tu accèdes avec un index.

💻 **Exemple Java**
```java 
ArrayList<String> liste = new ArrayList<>();

liste.add("A");
liste.add("B");
liste.add("C");

System.out.println(liste.get(1)); // B
```

⚡ **Caractéristiques**
- Accès rapide ✔️
- Insertion au milieu lente ❌
- Taille dynamique ✔️

# 🔗 4. <ins>Liste chaînée (LinkedList)</ins>
✔️ **Idée**<br>
Chaque élément pointe vers le suivant.

🧾 **Illustration**
```
[A] -> [B] -> [C] -> [D] -> null
```

💻 **Exemple**
```java
LinkedList<String> liste = new LinkedList<>();

liste.add("A");
liste.add("B");
liste.add("C");
```

⚡ **Caractéristiques**
- Insertion rapide ✔️
- Accès lent (parcours) ❌

# 🥞 5. <ins>Pile (Stack)</ins>
✔️ **Idée**<br>
LIFO = Last In, First Out

👉 comme une pile d’assiettes

🧾 **Illustration**
```
    [C]  ← sommet
    [B]
    [A]
```

💻 **Exemple**
```java
Stack<String> pile = new Stack<>();

pile.push("A");
pile.push("B");
pile.push("C");

System.out.println(pile.pop()); // C
```

⚡ **Règles**
- push() → ajouter
- pop() → retirer
- peek() → regarder

# 🚶 6. <ins>File (Queue)</ins>
✔️ **Idée**<br>
FIFO = First In, First Out

👉 comme une file d’attente

🧾 **Illustration**
```
Entrée → [A] [B] [C] → Sortie
```

💻 **Exemple**
```java
Queue<String> file = new LinkedList<>();

file.add("A");
file.add("B");
file.add("C");

System.out.println(file.poll()); // A
```

⚡ **Règles**
- add() → entrer
- poll() → sortir

# 🗂️ 7. <ins>Map (HashMap)</ins>
✔️ **Idée**<br>
Associer une clé → valeur

👉 comme un dictionnaire

🧾 **Illustration**
```
"nom"  → "Alice"
"age"  → 25
```

💻 **Exemple**
```java
HashMap<String, Integer> map = new HashMap<>();

map.put("Alice", 25);
map.put("Bob", 30);

System.out.println(map.get("Alice")); // 25
```

⚡ **Caractéristiques**
- Accès ultra rapide ✔️
- Pas d’ordre ❌

# 🌳 8. <ins>Set (HashSet)</ins>
✔️ **Idée**<br>
Collection sans doublons

🧾 **Illustration**
```
[A, B, C]  ✔
[A, B, A]  ❌ → devient [A, B]
```

💻 **Exemple**
```java
HashSet<String> set = new HashSet<>();

set.add("A");
set.add("B");
set.add("A");

System.out.println(set); // [A, B]
```

# 🧩 9. <ins>Résumé visuel</ins>
| Structure  | Ordre | Doublons   | Accès rapide | Usage            |
| ---------- | ----- | ---------- | ------------ | ---------------- |
| ArrayList  | ✔️    | ✔️         | ✔️           | Liste simple     |
| LinkedList | ✔️    | ✔️         | ❌            | Insertion rapide |
| Stack      | ✔️    | ✔️         | ✔️           | LIFO             |
| Queue      | ✔️    | ✔️         | ✔️           | FIFO             |
| HashMap    | ❌     | clé unique | ✔️           | Dictionnaire     |
| HashSet    | ❌     | ❌          | ✔️           | Unicité          |


# 🎯 10. <ins>Comment choisir ?</ins>
👉 Pose-toi ces questions :
- Besoin d’un ordre ? → ```ArrayList```
- Besoin clé/valeur ? → ```HashMap```
- Pas de doublons ? → ```HashSet```
- Logique pile ? → ```Stack```
- Logique file ? → ```Queue```
- Beaucoup d’insertions ? → ```LinkedList```