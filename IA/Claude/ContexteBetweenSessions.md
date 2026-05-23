# Continuité entre sessions d'agent IA
Le problème classique : tokens épuisés, nouvelle session = contexte perdu. Voici les stratégies les plus efficaces :

## 1. Le fichier `CONTEXT.md` (la plus simple)
Dans ton projet, maintiens un fichier que l'agent met à jour lui-même à chaque fin de session :

```markdown
# CONTEXT.md

## État actuel
- Tâche en cours : Refactoring du module auth
- Fichier en cours : src/auth/middleware.ts (ligne 142)
- Prochaine étape : Ajouter le refresh token

## Décisions prises
- JWT stocké en httpOnly cookie (pas localStorage)
- Expiration : 15min access / 7j refresh

## Problèmes connus
- Bug ligne 87 : cas edge si user === null (non résolu)

## Commande pour reprendre
"Lis CONTEXT.md et continue la tâche"
```

Prompt de fin de session :
> "Mets à jour `CONTEXT.md` avec ce qu'on a fait, où on en est, et ce qu'il reste à faire"

## 2. Le fichier `TODO.md` avec statuts
```markdown
## TODO

- [x] Setup projet
- [x] Modèle User
- [~] Middleware auth (en cours - jwt.ts terminé, refresh à faire)
- [ ] Tests unitaires
- [ ] Deploy
```
L'agent voit immédiatement où reprendre avec [~].

## 3. Le fichier `AGENT_LOG.md` — journal horodaté
```markdown
## 2026-04-23 - Session 3
### Fait
- Créé UserService avec CRUD complet
- Corrigé bug #12 dans parser.ts

### Arrêté à
- Fichier : src/services/email.ts
- Raison : tokens épuisés

### Reprendre avec
- Implémenter sendVerificationEmail()
- Puis connecter à UserService.register()
```

## 4. Prompt de démarrage standardisé
Crée un fichier `RESUME.md` avec le prompt tout prêt :
```markdown
Lis les fichiers suivants dans l'ordre :
1. CONTEXT.md — état général du projet
2. TODO.md — tâches restantes
3. [fichier en cours] — reprends à la ligne indiquée

Ne réexplique pas ce qui est fait. Continue directement.
```
Tu copies-colles ça en début de nouvelle session.

## 5. Structure de dossier recommandée
```
mon-projet/
├── .agent/
│   ├── CONTEXT.md      ← état courant
│   ├── DECISIONS.md    ← choix d'architecture
│   ├── BUGS.md         ← problèmes ouverts
│   └── RESUME_PROMPT.md ← prompt de reprise
├── src/
└── ...
```

Règle d'or
> Dis à l'agent en début de session :<br>
> "Avant de commencer, lis .agent/CONTEXT.md. À la fin de chaque réponse importante, mets-le à jour."

Et en fin de session quand les tokens approchent :
> "Résume ce qu'on a fait et mets à jour CONTEXT.md pour la prochaine session"

Voici une architecture complète pour un projet :
```
mon-projet/
│
├── CLAUDE.md                  ← 🧠 Mémoire principale (stable)
├── CLAUDE.local.md            ← 🔒 Notes perso (gitignored)
│
├── .claude/
│   ├── commands/              ← Slash commands personnalisés
│   │   ├── resume.md          ← /resume  → reprendre la session
│   │   ├── status.md          ← /status  → état du projet
│   │   └── perf-audit.md      ← /perf-audit → workflow réutilisable
│   └── settings.json          ← Config MCP, permissions
│
├── .agent/                    ← 🔄 Mémoire dynamique (session)
│   ├── CONTEXT.md             ← État courant de la tâche en cours
│   ├── TODO.md                ← Backlog avec statuts [x] [~] [ ]
│   ├── DECISIONS.md           ← Choix d'archi et leur justification
│   └── BUGS.md                ← Problèmes ouverts
│
├── docs/
│   ├── architecture.md        ← Référencé via @docs/architecture.md
│   ├── api-standards.md
│   └── database-schema.md
│
└── src/
    └── api/
        └── CLAUDE.md          ← Règles spécifiques à ce module
```

Contenu type du CLAUDE.md racine : 
```markdown
# Mon Projet

## Stack
- Node.js 20, TypeScript, PostgreSQL, React 18
- ORM : Prisma (pas de raw SQL sauf migration)

## Commandes essentielles
- `npm run dev` — démarrer
- `npm test -- --watch` — tests en watch
- `npm run db:migrate` — migrations Prisma

## Conventions
- Nommage : camelCase pour vars, PascalCase pour composants
- Pas de `any` TypeScript
- Commits : conventional commits (feat/fix/chore)

## Architecture
@docs/architecture.md
@docs/api-standards.md

## État session courante
@.agent/CONTEXT.md
@.agent/TODO.md
```