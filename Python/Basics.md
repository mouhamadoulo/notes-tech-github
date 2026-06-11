# Tutoriel complet Python (sans programmation orientée objet)

## Introduction

Python est un langage de programmation simple à apprendre, très utilisé dans :

- Le développement web
- L'analyse de données
- L'automatisation de tâches
- L'intelligence artificielle
- La cybersécurité
- Les scripts système

Ce tutoriel couvre les bases essentielles de Python **sans utiliser la programmation orientée objet (POO)**.

---

# 1. Afficher du texte

La fonction `print()` permet d'afficher du contenu dans la console.

```python
print("Bonjour le monde")
```

Résultat :

```text
Bonjour le monde
```

Afficher plusieurs éléments :

```python
nom = "Alice"
age = 25

print("Nom :", nom)
print("Age :", age)
```

---

# 2. Les variables

Une variable permet de stocker une valeur.

```python
nom = "Alice"
age = 25
taille = 1.72
```

Python détermine automatiquement le type.

```python
print(type(nom))
print(type(age))
print(type(taille))
```

Résultat :

```text
<class 'str'>
<class 'int'>
<class 'float'>
```

---

# 3. Les types de données principaux

## Chaînes de caractères (str)

```python
prenom = "Jean"
```

## Entiers (int)

```python
age = 30
```

## Nombres décimaux (float)

```python
prix = 19.99
```

## Booléens (bool)

```python
est_majeur = True
est_connecte = False
```

---

# 4. Les commentaires

Les commentaires ne sont pas exécutés.

```python
# Ceci est un commentaire
print("Bonjour")
```

Commentaire sur plusieurs lignes :

```python
"""
Commentaire
sur plusieurs lignes
"""
```

---

# 5. Saisie utilisateur

La fonction `input()` permet de récupérer une valeur saisie.

```python
nom = input("Entrez votre nom : ")

print("Bonjour", nom)
```

Exemple :

```text
Entrez votre nom : Alice
Bonjour Alice
```

### Conversion en nombre

Par défaut, `input()` retourne une chaîne.

```python
age = int(input("Votre âge : "))

print(age + 1)
```

---

# 6. Les opérateurs

## Addition

```python
a = 10
b = 5

print(a + b)
```

Résultat :

```text
15
```

## Soustraction

```python
print(a - b)
```

## Multiplication

```python
print(a * b)
```

## Division

```python
print(a / b)
```

## Division entière

```python
print(a // b)
```

## Modulo (reste)

```python
print(a % b)
```

## Puissance

```python
print(a ** b)
```

---

# 7. Comparaisons

```python
a = 10
b = 5

print(a == b)
print(a != b)
print(a > b)
print(a < b)
print(a >= b)
print(a <= b)
```

Résultat :

```text
False
True
True
False
True
False
```

---

# 8. Opérateurs logiques

## AND

```python
age = 20

print(age >= 18 and age <= 65)
```

Les deux conditions doivent être vraies.

---

## OR

```python
print(age < 18 or age > 65)
```

Une seule condition vraie suffit.

---

## NOT

```python
est_connecte = False

print(not est_connecte)
```

Résultat :

```text
True
```

---

# 9. Conditions

## if

```python
age = 20

if age >= 18:
    print("Majeur")
```

---

## if / else

```python
age = 16

if age >= 18:
    print("Majeur")
else:
    print("Mineur")
```

---

## if / elif / else

```python
note = 15

if note >= 16:
    print("Très bien")
elif note >= 12:
    print("Bien")
elif note >= 10:
    print("Passable")
else:
    print("Échec")
```

---

# 10. Les chaînes de caractères

## Concaténation

```python
prenom = "Jean"
nom = "Dupont"

complet = prenom + " " + nom

print(complet)
```

---

## Répétition

```python
print("ha" * 3)
```

Résultat :

```text
hahaha
```

---

## Longueur

```python
texte = "Bonjour"

print(len(texte))
```

---

## Mise en majuscule

```python
print(texte.upper())
```

---

## Mise en minuscule

```python
print(texte.lower())
```

---

## Remplacement

```python
texte = "Bonjour"

print(texte.replace("Bonjour", "Salut"))
```

---

# 11. Les listes

Une liste permet de stocker plusieurs valeurs.

```python
fruits = ["pomme", "banane", "orange"]
```

---

## Accéder à un élément

```python
print(fruits[0])
```

Résultat :

```text
pomme
```

---

## Modifier un élément

```python
fruits[1] = "kiwi"
```

---

## Ajouter un élément

```python
fruits.append("fraise")
```

---

## Supprimer un élément

```python
fruits.remove("orange")
```

---

## Taille d'une liste

```python
print(len(fruits))
```

---

# 12. Les boucles

## Boucle while

```python
i = 1

while i <= 5:
    print(i)
    i += 1
```

Résultat :

```text
1
2
3
4
5
```

---

## Boucle for

```python
for i in range(5):
    print(i)
```

Résultat :

