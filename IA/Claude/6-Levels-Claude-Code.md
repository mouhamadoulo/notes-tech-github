# Les 6 niveaux de Claude Code

# NIVEAU 1 : LE PROMPTEUR  
> « Claude est un outil »

**TU ES À CE NIVEAU QUAND**  
- Tu ouvres Claude Code et tu commences immédiatement à écrire des prompts — sans configuration, sans contexte  
- « Construis-moi une landing page » — en une seule fois  
- Tu vois Claude comme : commande > résultat  

**COMPÉTENCES À MAÎTRISER**  
* [ ] Écrire des prompts clairs et précis  
* [ ] Lire et évaluer les réponses de Claude  
* [ ] Bases de l’utilisation du terminal  

**PIÈGE : Régression vers la moyenne**  
> Pas de contexte = Claude devine tout  
> Deviner = moyenne des données d’entraînement = résultat médiocre  
> C’est là que vivent 90 % des gens  

**DÉBLOQUER > Niveau 2 :**  
Arrête de donner des ordres. Commence à demander à Claude de planifier AVEC toi.  

---

# NIVEAU 2 : LE PLANIFICATEUR  
> « Claude est un partenaire »

**TU ES À CE NIVEAU QUAND**  
- Tu utilises le mode Plan avant de construire  
- Tu demandes : « Qu’est-ce que j’oublie ici ? »  
- Tu discutes de l’architecture avant d’écrire une seule ligne de code  

**COMPÉTENCES À MAÎTRISER**  
* [ ] Mode Plan + réflexion approfondie  
* [ ] Questionnement collaboratif : « Qu’est-ce qu’un expert verrait ici que je ne vois pas ? »  
* [ ] Laisser Claude remettre en question tes idées  

**PIÈGE : Planifier à l’aveugle**  
> Super conversation, mais Claude continue de deviner ton projet, ta stack, tes préférences  
> Les mots ne suffisent pas  

**DÉBLOQUER > Niveau 3 :**  
La conversation ne sert à rien si Claude n’a pas les bonnes informations.  

---

# NIVEAU 3 : L’INGÉNIEUR DU CONTEXTE  
> « Tu n’es pas un développeur — tu es un curateur »

**TU ES À CE NIVEAU QUAND**  
- Tu écris et maintiens des fichiers `CLAUDE.md`  
- Tu réfléchis à ce que Claude voit vs ce qu’il ne voit pas  
- Tu utilises `/clear` entre les tâches  

**COMPÉTENCES À MAÎTRISER**  
* [ ] CLAUDE.md léger (pas surchargé)  
* [ ] Exemples courts (few-shot) plutôt que longues instructions  
* [ ] Gestion de la fenêtre de contexte + `/clear`  
* [ ] Le bon contexte au bon moment  

**PIÈGE : CLAUDE.md surchargé**  
> ETH Zurich : des fichiers de contexte générés par LLM ont rendu les agents MOINS performants dans 5 cas sur 8  
> Moins, c’est mieux (1/3)  

**DÉBLOQUER > Niveau 4 :**  
Tu as besoin de capacités au-delà de ce que Claude offre nativement. Il est temps d’aller plus loin.  

---

# NIVEAU 4 : LE CRÉATEUR D’OUTILS  
> « Tu équipes Claude »

**TU ES À CE NIVEAU QUAND**  
- Tu installes des MCP (Supabase, Figma, Playwright)  
- Tu connectes Claude au monde extérieur  
- Tu expérimentes avec des frameworks  

**COMPÉTENCES À MAÎTRISER**  
* [ ] Sélection précise des MCP (le bon outil pour le bon besoin, selon le projet)  
* [ ] Compréhension des briques techniques (frontend/backend/auth/data)  
* [ ] Demander à Claude : « explique COMMENT tu as fait ça »  

**PIÈGE : Le magasin de bonbons**  
> Capacité ≠ performance  
> 15 outils = Claude choisit le mauvais  
> Plus d’options, pires décisions  
> Moins, c’est mieux (2/3)  

**DÉBLOQUER > Niveau 5 :**  
Tu répètes toujours les mêmes workflows. Et si Claude les connaissait déjà ?  

---

# NIVEAU 5 : LE SPÉCIALISTE  
> « Claude devient le tien »

**TU ES À CE NIVEAU QUAND**  
- Tu crées des compétences personnalisées qui reflètent TES standards et workflows  
- Ta configuration Claude est unique  
- Le Claude des autres ne fonctionne pas comme le tien  

**COMPÉTENCES À MAÎTRISER**  
* [ ] Création de skills (ton style, tes règles)  
* [ ] Commandes slash (`.claude/commands/`)  
* [ ] Workflows (pipelines multi-étapes)  
* [ ] Créateur de skills pour construire les tiens  

**PIÈGE : Surcharge de skills**  
> Trop de skills = Claude ne choisit pas le bon  
> 20–30 bien sélectionnés > 1000 génériques  
> Moins, c’est mieux (3/3)  

**DÉBLOQUER > Niveau 6 :**  
Un seul agent ne suffit plus. Tu as besoin d’exécution en parallèle.  

---

# NIVEAU 6 : L’ORCHESTRATEUR  
> « Tu n’es plus un curateur — tu es un manager »

**TU ES À CE NIVEAU QUAND**  
- Tu exécutes plusieurs sessions Claude en parallèle  
- Tu utilises des worktrees pour isoler les agents  
- Tu décides QUOI faire, pas COMMENT  

**COMPÉTENCES À MAÎTRISER**  
* [ ] Division des tâches + planification parallèle  
* [ ] Worktrees (`--worktree` / `-w`)  
* [ ] Gestion de sessions multi-terminal  
* [ ] Équipes d’agents (la frontière actuelle)  

**PIÈGE : Sauter les bases**  
> Sans les niveaux 1 à 5, tu obtiens cinq agents produisant cinq variantes de résultats médiocres  
> Chaque niveau nourrit celui-ci  

**LE PARCOURS COMPLET**  
Tu es passé de donner des ordres à un outil à gérer une équipe qui s’auto-organise.