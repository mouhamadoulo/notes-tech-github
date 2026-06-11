# Tutoriel complet : Comprendre la Programmation Orientée Objet (POO) en Python à partir de zéro

> Objectif : apprendre la POO en Python en construisant progressivement un mini projet de gestion de bibliothèque.

---

# 1. Pourquoi la POO existe ?

Avant la POO, on écrit souvent des programmes avec :

- des variables
- des fonctions
- des structures de données (listes, dictionnaires)

Exemple simple :

```python
livre1 = {
    "titre": "Python Débutant",
    "auteur": "Jean Martin",
    "disponible": True
}

livre2 = {
    "titre": "Python Avancé",
    "auteur": "Marie Dupont",
    "disponible": False
}
```

Pour afficher un livre :

```python
def afficher_livre(livre):
    print(f"Titre : {livre['titre']}")
    print(f"Auteur : {livre['auteur']}")
    print(f"Disponible : {livre['disponible']}")
```

Utilisation :

```python
afficher_livre(livre1)
```

Résultat :

```text
Titre : Python Débutant
Auteur : Jean Martin
Disponible : True
```

---

# Problème

Si notre bibliothèque contient :

- 10 livres
- 100 livres
- 10 000 livres

nous allons manipuler énormément de dictionnaires.

Le code devient :

- difficile à maintenir
- difficile à relire
- source d'erreurs

La POO a été créée pour regrouper :

- les données
- les actions

dans un même objet.

---

# 2. Première classe

En POO, un livre devient un objet.

Créons notre première classe.

```python
class Livre:
    pass
```

Explication :

```python
class Livre:
```

définit un modèle de fabrication.

On peut voir la classe comme un plan de construction.

---

# 3. Créer un objet

```python
class Livre:
    pass

livre1 = Livre()
```

Ici :

```python
Livre()
```

fabrique un objet.

L'objet est stocké dans :

```python
livre1
```

On appelle cela une **instance**.

---

# 4. Ajouter des attributs

Un livre possède :

- un titre
- un auteur
- une disponibilité

Créons-les.

```python
class Livre:

    def __init__(self, titre, auteur):
        self.titre = titre
        self.auteur = auteur
        self.disponible = True
```

Création :

```python
livre1 = Livre("Python Débutant", "Jean Martin")
```

---

# 5. Comprendre `__init__`

Cette méthode est exécutée automatiquement lors de la création.

```python
livre1 = Livre("Python Débutant", "Jean Martin")
```

Python exécute :

```python
__init__(
    self,
    "Python Débutant",
    "Jean Martin"
)
```

---

# 6. Comprendre `self`

C'est probablement la notion la plus importante.

Exemple :

```python
livre1 = Livre("Python Débutant", "Jean Martin")
livre2 = Livre("IA Moderne", "Alice Martin")
```

Python crée :

```python
livre1
```

et

```python
livre2
```

`self` représente l'objet courant.

Pour `livre1` :

```python
self.titre
```

désigne :

```python
livre1.titre
```

Pour `livre2` :

```python
self.titre
```

désigne :

```python
livre2.titre
```

---

# 7. Afficher les attributs

```python
print(livre1.titre)
print(livre1.auteur)
```

Résultat :

```text
Python Débutant
Jean Martin
```

---

# 8. Ajouter des méthodes

Une méthode est une fonction appartenant à une classe.

```python
class Livre:

    def __init__(self, titre, auteur):
        self.titre = titre
        self.auteur = auteur
        self.disponible = True

    def afficher(self):
        print(f"Titre : {self.titre}")
        print(f"Auteur : {self.auteur}")
```

Utilisation :

```python
livre1.afficher()
```

Résultat :

```text
Titre : Python Débutant
Auteur : Jean Martin
```

---

# 9. Emprunter un livre

Ajoutons une action.

```python
class Livre:

    def __init__(self, titre, auteur):
        self.titre = titre
        self.auteur = auteur
        self.disponible = True

    def emprunter(self):

        if self.disponible:
            self.disponible = False
            print("Livre emprunté")
        else:
            print("Livre déjà emprunté")
```

Utilisation :

```python
livre1.emprunter()
```

---

# 10. Retourner un livre

```python
def retourner(self):

    if not self.disponible:
        self.disponible = True
        print("Livre retourné")
    else:
        print("Déjà disponible")
```

Classe complète :

```python
class Livre:

    def __init__(self, titre, auteur):
        self.titre = titre
        self.auteur = auteur
        self.disponible = True

    def emprunter(self):

        if self.disponible:
            self.disponible = False
            print("Livre emprunté")

    def retourner(self):

        if not self.disponible:
            self.disponible = True
            print("Livre retourné")
```

---

# 11. Créer plusieurs objets

```python
livre1 = Livre("Python", "Jean")
livre2 = Livre("Java", "Marie")
livre3 = Livre("C++", "Alice")
```

Chaque objet possède ses propres données.

```python
print(livre1.titre)
print(livre2.titre)
print(livre3.titre)
```

---

# 12. Créer une classe Bibliothèque

Nous allons maintenant gérer plusieurs livres.

```python
class Bibliotheque:

    def __init__(self):
        self.livres = []
```

La bibliothèque contient :

```python
self.livres
```

qui est une liste.

---

# 13. Ajouter un livre

```python
class Bibliotheque:

    def __init__(self):
        self.livres = []

    def ajouter_livre(self, livre):
        self.livres.append(livre)
```

Utilisation :

```python
biblio = Bibliotheque()

biblio.ajouter_livre(livre1)
biblio.ajouter_livre(livre2)
```

