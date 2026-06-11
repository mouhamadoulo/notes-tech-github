# Tutoriel complet : Créer des scripts en Python

## Introduction

Python est un langage de programmation simple à apprendre, puissant et utilisé dans de nombreux domaines :

- Automatisation de tâches
- Analyse de données
- Intelligence artificielle
- Développement web
- Administration système
- Cybersécurité
- Jeux vidéo
- Scripts utilitaires

Un **script Python** est simplement un fichier texte contenant des instructions Python exécutées de haut en bas.

---

# 1. Installation de Python

Téléchargez Python depuis :

- https://www.python.org/downloads/

Vérifiez l'installation :

```bash
python --version
```

ou

```bash
python3 --version
```

---

# 2. Premier script Python

Créez un fichier :

```text
bonjour.py
```

Code :

```python
print("Bonjour le monde !")
```

Exécution :

```bash
python bonjour.py
```

Résultat :

```text
Bonjour le monde !
```

---

# 3. Variables

Les variables permettent de stocker des données.

```python
nom = "Pierre"
age = 28
taille = 1.80
est_majeur = True
```

Affichage :

```python
print(nom)
print(age)
```

---

# Types de données principaux

| Type | Exemple |
|--------|---------|
| str | "Bonjour" |
| int | 42 |
| float | 3.14 |
| bool | True |
| list | [1,2,3] |
| dict | {"nom":"Pierre"} |

---

# 4. Les entrées utilisateur

```python
nom = input("Quel est votre nom ? ")

print("Bonjour", nom)
```

Exemple :

```text
Quel est votre nom ? Pierre
Bonjour Pierre
```

---

# 5. Conversion des types

```python
age = int(input("Votre âge : "))

print(age + 1)
```

Conversions courantes :

```python
int()
float()
str()
bool()
```

---

# 6. Les opérateurs

## Mathématiques

```python
a = 10
b = 3

print(a + b)
print(a - b)
print(a * b)
print(a / b)
print(a // b)
print(a % b)
print(a ** b)
```

---

## Comparaisons

```python
print(10 > 5)
print(10 < 5)
print(10 == 10)
print(10 != 5)
```

---

## Logiques

```python
and
or
not
```

Exemple :

```python
age = 20

print(age >= 18 and age < 65)
```

---

# 7. Conditions

## if

```python
age = 18

if age >= 18:
    print("Majeur")
```

---

## if / else

```python
age = 15

if age >= 18:
    print("Majeur")
else:
    print("Mineur")
```

---

## if / elif / else

```python
note = 14

if note >= 16:
    print("Très bien")
elif note >= 10:
    print("Admis")
else:
    print("Échec")
```

---

# 8. Boucles

## Boucle while

```python
compteur = 1

while compteur <= 5:
    print(compteur)
    compteur += 1
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

## Parcourir une liste

```python
fruits = ["pomme", "banane", "orange"]

for fruit in fruits:
    print(fruit)
```

---

# 9. Les listes

Création :

```python
fruits = ["pomme", "banane", "orange"]
```

Accès :

```python
print(fruits[0])
```

Ajout :

```python
fruits.append("kiwi")
```

Suppression :

```python
fruits.remove("banane")
```

Longueur :

```python
print(len(fruits))
```

---

# 10. Les dictionnaires

Equivalent des objets JSON.

```python
personne = {
    "nom": "Pierre",
    "age": 28,
    "ville": "Paris"
}
```

Accès :

```python
print(personne["nom"])
```

Ajout :

```python
personne["email"] = "test@test.com"
```

Parcours :

```python
for cle, valeur in personne.items():
    print(cle, valeur)
```

---

# 11. Les fonctions

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

## Paramètres

```python
def bonjour(nom):
    print("Bonjour", nom)

bonjour("Pierre")
```

---

## Retour de valeur

```python
def addition(a, b):
    return a + b

resultat = addition(5, 3)

print(resultat)
```

---

# 12. Gestion des erreurs

Sans gestion :

```python
nombre = int(input())
```

Si l'utilisateur écrit du texte :

```text
ValueError
```

---

Avec try/except :

```python
try:
    nombre = int(input("Nombre : "))
except ValueError:
    print("Veuillez saisir un nombre valide")
```

---

# 13. Les fichiers

## Lire un fichier

```python
with open("texte.txt", "r", encoding="utf-8") as fichier:
    contenu = fichier.read()

print(contenu)
```

---

## Écrire un fichier

```python
with open("texte.txt", "w", encoding="utf-8") as fichier:
    fichier.write("Bonjour")
```

---

## Ajouter du contenu

```python
with open("texte.txt", "a", encoding="utf-8") as fichier:
    fichier.write("\nNouvelle ligne")
```

---

# 14. Les modules

Importer un module :

```python
import math

print(math.sqrt(25))
```

Résultat :

```text
5.0
```

---

## Import spécifique

```python
from math import sqrt

print(sqrt(25))
```

---

# 15. Les packages externes

Installation :

```bash
pip install requests
```

Utilisation :

```python
import requests

response = requests.get("https://api.github.com")

