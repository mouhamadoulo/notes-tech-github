# 🧠 1. <ins>Les bases pour bien prompter Codex</ins>

## 🎯 1.1 Sois ultra précis (le plus important)
Un mauvais prompt :
> “Crée une app React”

Un bon prompt :
> “Crée une app React avec TypeScript, architecture feature-based, gestion d’état avec Zustand, authentification JWT, et un dashboard avec graphiques.”

👉 Plus tu es précis, plus Codex est performant

## 🧩 1.2 Donne du contexte (énorme levier)
Codex fonctionne comme un dev junior très rapide :

👉 Il a besoin de :
- stack (React, Node, etc.)
- conventions
- structure projet
- contraintes

💡 Astuce :
Ajoute toujours :
```
Contexte:
- Projet: SaaS de gestion de tâches
- Stack: Next.js + Prisma + PostgreSQL
- Architecture: Clean architecture
```

## 🧱 1.3 Structure ton prompt (template pro)
Utilise ce format :
```
Objectif:
Tâche précise

Contexte:
Infos projet

Contraintes:
- Langage
- Libs
- Style

Output attendu:
- Code
- Explication
- Tests
```

👉 Ça change radicalement la qualité

## 🔁 1.4 Travaille en itérations (pas en one-shot)
Les devs qui réussissent avec Codex ne font PAS :
> “génère toute l’app”

Mais plutôt :
- 1.Architecture
- 2.Feature 1
- 3.Refacto
- 4.Tests
- 5.Optimisation

👉 C’est confirmé : approche step-by-step > one-shot

# 🚀 2. <ins>Techniques avancées (niveau intermédiaire → avancé)</ins>

## 🧪 2.1 Demande d’abord un plan (très puissant)
Avant de coder :
```
Analyse le problème et propose une architecture détaillée avant d’écrire du code
```

👉 Technique proche de **“guided code generation”**<br>
→ améliore fortement la qualité

## 🧠 2.2 Fais “penser” Codex comme un dev
Exemple :
```
Avant de coder:
1. Liste les étapes
2. Identifie les edge cases
3. Propose des tests
Puis code
```

## 📦 2.3 Utilise des exemples (few-shot)
```
Voici un exemple de fonction dans mon projet:
[code]

Maintenant fais pareil pour:
...
```

👉 Ça aligne Codex avec ton style

## 🧾 2.4 Force un format de sortie
Ex :
```
Réponds avec:
1. Code
2. Explication
3. Tests unitaires
```

# 🗂️ 3. <ins>Suivre la progression du projet (très important)</ins>

## 🔄 3.1 Utilise Git comme source de vérité
- commits fréquents
- PR par feature
- rollback facile

👉 recommandé pour suivre l’état du projet

## 📊 3.2 Laisse Codex travailler comme un agent
Avec Codex :
- il peut modifier fichiers
- lancer tests
- afficher logs
- proposer PR

👉 Tu peux suivre :
- logs terminal
- résultats tests
- diff Git

## 🧭 3.3 Demande explicitement le suivi
Prompt utile :
```
Travaille étape par étape et maintiens un log de progression
```
Ou :
```
Après chaque étape:
- explique ce que tu as fait
- indique ce qu’il reste à faire
```

## 📁 3.4 Crée un fichier type “AGENTS.md”
C’est une killer feature :

👉 Tu expliques à Codex :
- comment lancer le projet
- conventions
- commandes tests
- structure code

➡️ Résultat : beaucoup plus cohérent

## 📌 3.5 Sépare ton projet en tâches
Exemple roadmap :
- Auth
- Dashboard
- API
- Paiement
- Notifications

👉 Puis tu prompts par bloc

## 3.6 Astuce pour ne pas "perdre la main" entre deux sessions
```
Si Codex semble avoir "oublié" l'état d'avancement de votre projet après une réouverture,
la meilleure pratique en vibe coding est d'adopter le "Context Dumping" :
Le conseil pro : Avant de fermer votre session, demandez à l'IA :
"Résume l'état actuel du projet, les derniers changements 
et ce qu'il reste à faire dans un fichier PROGRESS.md."
> À la réouverture, il vous suffit de dire : "Lis PROGRESS.md et attends mes instructions."
```

# ⚙️ 4. <ins>Workflow pro pour créer une app avec Codex</ins>

## 🧱 Étape 1 – cadrage
```
Décris l’application + demande une architecture
```

## 🧩 Étape 2 – setup projet
```
Initialise projet + config
```

## 🧪 Étape 3 – features
```
Implémente feature X
```

## 🔁 Étape 4 – refacto
```
Améliore structure + perf
```

## ✅ Étape 5 – tests
```
Ajoute tests unitaires
```

## 🚀 Étape 6 – déploiement
```
Prépare déploiement (Docker / Vercel)
```

# 💡 5. <ins>Astuces qui font la différence</ins>

## ⚡ 5.1 Parle comme un chef de projet
Tu ne codes plus → tu orchestres

## 🧠 5.2 Toujours vérifier le code
Codex n’est pas infaillible

## 🧩 5.3 Donne de la documentation
Plus il comprend ton projet → meilleur il est

## 🔄 5.4 Re-prompt au lieu de corriger toi-même
Ex :
```
Refactor ce code pour améliorer la lisibilité et réduire la complexité
```

## 🧪 5.5 Toujours inclure les tests
Codex sait les écrire → énorme gain

# 🧠 Résumé simple
👉 Pour réussir avec Codex :
- Prompt = clair + structuré + contextualisé
- Travail = itératif (pas one-shot)
- Suivi = Git + logs + étapes
- Organisation = comme un chef de projet