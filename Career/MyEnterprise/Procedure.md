Oui, **tu peux techniquement créer une micro-entreprise maintenant**, même pendant ta période d'essai. Mais vu la formulation « travaillera exclusivement au profit du Groupe » de ton CDI, je te recommande de distinguer **créer la micro-entreprise** et **commencer à accepter des missions potentiellement concurrentes**. 

Ton contrat ne dit pas que tu dois attendre la fin de la période d'essai pour créer une entreprise. En revanche, ton obligation de loyauté reste applicable, et ton contrat interdit notamment les comportements de concurrence déloyale et le détournement de clientèle. 

Pour ton projet de prestations de développement informatique, voici le parcours que je suivrais.

### 1. Définis ton activité

Pour des missions du type développement Java/Spring Boot/Angular, création d'applications web, conseil et assistance technique, tu peux déclarer quelque chose comme :

> **Développement informatique, conception et réalisation d'applications web et logiciels, conseil et prestations de services informatiques.**

Tu n'as pas besoin de créer une SASU avec capital, statuts, compte professionnel, comptable, etc. Pour commencer avec quelques missions en complément de ton CDI, **Entreprise individuelle sous régime micro-entrepreneur** est beaucoup plus simple.

### 2. Fais la création exclusivement via le Guichet unique

La création se fait via le **Guichet unique des formalités d'entreprises géré par l'INPI**. Le portail permet de créer, modifier et fermer une entreprise. ([Opposition Brevet][1])