print(response.status_code)
```

---

# 16. Les classes (Programmation Orientée Objet)

## Création

```python
class Voiture:

    def __init__(self, marque):
        self.marque = marque

    def afficher(self):
        print(self.marque)
```

---

## Utilisation

```python
v = Voiture("Peugeot")

v.afficher()
```

---

# Cas d'usage courants

---

# Cas 1 : Calculatrice

```python
a = float(input("Nombre 1 : "))
b = float(input("Nombre 2 : "))

operation = input("Opération (+ - * /) : ")

if operation == "+":
    print(a + b)

elif operation == "-":
    print(a - b)

elif operation == "*":
    print(a * b)

elif operation == "/":
    print(a / b)

else:
    print("Opération inconnue")
```

---

# Cas 2 : Générateur de mots de passe

```python
import random
import string

taille = 16

caracteres = (
    string.ascii_letters +
    string.digits +
    string.punctuation
)

mot_de_passe = "".join(
    random.choice(caracteres)
    for _ in range(taille)
)

print(mot_de_passe)
```

---

# Cas 3 : Renommage automatique de fichiers

```python
import os

dossier = "documents"

for i, fichier in enumerate(os.listdir(dossier)):
    ancien = os.path.join(dossier, fichier)
    nouveau = os.path.join(dossier, f"fichier_{i}.txt")

    os.rename(ancien, nouveau)
```

---

# Cas 4 : Lecture CSV

```python
import csv

with open("data.csv", newline="", encoding="utf-8") as f:
    lecteur = csv.reader(f)

    for ligne in lecteur:
        print(ligne)
```

---

# Cas 5 : Sauvegarde JSON

```python
import json

data = {
    "nom": "Pierre",
    "age": 28
}

with open("data.json", "w") as f:
    json.dump(data, f, indent=4)
```

---

# Cas 6 : Requête API

```python
import requests

url = "https://api.github.com/users/octocat"

response = requests.get(url)

data = response.json()

print(data["login"])
```

---

# Cas 7 : Surveillance d'un dossier

```python
import os
import time

dossier = "documents"

while True:

    fichiers = os.listdir(dossier)

    print(f"Nombre de fichiers : {len(fichiers)}")

    time.sleep(5)
```

---

# Cas 8 : Recherche dans un fichier

```python
mot = input("Mot recherché : ")

with open("texte.txt", "r", encoding="utf-8") as f:

    for numero, ligne in enumerate(f, start=1):

        if mot.lower() in ligne.lower():
            print(
                f"Ligne {numero}: {ligne.strip()}"
            )
```

---

# Structure recommandée d'un script professionnel

```python
import os


def charger_donnees():
    pass


def traiter_donnees():
    pass


def sauvegarder():
    pass


def main():

    charger_donnees()

    traiter_donnees()

    sauvegarder()


if __name__ == "__main__":
    main()
```

---

# Bonnes pratiques

## Utiliser des noms explicites

Mauvais :

```python
x = 10
```

Bon :

```python
nombre_utilisateurs = 10
```

---

## Ajouter des commentaires utiles

```python
# Calcul de la TVA
prix_ttc = prix_ht * 1.20
```

---

## Éviter la duplication

Mauvais :

```python
print("Bonjour Pierre")
print("Bonjour Marie")
print("Bonjour Jean")
```

Bon :

```python
def bonjour(nom):
    print("Bonjour", nom)
```

---

## Utiliser des fonctions

```python
def calcul_tva(prix):
    return prix * 1.20
```

---

# Bibliothèques Python incontournables

| Bibliothèque | Utilité |
|-------------|----------|
| requests | API HTTP |
| pandas | Analyse de données |
| numpy | Calcul scientifique |
| matplotlib | Graphiques |
| sqlalchemy | Base de données |
| flask | Web |
| fastapi | API REST |
| beautifulsoup4 | Scraping |
| openpyxl | Excel |
| pillow | Images |

Installation :

```bash
pip install nom_bibliotheque
```

---

# Mini projet complet : Gestionnaire de tâches

```python
taches = []

while True:

    print("\n1. Ajouter")
    print("2. Voir")
    print("3. Quitter")

    choix = input("Choix : ")

    if choix == "1":

        tache = input("Nouvelle tâche : ")

        taches.append(tache)

    elif choix == "2":

        for i, tache in enumerate(taches, start=1):
            print(i, "-", tache)

    elif choix == "3":
        break

    else:
        print("Choix invalide")
```

---

# Conclusion

Pour devenir efficace en Python, maîtrisez dans cet ordre :

1. Variables
2. Conditions
3. Boucles
4. Fonctions
5. Listes et dictionnaires
6. Gestion des fichiers
7. Modules
8. Gestion des erreurs
9. Programmation orientée objet
10. Bibliothèques externes

Une fois ces bases acquises, vous pourrez développer :
- des scripts d'automatisation ;
- des outils système ;
- des API web ;
- des applications de bureau ;
- des bots ;
- des programmes d'analyse de données ;
- des projets d'intelligence artificielle.

La meilleure méthode d'apprentissage reste la pratique : écrire des scripts réels, les améliorer, puis les transformer progressivement en projets complets.