```text
0
1
2
3
4
```

---

## Intervalle personnalisé

```python
for i in range(1, 6):
    print(i)
```

---

## Parcourir une liste

```python
fruits = ["pomme", "banane", "orange"]

for fruit in fruits:
    print(fruit)
```

---

# 13. break et continue

## break

Interrompt la boucle.

```python
for i in range(10):
    if i == 5:
        break

    print(i)
```

---

## continue

Passe à l'itération suivante.

```python
for i in range(10):
    if i == 5:
        continue

    print(i)
```

---

# 14. Les fonctions

Une fonction permet de réutiliser du code.

```python
def dire_bonjour():
    print("Bonjour")
```

Appel :

```python
dire_bonjour()
```

---

## Fonction avec paramètre

```python
def saluer(nom):
    print("Bonjour", nom)
```

Utilisation :

```python
saluer("Alice")
```

---

## Fonction avec retour

```python
def addition(a, b):
    return a + b
```

Utilisation :

```python
resultat = addition(5, 3)

print(resultat)
```

---

# 15. Les dictionnaires

Un dictionnaire stocke des paires clé/valeur.

```python
personne = {
    "nom": "Alice",
    "age": 25
}
```

---

## Lecture

```python
print(personne["nom"])
```

---

## Modification

```python
personne["age"] = 26
```

---

## Ajout

```python
personne["ville"] = "Paris"
```

---

## Parcours

```python
for cle, valeur in personne.items():
    print(cle, valeur)
```

---

# 16. Les tuples

Les tuples ressemblent aux listes mais sont immuables.

```python
coordonnees = (10, 20)

print(coordonnees[0])
```

---

# 17. Gestion des erreurs

Sans gestion :

```python
nombre = int(input("Nombre : "))
```

Si l'utilisateur saisit du texte :

```text
ValueError
```

Avec gestion :

```python
try:
    nombre = int(input("Nombre : "))
    print(nombre)

except ValueError:
    print("Veuillez entrer un nombre valide")
```

---

# 18. Lecture de fichiers

## Lire un fichier

```python
with open("texte.txt", "r") as fichier:
    contenu = fichier.read()

print(contenu)
```

---

## Lire ligne par ligne

```python
with open("texte.txt", "r") as fichier:
    for ligne in fichier:
        print(ligne)
```

---

# 19. Écriture de fichiers

```python
with open("resultat.txt", "w") as fichier:
    fichier.write("Bonjour")
```

---

# 20. Importer des modules

Python possède de nombreux modules intégrés.

## Module math

```python
import math

print(math.sqrt(25))
```

Résultat :

```text
5.0
```

---

## Nombre aléatoire

```python
import random

print(random.randint(1, 10))
```

---

# 21. Les compréhensions de liste

Méthode classique :

```python
carres = []

for i in range(5):
    carres.append(i * i)

print(carres)
```

Version compacte :

```python
carres = [i * i for i in range(5)]

print(carres)
```

Résultat :

```text
[0, 1, 4, 9, 16]
```

---

# 22. Formatage de chaînes (f-string)

Méthode recommandée.

```python
nom = "Alice"
age = 25

print(f"{nom} a {age} ans")
```

Résultat :

```text
Alice a 25 ans
```

---

# 23. Projet pratique : calculatrice simple

```python
a = float(input("Premier nombre : "))
b = float(input("Deuxième nombre : "))

print("1 : Addition")
print("2 : Soustraction")
print("3 : Multiplication")
print("4 : Division")

choix = input("Choix : ")

if choix == "1":
    print(a + b)

elif choix == "2":
    print(a - b)

elif choix == "3":
    print(a * b)

elif choix == "4":
    if b != 0:
        print(a / b)
    else:
        print("Division impossible")

else:
    print("Choix invalide")
```

---

# 24. Projet pratique : compteur de mots

```python
texte = input("Entrez une phrase : ")

mots = texte.split()

print("Nombre de mots :", len(mots))
```

---

# 25. Projet pratique : deviner un nombre

```python
import random

secret = random.randint(1, 100)

while True:
    proposition = int(input("Votre nombre : "))

    if proposition < secret:
        print("Trop petit")

    elif proposition > secret:
        print("Trop grand")

    else:
        print("Bravo")
        break
```

---

# Résumé

À ce stade, vous savez :

- Afficher du texte
- Utiliser des variables
- Manipuler les types de base
- Faire des calculs
- Utiliser des conditions
- Utiliser des boucles
- Créer des fonctions
- Manipuler des listes
- Utiliser des dictionnaires
- Lire et écrire des fichiers
- Gérer des erreurs
- Importer des modules

Ces notions représentent environ 80 à 90 % de ce qui est utilisé dans les scripts Python classiques. Une fois ces bases maîtrisées, vous pourrez aborder :
- Les modules avancés
- Les expressions régulières
- Le traitement de données
- Les API
- Les bases de données SQL
- Le développement web
- La programmation orientée objet (POO)
