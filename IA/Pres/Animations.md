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

---

Vu le public, je pense que la présentation ne doit pas être une présentation **sur les outils**, mais une présentation **sur l'impact de l'IA sur le métier de développeur**. Les commerciaux veulent comprendre **ce que cela change pour les clients**, tandis que les développeurs veulent comprendre **comment on travaille avec ces outils**.

Je structurerais donc la présentation autour de 5 grands thèmes.

---

# 1. Pourquoi toutes les ESN parlent aujourd'hui d'IA ?

Commencer par répondre à la question que tout le monde se pose.

Expliquer qu'il y a plusieurs niveaux d'IA dans le développement.

Animation :

Une pyramide qui se construit.

```
                 Agents IA
            (développement autonome)

           Copilotes de développement

         IA générative (ChatGPT, Lucia...)

      Automatisation classique (scripts, CI...)
```

Message :

> Toutes les entreprises ne sont pas au même niveau de maturité.

---

# 2. Où intervient réellement l'IA dans un projet logiciel ?

Très intéressant pour des commerciaux.

Faire apparaître tout le cycle de vie d'un projet.

```
Besoin métier

↓

Conception

↓

Développement

↓

Tests

↓

Documentation

↓

Déploiement

↓

Maintenance
```

Puis faire apparaître des icônes IA à chaque étape.

Par exemple :

```
Besoin métier      🤖

Conception         🤖

Développement      🤖

Tests              🤖

Documentation      🤖

Déploiement        🤖

Maintenance        🤖
```

Le public réalise que l'IA ne sert pas uniquement à générer du code.

---

# 3. Les gains observés

Faire un radar.

```
                Qualité

       Temps              Documentation

      Productivité      Onboarding

              Satisfaction développeur
```

Puis colorier progressivement les axes.

C'est plus parlant qu'un tableau.

---

# 4. Les limites de l'IA

Sujet très apprécié des décideurs.

Faire un iceberg.

```
               IA écrit du code

──────────────────────────────

Validation

Architecture

Sécurité

Métier

Tests

Performance

Responsabilité
```

Message :

Le code généré est seulement la partie visible.

Le vrai travail du développeur reste sous l'eau.

---

# 5. Les hallucinations

Faire une animation amusante.

À gauche :

```
Question

↓

IA

↓

Réponse
```

Puis une croix rouge.

Exemple :

L'IA invente une API Java.

Ensuite :

```
Question

↓

Développeur

↓

Validation

↓

Bonne réponse
```

Message :

L'IA est très rapide mais pas infaillible.

---

# 6. Prompt vs contexte

Sujet que peu de gens connaissent.

Animation.

```
Prompt court

↓

Résultat moyen
```

Puis

```
Contexte

Architecture

Stack technique

Contraintes

Prompt

↓

Excellent résultat
```

Montrer que la qualité dépend surtout du contexte fourni.

---

# 7. L'évolution du rôle du développeur

Faire évoluer une silhouette.

```
2015

👨‍💻
Code
```

↓

```
2020

👨‍💻

Code

Tests
```

↓

```
2024

👨‍💻🤖

Architecture

Validation

Prompt

Relecture
```

↓

```
2026

👨‍💻

Chef d'orchestre

🤖 🤖 🤖
```

C'est souvent la diapo qui marque le plus.

---

# 8. Développement classique vs développement agentique

Faire deux colonnes.

Classique

```
Créer Controller

Créer Service

Créer Repository

Créer DTO

Créer Tests
```

Animation.

À droite.

```
Créer la fonctionnalité "Gestion des clients"

↓

Agent

↓

8 fichiers modifiés
```

Les commerciaux comprennent immédiatement.

---

# 9. ROI

Sujet qu'ils adorent.

Faire un avant/après.

```
Avant

Recherche : 30 min

Code : 2 h

Tests : 1 h

Documentation : 45 min
```

↓

```
Après

Recherche : 5 min

Code : 45 min

Tests : 20 min

Documentation : 5 min
```

Même sans chiffres précis, montrer une tendance.

---

# 10. Les risques

Faire une balance.

À gauche

```
Productivité
```

À droite

```
Sécurité

Confidentialité

Dépendance

Qualité
```

Puis faire apparaître un équilibre.

Message :

Il faut gouverner l'utilisation de l'IA.

---

# 11. Les différents types d'IA

Très pédagogique.

Créer une échelle.

```
Assistant

↓

Copilot

↓

Agent

↓

Équipe d'agents
```

Puis faire apparaître des exemples.

Assistant

➡ répond

Copilot

➡ propose

Agent

➡ agit

Équipe d'agents

➡ collabore

---

# 12. Multi-agents

Sujet très actuel.

Faire un organigramme.

```
                 Développeur

                     │

          ───────────┼───────────

          │          │           │

    Agent Backend

    Agent Front

    Agent Tests

          │          │           │

          └──────────┼───────────

               Pull Request
```

Les commerciaux adorent cette image.

---

# 13. L'avenir

Une roadmap.

```
2023

ChatGPT

↓

2024

Copilot

↓

2025

Agents

↓

2026

Équipes d'agents

↓

Demain

Développement autonome supervisé
```

---

# 14. Une diapo interactive

Très sympa.

Titre :

**"Qui fait quoi ?"**

Faire apparaître une fonctionnalité.

```
Créer une API de gestion des commandes
```

Puis demander au public.

Qui fait quoi ?

Ensuite faire apparaître.

```
IA

✔ CRUD

✔ DTO

✔ Mapping

✔ Tests

✔ Documentation
```

Puis

```
Développeur

✔ Architecture

✔ Validation

✔ Sécurité

✔ Métier

✔ Performance
```

Cela crée de l'interaction et renforce le message que l'IA est un **outil d'augmentation**, pas un remplacement.

---

## Ce qui fera vraiment la différence

Avec un public composé majoritairement de commerciaux, je terminerais par une diapositive qui répond à une question simple : **"Pourquoi un client devrait-il s'intéresser à l'IA dans ses projets de développement ?"**

Un schéma très parlant est celui-ci :

```text
                 CLIENT

                    │

      Besoin métier exprimé

                    ▼

        👨‍💻 + 🤖 IA (équipe augmentée)

                    ▼

      ✔ Livraison plus rapide

      ✔ Plus de temps pour le métier

      ✔ Documentation améliorée

      ✔ Qualité plus homogène

      ✔ Montée en compétence facilitée

      ✔ Innovation accélérée
```

Cette conclusion parle à tout le monde : les développeurs y voient l'évolution de leur manière de travailler, et les commerciaux repartent avec des bénéfices concrets qu'ils pourront expliquer à leurs clients. Le fil rouge devient alors **"l'IA ne remplace pas le développeur, elle augmente les capacités de toute l'équipe et permet de concentrer l'expertise humaine sur les décisions à forte valeur ajoutée."**

