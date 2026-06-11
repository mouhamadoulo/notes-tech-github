# Tutoriel complet : Créer une API REST en Python avec FastAPI

## Introduction

Dans ce tutoriel, nous allons apprendre à créer une API REST moderne avec FastAPI en construisant un projet complet de gestion de tâches (Todo List).

À la fin du tutoriel, vous saurez :

- Installer FastAPI
- Créer des routes API
- Utiliser les méthodes HTTP (GET, POST, PUT, DELETE)
- Valider les données avec Pydantic
- Utiliser les paramètres d'URL
- Organiser un projet proprement
- Gérer les erreurs
- Tester l'API
- Comprendre les bonnes pratiques

---

# Pourquoi FastAPI ?

FastAPI est un framework Python moderne pour créer des API.

Ses avantages :

- Très rapide
- Documentation automatique
- Validation automatique des données
- Syntaxe moderne basée sur les annotations de type Python
- Facile à apprendre

---

# Notre projet fil rouge

Nous allons créer une API de gestion de tâches.

Chaque tâche contiendra :

```json
{
    "id": 1,
    "title": "Apprendre FastAPI",
    "description": "Faire le tutoriel complet",
    "completed": false
}
```

Notre API permettra de :

| Action | Méthode |
|----------|----------|
| Lister les tâches | GET |
| Obtenir une tâche | GET |
| Créer une tâche | POST |
| Modifier une tâche | PUT |
| Supprimer une tâche | DELETE |

---

# Installation

## Créer un environnement virtuel

Linux / Mac :

```bash
python -m venv venv
source venv/bin/activate
```

Windows :

```powershell
python -m venv venv
venv\Scripts\activate
```

---

## Installer FastAPI

```bash
pip install fastapi uvicorn
```

Vérification :

```bash
pip list
```

Vous devriez voir :

```text
fastapi
uvicorn
```

---

# Premier programme FastAPI

Créer un fichier :

```text
main.py
```

Code :

```python
from fastapi import FastAPI

app = FastAPI()


@app.get("/")
def root():
    return {"message": "Bienvenue sur mon API"}
```

---

## Explications

### Création de l'application

```python
app = FastAPI()
```

Instancie l'application FastAPI.

---

### Création d'une route

```python
@app.get("/")
```

Cette ligne indique :

- méthode HTTP : GET
- URL : /

---

### Fonction exécutée

```python
def root():
```

Cette fonction sera appelée lorsqu'un utilisateur visite :

```text
GET /
```

---

### Réponse

```python
return {"message": "Bienvenue sur mon API"}
```

FastAPI convertit automatiquement le dictionnaire Python en JSON.

Réponse :

```json
{
  "message": "Bienvenue sur mon API"
}
```

---

# Lancer le serveur

```bash
uvicorn main:app --reload
```

Décomposition :

```text
main     -> fichier main.py
app      -> variable app = FastAPI()
--reload -> recharge automatiquement
```

---

# Documentation automatique

FastAPI génère automatiquement une documentation.

Swagger :

```text
http://localhost:8000/docs
```

ReDoc :

```text
http://localhost:8000/redoc
```

C'est l'un des plus gros avantages de FastAPI.

---

# Création de notre API Todo

Nous allons maintenant créer notre vraie API.

---

# Stockage temporaire

Pour simplifier, nous stockons les données en mémoire.

```python
tasks = []
```

Attention :

Lorsque le serveur redémarre, les données sont perdues.

Plus tard on utilisera une base de données.

---

# Créer un modèle de données

FastAPI utilise Pydantic.

```python
from pydantic import BaseModel
```

---

## Définition d'une tâche

```python
class Task(BaseModel):
    title: str
    description: str
    completed: bool = False
```

---

## Explication

### title

```python
title: str
```

Champ obligatoire de type chaîne.

---

### description

```python
description: str
```

Champ obligatoire.

---

### completed

```python
completed: bool = False
```

Champ optionnel avec valeur par défaut.

---

# Route POST : créer une tâche

```python
@app.post("/tasks")
def create_task(task: Task):
    return task
```

---

## Tester

Requête :

```json
{
    "title": "Apprendre FastAPI",
    "description": "Faire le tutoriel"
}
```

Réponse :

```json
{
    "title": "Apprendre FastAPI",
    "description": "Faire le tutoriel",
    "completed": false
}
```

---

## Validation automatique

Si l'utilisateur envoie :

```json
{
    "title": 123
}
```

FastAPI retourne automatiquement une erreur.

Aucun code supplémentaire nécessaire.

---

# Ajouter un identifiant

Nous devons générer un ID.

```python
tasks = []
current_id = 1
```

---

## Nouvelle route POST

```python
@app.post("/tasks")
def create_task(task: Task):

    global current_id

    new_task = {
        "id": current_id,
        **task.model_dump()
    }

    tasks.append(new_task)

    current_id += 1

    return new_task
```

---

## Explications

### model_dump()

```python
task.model_dump()
```

Transforme un objet Pydantic en dictionnaire.

Résultat :

```python
{
    "title": "...",
    "description": "...",
    "completed": False
}
```

---

### Fusion de dictionnaires

```python
{
    "id": current_id,
    **task.model_dump()
}
```

Produit :

