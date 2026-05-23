# Python
Voici un guide complet mais essentiel pour bien démarrer avec Python, <br>
depuis l’installation jusqu’aux concepts avancés comme la programmation orientée objet (POO), <br>
avec des exemples clairs.

# 🐍 1. <ins>Qu’est-ce que Python ?</ins>
Python est un langage :
- simple à lire et écrire
- très utilisé (web, data, IA, scripts, automatisation)
- interprété (pas besoin de compilation)

# ⚙️ 2. <ins>Installation de Python</ins>
Sur Windows / Mac / Linux

**1.Va sur : Python.org**<br>
**2.Télécharge la dernière version**<br>
**3.Pendant l’installation :**<br>
✅ coche "Add Python to PATH"

✔️ *Vérifier l’installation*<br>
Dans le terminal :
```bash
python --version
```

# 🧠 3. <ins>Bases du langage</ins>

## 🔹 Variables
```python
nom = "Alice"
age = 25
prix = 19.99
```

## 🔹 Types principaux
```python
int      # entier
float    # nombre décimal
str      # texte
bool     # True / False
```

## 🔹 Affichage
```python
print("Bonjour")
```

# 🔀 4. <ins>Conditions</ins>
```python 
age = 18

if age >= 18:
    print("Majeur")
else:
    print("Mineur")
```

# 🔁 5. <ins>Boucles</ins>

## Boucle for
```python
for i in range(5):
    print(i)
```

## Boucle while
```python
x = 0
while x < 5:
    print(x)
    x += 1
```

# 📦 6. <ins>Structures de données</ins>

## 🔹 Liste
```python
fruits = ["pomme", "banane", "orange"]
print(fruits[0])
```

## 🔹 Dictionnaire
```python
personne = {
    "nom": "Alice",
    "age": 25
}
print(personne["nom"])
```

## 🔹 Tuple (immuable)
```python
coord = (10, 20)
```

# 🔧 7. <ins>Fonctions</ins>
```python 
def dire_bonjour(nom):
    return "Bonjour " + nom

print(dire_bonjour("Alice"))
```

# 📁 8. <ins>Modules et imports</ins>
```python
import math

print(math.sqrt(16))
```

# ⚡ 9. <ins>Gestion des erreurs</ins>
```python
try:
    x = int("abc")
except:
    print("Erreur de conversion")
```

# 🧱 10. <ins>Programmation Orientée Objet (POO)</ins>

## 🔹 Concept : Classe et objet
```python
class Personne:
    def __init__(self, nom, age):
        self.nom = nom
        self.age = age

    def se_presenter(self):
        print(f"Je m'appelle {self.nom} et j'ai {self.age} ans")

# création d'objet
p1 = Personne("Alice", 25)
p1.se_presenter()
```

## 🔹 Encapsulation
```python
class Compte:
    def __init__(self, solde):
        self._solde = solde  # protégé

    def deposer(self, montant):
        self._solde += montant
```

## 🔹 Héritage
```python
class Animal:
    def parler(self):
        print("Son générique")

class Chien(Animal):
    def parler(self):
        print("Wouf")

c = Chien()
c.parler()
```

## 🔹 Polymorphisme
```python
for animal in [Chien(), Animal()]:
    animal.parler()
```

# 📌 11. <ins>Concepts essentiels à retenir</ins>

## ✔️ Indentation obligatoire
Python utilise les espaces :
```python
if True:
    print("ok")
```

## ✔️ Typage dynamique
Pas besoin de préciser le type :
```python
x = 10
x = "texte"
```

## ✔️ Tout est objet
Même les nombres et fonctions