---

# 14. Afficher tous les livres

```python
def afficher_livres(self):

    for livre in self.livres:
        livre.afficher()
```

Classe :

```python
class Bibliotheque:

    def __init__(self):
        self.livres = []

    def ajouter_livre(self, livre):
        self.livres.append(livre)

    def afficher_livres(self):

        for livre in self.livres:
            livre.afficher()
```

---

# 15. Encapsulation

Principe :

> Les données internes ne devraient pas être modifiées n'importe comment.

Mauvaise pratique :

```python
livre1.disponible = False
```

Meilleure pratique :

```python
livre1.emprunter()
```

On contrôle ainsi les modifications.

---

# 16. Attributs privés

Python utilise une convention :

```python
class Livre:

    def __init__(self, titre):
        self.__titre = titre
```

Le double underscore :

```python
__titre
```

signale :

> attribut interne à ne pas manipuler directement.

---

# 17. Getters

Lire un attribut privé.

```python
class Livre:

    def __init__(self, titre):
        self.__titre = titre

    def get_titre(self):
        return self.__titre
```

Utilisation :

```python
print(livre.get_titre())
```

---

# 18. Setters

Modifier un attribut.

```python
class Livre:

    def __init__(self, titre):
        self.__titre = titre

    def set_titre(self, titre):
        self.__titre = titre
```

---

# 19. Properties

Méthode moderne.

```python
class Livre:

    def __init__(self, titre):
        self.__titre = titre

    @property
    def titre(self):
        return self.__titre
```

Utilisation :

```python
print(livre.titre)
```

sans appeler de fonction.

---

# 20. Héritage

Supposons plusieurs types de livres.

Classe de base :

```python
class Livre:

    def __init__(self, titre):
        self.titre = titre
```

---

# 21. Hériter d'une classe

```python
class Roman(Livre):
    pass
```

Création :

```python
roman = Roman("Les Misérables")
```

Roman possède déjà :

```python
titre
```

grâce à l'héritage.

---

# 22. Ajouter des attributs spécifiques

```python
class Roman(Livre):

    def __init__(self, titre, genre):

        super().__init__(titre)

        self.genre = genre
```

Explication :

```python
super()
```

appelle le constructeur parent.

---

# 23. Exemple complet

```python
class Livre:

    def __init__(self, titre):
        self.titre = titre


class Roman(Livre):

    def __init__(self, titre, genre):

        super().__init__(titre)

        self.genre = genre
```

Utilisation :

```python
roman = Roman(
    "Le Comte de Monte-Cristo",
    "Aventure"
)
```

---

# 24. Polymorphisme

Même méthode, comportements différents.

Classe parent :

```python
class Livre:

    def description(self):
        print("Livre générique")
```

Classe enfant :

```python
class Roman(Livre):

    def description(self):
        print("Roman")
```

Autre enfant :

```python
class Manga(Livre):

    def description(self):
        print("Manga")
```

---

# 25. Utilisation du polymorphisme

```python
livres = [
    Roman(),
    Manga()
]

for livre in livres:
    livre.description()
```

Résultat :

```text
Roman
Manga
```

---

# 26. Composition

Une classe contient d'autres objets.

Exemple :

```python
class Bibliotheque:

    def __init__(self):
        self.livres = []
```

Une bibliothèque est composée de livres.

---

# 27. Projet final complet

```python
class Livre:

    def __init__(self, titre, auteur):

        self.titre = titre
        self.auteur = auteur
        self.disponible = True

    def afficher(self):

        print(f"Titre : {self.titre}")
        print(f"Auteur : {self.auteur}")
        print(f"Disponible : {self.disponible}")

    def emprunter(self):

        if self.disponible:
            self.disponible = False

    def retourner(self):

        if not self.disponible:
            self.disponible = True


class Roman(Livre):

    def __init__(self, titre, auteur, genre):

        super().__init__(titre, auteur)

        self.genre = genre

    def afficher(self):

        super().afficher()
        print(f"Genre : {self.genre}")


class Bibliotheque:

    def __init__(self):
        self.livres = []

    def ajouter_livre(self, livre):
        self.livres.append(livre)

    def afficher_livres(self):

        for livre in self.livres:
            livre.afficher()
            print("-" * 30)


biblio = Bibliotheque()

livre1 = Roman(
    "Le Comte de Monte-Cristo",
    "Alexandre Dumas",
    "Aventure"
)

livre2 = Roman(
    "Les Misérables",
    "Victor Hugo",
    "Historique"
)

biblio.ajouter_livre(livre1)
biblio.ajouter_livre(livre2)

biblio.afficher_livres()
```

---

# Résumé des concepts POO

| Concept | Rôle |
|----------|----------|
| Classe | Plan de fabrication |
| Objet | Instance d'une classe |
| Attribut | Donnée d'un objet |
| Méthode | Fonction d'un objet |
| self | Référence à l'objet courant |
| __init__ | Constructeur |
| Encapsulation | Protection des données |
| Héritage | Réutilisation de code |
| Polymorphisme | Même interface, comportements différents |
| Composition | Objets contenus dans d'autres objets |

---

# Ce qu'il faut absolument retenir

La POO consiste à répondre à deux questions :

### 1. Quelles sont les données ?

Exemple :

```python
titre
auteur
disponible
```

### 2. Quelles actions peut-on effectuer ?

Exemple :

```python
emprunter()
retourner()
afficher()
```

On regroupe alors données et comportements dans une même classe :

```python
class Livre:
    ...
```

C'est le cœur de la programmation orientée objet.
