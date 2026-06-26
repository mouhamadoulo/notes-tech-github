Pour ce type de présentation, je privilégierais un style **visuel et dynamique**, avec peu de texte et beaucoup de schémas. L'objectif est que ton auditoire retienne l'évolution des pratiques plutôt que des listes de fonctionnalités. Voici des idées de visuels et d'animations pour chaque partie.

---

# 1. Introduction – La transformation du développement

## Animation : une frise chronologique

Construis une timeline qui se révèle progressivement.

```
2019          2022           2024             2025
│--------------│---------------│----------------│
Dév classique   Lucia         GitHub Copilot   Développement agentique
```

Animation :

* apparition du premier jalon
* puis du deuxième
* puis du troisième
* puis du dernier

Le public comprend immédiatement qu'il s'agit d'une évolution.

---

# 2. Le développement sans IA

Créer un schéma simple.

```
Besoin métier

      ↓

Développeur

      ↓

Recherche StackOverflow
Documentation
Google

      ↓

Code

      ↓

Tests

      ↓

Correction
```

Animation :

Faire apparaître chaque étape une par une.

Tu peux faire apparaître un petit chronomètre à côté.

⏳ Temps important

---

# 3. Avec Lucia

Créer un schéma centré.

```
                 Lucia

         ┌──────────────┐
         │              │
Explication du code
Documentation
Recherche
Tests
Résumé
```

Autour, représenter plusieurs développeurs.

```
           Dev

Dev ---- Lucia ---- Dev

           Dev
```

Animation :

Chaque bulle apparaît quand tu expliques un cas d'usage.

---

# 4. Avant / Après GitHub Copilot

Une diapo coupée en deux.

Avant

```
Developer

↓

Tape tout le code

↓

Compile

↓

Corrige

↓

Continue
```

Après

```
Developer

↓

Copilot propose

↓

TAB

↓

Le code est écrit

↓

Le développeur vérifie
```

Animation :

Une barre verticale glisse et révèle la partie "Après".

---

# 5. Démonstration de complétion

Afficher une capture IDE.

Au lieu de montrer tout le code, utiliser des encadrés.

Par exemple

```
public UserDto create(...)
```

Puis animation :

Le reste apparaît comme si Copilot écrivait.

```
public UserDto create(...)
{
   ...
}
```

Le public comprend immédiatement le gain.

---

# 6. Ce que Copilot faisait

Créer une roue.

Au centre

```
GitHub Copilot
```

Autour

```
Tests

Documentation

SQL

Angular

Spring

Regex

Refactoring

Commentaires
```

Chaque élément apparaît au clic.

---

# 7. Le développement agentique

C'est LA diapo à travailler.

Faire un schéma comparatif.

## Copilot

```
Développeur

↓

Prompt

↓

Copilot

↓

Réponse
```

Puis faire disparaître.

---

Faire apparaître :

```
Développeur

↓

Objectif

↓

Agent IA

↓

Analyse

↓

Planifie

↓

Modifie 8 fichiers

↓

Lance Maven

↓

Corrige

↓

Propose une PR
```

Animation :

Chaque étape apparaît automatiquement.

Le public voit immédiatement la différence.

---

# 8. Architecture d'un agent

Très visuel.

```
             Objectif

                 │

                 ▼

          Agent IA

     ┌──────┼─────────┐

Analyse   Recherche   Planification

     └──────┼─────────┘

            ▼

 Modifie plusieurs fichiers

            ▼

 Lance les tests

            ▼

 Corrige

            ▼

 Développeur valide
```

---

# 9. Exemple concret

Très parlant.

Objectif

> Ajouter la gestion des clients.

Animation.

Petit à petit apparaissent :

✅ Entity

↓

✅ Repository

↓

✅ Service

↓

✅ Controller

↓

✅ Tests

↓

✅ Angular

↓

✅ Routes

↓

✅ Documentation

À la fin

```
✔ 8 fichiers modifiés
```

C'est impressionnant sans être exagéré.

---

# 10. Productivité

Utiliser des icônes.

Avant

```
🔍 Recherche

⌨️ Codage

🧪 Tests

📚 Documentation
```

Après

```
🤖 Recherche assistée

🤖 Génération

🤖 Proposition de tests

🤖 Documentation automatique
```

Animation :

Les icônes se remplacent progressivement.

---

# 11. Les limites

Faire un triangle.

```
            IA

      /               \

Rapidité         Créativité

        \

     Fiabilité
```

Puis faire apparaître un panneau

```
⚠ Toujours vérifier
```

---

# 12. Le nouveau rôle du développeur

Créer une évolution.

```
Avant

Développeur

↓

Écrit du code
```

Animation.

Puis

```
Aujourd'hui

Développeur

↓

Architecte

↓

Prompt Engineer

↓

Relecteur

↓

Expert métier

↓

Décide
```

Tu peux utiliser une animation de transformation.

---

# 13. Conclusion

Faire une montée en puissance.

```
Développeur

↓

Développeur + IA

↓

Développeur + Copilot

↓

Développeur + Agent

↓

Équipe augmentée
```

La dernière étape peut être mise en évidence avec une couleur différente.

---

# Quelques animations efficaces dans PowerPoint

Évite les animations trop spectaculaires. Privilégie celles qui accompagnent ton discours :

* **Apparition/Fondu** : idéal pour révéler progressivement des idées.
* **Morphose (Morph)** : parfait pour transformer une diapositive "Avant" en "Après" ou faire évoluer un schéma sans rupture visuelle.
* **Balayage (Wipe)** : utile pour montrer un flux ou une chronologie.
* **Zoom discret** : pour attirer l'attention sur une partie d'un schéma ou une capture d'écran.
* **Chemins de déplacement** : efficaces pour illustrer un flux d'information (par exemple, entre le développeur, l'agent IA et le dépôt Git).

### Une idée forte pour marquer les esprits

Si tu veux que le public retienne une seule image, construis toute la présentation autour d'un schéma qui évolue au fil des diapositives :

```
2019
👨‍💻 Développeur
      │
      ▼
     Code

2022
👨‍💻 ⇄ 🤖 Lucia
      │
      ▼
     Code

2024
👨‍💻 ⇄ 🤖 Copilot
      │
      ▼
     Code

2025
👨‍💻
  │
  ▼
🤖 Agent IA
  │
  ├── Analyse
  ├── Développe
  ├── Teste
  ├── Corrige
  └── Propose une PR
```

Grâce à une animation **Morph**, ce même schéma peut se transformer d'une diapositive à l'autre. Le public perçoit alors très clairement le passage d'un développeur qui écrit tout lui-même à un développeur qui orchestre le travail d'un agent IA. C'est un fil conducteur visuel simple et très mémorable.