```python
{
    "id": 1,
    "title": "...",
    "description": "...",
    "completed": False
}
```

---

# Route GET : lister toutes les tâches

```python
@app.get("/tasks")
def get_tasks():
    return tasks
```

---

## Réponse

```json
[
  {
    "id": 1,
    "title": "Apprendre FastAPI",
    "description": "Faire le tutoriel",
    "completed": false
  }
]
```

---

# Paramètres d'URL

Exemple :

```text
/tasks/1
/tasks/2
/tasks/3
```

---

# Obtenir une tâche

```python
@app.get("/tasks/{task_id}")
def get_task(task_id: int):

    for task in tasks:
        if task["id"] == task_id:
            return task

    return {"error": "Tâche introuvable"}
```

---

## Explications

Le paramètre :

```python
task_id: int
```

est automatiquement extrait de l'URL.

Exemple :

```text
GET /tasks/5
```

donne :

```python
task_id = 5
```

---

# Gestion correcte des erreurs

Utilisons les exceptions HTTP.

```python
from fastapi import HTTPException
```

---

Version améliorée :

```python
@app.get("/tasks/{task_id}")
def get_task(task_id: int):

    for task in tasks:
        if task["id"] == task_id:
            return task

    raise HTTPException(
        status_code=404,
        detail="Tâche introuvable"
    )
```

---

Réponse :

```json
{
  "detail": "Tâche introuvable"
}
```

---

# Mise à jour d'une tâche

```python
@app.put("/tasks/{task_id}")
def update_task(task_id: int, updated_task: Task):

    for task in tasks:

        if task["id"] == task_id:

            task["title"] = updated_task.title
            task["description"] = updated_task.description
            task["completed"] = updated_task.completed

            return task

    raise HTTPException(
        status_code=404,
        detail="Tâche introuvable"
    )
```

---

# Suppression d'une tâche

```python
@app.delete("/tasks/{task_id}")
def delete_task(task_id: int):

    for index, task in enumerate(tasks):

        if task["id"] == task_id:

            tasks.pop(index)

            return {
                "message": "Tâche supprimée"
            }

    raise HTTPException(
        status_code=404,
        detail="Tâche introuvable"
    )
```

---

# Code complet de l'API

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

tasks = []
current_id = 1


class Task(BaseModel):
    title: str
    description: str
    completed: bool = False


@app.get("/")
def root():
    return {
        "message": "API Todo FastAPI"
    }


@app.get("/tasks")
def get_tasks():
    return tasks


@app.get("/tasks/{task_id}")
def get_task(task_id: int):

    for task in tasks:
        if task["id"] == task_id:
            return task

    raise HTTPException(
        status_code=404,
        detail="Tâche introuvable"
    )


@app.post("/tasks")
def create_task(task: Task):

    global current_id

    new_task = {
        "id": current_id,
        **task.model_dump()
    }

    tasks.append(new_task)

    current_id += 1

    return new_task


@app.put("/tasks/{task_id}")
def update_task(task_id: int, updated_task: Task):

    for task in tasks:

        if task["id"] == task_id:

            task["title"] = updated_task.title
            task["description"] = updated_task.description
            task["completed"] = updated_task.completed

            return task

    raise HTTPException(
        status_code=404,
        detail="Tâche introuvable"
    )


@app.delete("/tasks/{task_id}")
def delete_task(task_id: int):

    for index, task in enumerate(tasks):

        if task["id"] == task_id:

            tasks.pop(index)

            return {
                "message": "Tâche supprimée"
            }

    raise HTTPException(
        status_code=404,
        detail="Tâche introuvable"
    )
```

---

# Organisation professionnelle du projet

Quand le projet grandit :

```text
project/
│
├── app/
│   ├── main.py
│   ├── models/
│   │   └── task.py
│   │
│   ├── routers/
│   │   └── tasks.py
│   │
│   ├── services/
│   │   └── task_service.py
│   │
│   └── database/
│       └── db.py
│
├── tests/
│
├── requirements.txt
│
└── README.md
```

---

# Ajouter une base de données plus tard

Les solutions les plus courantes :

- SQLite (développement)
- PostgreSQL (production)
- MySQL

Avec :

```bash
pip install sqlalchemy
```

ou

```bash
pip install sqlmodel
```

---

# Bonnes pratiques FastAPI

## Utiliser les annotations de type

Toujours :

```python
def get_task(task_id: int):
```

Jamais :

```python
def get_task(task_id):
```

---

## Valider les entrées

Toujours utiliser Pydantic.

```python
class User(BaseModel):
    email: str
```

---

## Gérer les erreurs

Toujours :

```python
raise HTTPException(...)
```

---

## Séparer les responsabilités

- Routes
- Services
- Base de données
- Modèles

---

# Résumé

Nous avons appris :

- Installation de FastAPI
- Création d'une application
- Routes GET
- Routes POST
- Routes PUT
- Routes DELETE
- Paramètres d'URL
- Validation avec Pydantic
- Gestion des erreurs
- Documentation automatique
- Organisation d'un projet professionnel

FastAPI est aujourd'hui l'un des frameworks les plus utilisés pour construire des API Python modernes grâce à sa simplicité, ses performances et son excellente intégration avec l'écosystème Python.