[Guichet unique INPI – créer son entreprise](https://procedures.inpi.fr/?utm_source=chatgpt.com)

Évite les sites privés qui proposent de « créer ta micro-entreprise » contre 50 €, 100 € ou davantage.

### 3. Crée ton compte puis lance une formalité de création

Sur le portail, tu vas essentiellement choisir une création d'entreprise en tant que **personne physique / entrepreneur individuel**, avec le régime micro-entrepreneur.

Tu renseigneras ensuite ton identité, ton adresse, les informations relatives à l'entreprise et ton activité.

Pour l'activité, décris précisément ce que tu vas faire plutôt que d'essayer de choisir toi-même un code APE. Le code APE sera déterminé à partir de ton activité déclarée.

### 4. Choisis une date de début d'activité intelligemment

Tu n'es pas obligé de créer ta micro aujourd'hui si tu n'as encore aucune mission.

Dans ton cas, je vois deux possibilités :

**Option A — tu veux commencer à chercher et facturer rapidement :** crée-la maintenant.

**Option B — tu veux seulement être prêt pour plus tard :** attends d'avoir une première mission sérieuse.

Je préfère généralement **B** : il n'y a pas énormément d'intérêt à créer une structure six mois avant d'avoir ton premier client.

### 5. Domicilie l'entreprise

Une micro-entreprise peut notamment être domiciliée à ton domicile sous certaines conditions.

Tu n'as donc pas besoin de louer un bureau simplement pour faire du développement informatique remote.

Attention cependant à la publication de certaines informations personnelles lors de la création ; l'INPI permet désormais de gérer davantage la confidentialité des actes et données personnelles. ([INPI][2])

### 6. Choisis tes options fiscales/sociales avec attention

C'est une partie où je ne te conseille pas de cocher les options au hasard.

La micro fonctionne avec des **cotisations calculées forfaitairement sur ton chiffre d'affaires**. L'Urssaf rappelle également que les charges réelles ne sont pas déductibles dans le régime micro. ([Mon Entreprise][3])

Il faudra notamment choisir ta périodicité de déclaration de chiffre d'affaires.

Pour quelqu'un qui fait du freelance occasionnel en complément d'un CDI, **mensuel** peut être pratique : tu encaisses → tu déclares → tu paies les cotisations correspondantes.

Il faudra aussi examiner le **versement libératoire de l'impôt sur le revenu**. Ne le sélectionne pas automatiquement : son intérêt et ton éligibilité dépendent notamment de ta situation fiscale.

### 7. Ne compte pas automatiquement sur l'ACRE

Point important en **2026** : les règles ont changé.

L'ACRE n'est plus simplement quelque chose dont tous les nouveaux micro-entrepreneurs bénéficient automatiquement. L'Urssaf indique désormais des conditions d'éligibilité spécifiques et une demande à déposer dans les **60 jours suivant la création**. ([Mon Entreprise][4])

Donc il faudra vérifier ton éligibilité au moment de la création.

### 8. Après validation, récupère tes identifiants

Après traitement de la formalité, ton entreprise sera enregistrée et tu obtiendras notamment ton **SIREN/SIRET** et ton **code APE**.

À partir de là tu pourras réellement fonctionner comme indépendant :

**Client → contrat/devis → prestation → facture → paiement → déclaration du CA → cotisations Urssaf**

### 9. Crée ensuite ton espace Urssaf

Une fois ton entreprise enregistrée, tu utiliseras l'espace micro-entrepreneur pour déclarer ton chiffre d'affaires et payer les cotisations.

[Portail officiel Autoentrepreneur Urssaf](https://www.autoentrepreneur.urssaf.fr/?utm_source=chatgpt.com)

Même lorsque ton CA est nul, respecte les obligations déclaratives applicables.

### 10. Mets en place une comptabilité très simple

L'un des gros avantages de la micro est précisément sa simplicité.

Pour commencer, organise au minimum :

```text
MICRO-ENTREPRISE
│
├── Clients/
│   ├── Client-A/
│   │   ├── contrat.pdf
│   │   ├── devis.pdf
│   │   └── factures/
│   └── Client-B/
│
├── Factures/
│   ├── 2026-001.pdf
│   ├── 2026-002.pdf
│   └── ...
│
├── Comptabilité/
│   └── livre-recettes.xlsx
│
└── Administratif/
    ├── INPI/
    ├── URSSAF/
    └── Impôts/
```

Tu dois notamment conserver une trace correcte de tes recettes et émettre des factures conformes.

### 11. Prévois également la CFE

Ne pense pas uniquement aux cotisations Urssaf.

Il existe également la **cotisation foncière des entreprises (CFE)**. Le simulateur officiel Urssaf rappelle qu'elle peut notamment intervenir à partir de la deuxième année d'exercice et que son montant dépend entre autres de la domiciliation. ([Mon Entreprise][3])

### 12. Et surtout : sépare HN de ta micro

C'est le point le plus important compte tenu de ton contrat.

Ton activité personnelle devrait être complètement indépendante :

**CDI HN**
→ horaires HN
→ PC HN/client
→ Git HN/client
→ données HN/client
→ clients HN

**Micro**
→ soir/week-end
→ PC personnel
→ GitHub personnel
→ licences personnelles
→ clients personnels
→ factures personnelles

Ton contrat est particulièrement strict concernant les données clients : il interdit notamment leur transfert ou stockage sur des appareils ou espaces personnels non autorisés. 

Et je ne prendrais **aucune mission pour un client HN** ou obtenue grâce à une relation créée dans le cadre de ton CDI. Ton contrat traite spécifiquement ce sujet. 

### Le point que je sécuriserais avant ta première mission

Ta création elle-même ne me paraît pas être le principal risque. C'est **l'exercice effectif d'une deuxième activité de prestations informatiques**, compte tenu de cette phrase de ton article 12 :

> « Le Salarié travaillera exclusivement au profit du Groupe. » 

Il existe bien dans le Code du travail un dispositif spécifique concernant la clause d'exclusivité lors de la création/reprise d'une entreprise, mais ton contrat et ta situation méritent d'être vérifiés précisément avant de te dire que cette protection rend sans risque telle ou telle mission. Je ne traiterais donc pas la création de la micro comme une autorisation automatique de concurrencer ton employeur.

**En pratique, tu peux déjà préparer/créer ta micro**, mais avant d'accepter ta première mission Java/Angular, je ferais vérifier le triptyque **activité du client + relation éventuelle avec HN + clause d'exclusivité/loyauté**.

Et comme tu es encore en période d'essai, il y a aussi un aspect purement pratique : je serais particulièrement prudent avant le **25 août et le démarrage de ta première mission client HN**. La période d'essai est plus facile à rompre que le CDI une fois celle-ci terminée.

Si tu veux, je peux maintenant te guider **écran par écran dans la déclaration INPI**, en te disant exactement **quoi sélectionner pour une micro-entreprise de développeur Java/Angular freelance** (activité, fiscalité, versement libératoire, TVA, périodicité, nom commercial, etc.).

[1]: https://oppobrv.inpi.fr/?utm_source=chatgpt.com "Portail e-procedures - INPI"
[2]: https://www.inpi.fr/realiser-demarches/formalites-dentreprises/deposer-actes?utm_source=chatgpt.com "Déposer des actes | INPI"
[3]: https://mon-entreprise.urssaf.fr/iframes/simulateur-autoentrepreneur?utm_source=chatgpt.com "Auto-entrepreneurs : simulateur de revenus - Mon-entreprise"
[4]: https://mon-entreprise.urssaf.fr/documentation/modele-ti/ind%C3%A9pendant/cotisations-et-contributions/cotisations/exon%C3%A9rations/Acre?currentEngineId=993799558&utm_source=chatgpt.com "Acre - Mon-entreprise"
