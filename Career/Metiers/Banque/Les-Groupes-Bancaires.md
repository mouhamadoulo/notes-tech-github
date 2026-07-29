
# Tutoriel : Comprendre les groupes bancaires français

La France possède l'un des systèmes bancaires les plus concentrés d'Europe. La majorité des banques que les particuliers utilisent appartiennent à seulement quelques grands groupes financiers.

---

# 1. Vue d'ensemble

On distingue plusieurs types d'établissements :

* Banques commerciales
* Banques mutualistes
* Banques d'investissement
* Banques publiques
* Banques en ligne
* Filiales spécialisées (crédit, assurance, leasing...)

Les principaux groupes sont :

| Groupe                          | Type               |
| ------------------------------- | ------------------ |
| BNP Paribas                     | Banque commerciale |
| Crédit Agricole                 | Banque mutualiste  |
| BPCE                            | Banque mutualiste  |
| Société Générale                | Banque commerciale |
| Crédit Mutuel Alliance Fédérale | Banque mutualiste  |
| La Banque Postale               | Banque publique    |

---

# 2. Le paysage bancaire français

```mermaid
graph TD

A[Système bancaire français]

A --> BNP[BNP Paribas]
A --> CA[Crédit Agricole]
A --> BPCE
A --> SG[Société Générale]
A --> CM[Crédit Mutuel]
A --> LBP[La Banque Postale]
```

---

# 3. Qui possède ces banques ?

Toutes les banques ne sont pas détenues de la même manière.

## BNP Paribas

Entreprise cotée en bourse.

Les actionnaires sont principalement :

* investisseurs institutionnels
* fonds internationaux
* investisseurs particuliers
* salariés

```mermaid
graph LR

A[Actionnaires]

A --> B[Fonds d'investissement]
A --> C[Investisseurs privés]
A --> D[Salariés]
```

---

## Société Générale

Même modèle :

* cotée en bourse
* capital réparti entre investisseurs mondiaux

---

## Crédit Agricole

Le fonctionnement est très différent.

Les clients sociétaires possèdent les caisses locales.

Les caisses locales possèdent les caisses régionales.

Les caisses régionales contrôlent Crédit Agricole SA.

```mermaid
graph TD

S[Sociétaires]

S --> CL[Caisses Locales]

CL --> CR[Caisses Régionales]

CR --> CASA[Crédit Agricole SA]

CASA --> FILIALES
```

---

## BPCE

Fusion de deux réseaux :

* Banques Populaires
* Caisses d'Épargne

Les sociétaires possèdent leurs banques.

```mermaid
graph TD

S[Sociétaires]

S --> BP[Banques Populaires]

S --> CE[Caisses d'Épargne]

BP --> BPCE

CE --> BPCE
```

---

## Crédit Mutuel

Même logique mutualiste.

```mermaid
graph TD

Clients --> Sociétaires

Sociétaires --> CaissesLocales

CaissesLocales --> Fédération

Fédération --> CréditMutuel
```

---

## La Banque Postale

Elle est détenue par :

```text
État Français
        │
        ▼
Caisse des Dépôts
        │
        ▼
La Poste
        │
        ▼
La Banque Postale
```

---

# 4. Les grandes filiales

## BNP Paribas

```mermaid
graph TD

BNP[BNP Paribas]

BNP --> Hello[Hello bank!]

BNP --> Cardif

BNP --> Leasing

BNP --> PersonalFinance

BNP --> Arval

BNP --> CIB[Corporate & Investment Banking]
```

### Description

* Hello bank! → banque en ligne
* Cardif → assurance
* Arval → location automobile
* Personal Finance → crédit à la consommation
* CIB → banque d'investissement

---

## Crédit Agricole

```mermaid
graph TD

CA[Crédit Agricole]

CA --> LCL

CA --> Amundi

CA --> CAA[Crédit Agricole Assurances]

CA --> CACIB

CA --> Sofinco

CA --> BforBank
```

---

## BPCE

```mermaid
graph TD

BPCE

BPCE --> BanquePopulaire

BPCE --> CaisseEpargne

BPCE --> Natixis

BPCE --> Oney

BPCE --> BanquePalatine
```

---

## Société Générale

```mermaid
graph TD

SG

SG --> Bourso[Boursorama / BoursoBank]

SG --> ALD

SG --> SGAssurances

SG --> SGCIB
```

---

## Crédit Mutuel

```mermaid
graph TD

CM

CM --> CIC

CM --> Cofidis

CM --> Fortuneo

CM --> AssurancesCM

CM --> BanqueEuropéenne
```

---

## La Banque Postale

```mermaid
graph TD

LBP

LBP --> CNP

LBP --> LBPAM

LBP --> Assurance

LBP --> Financement
```

---

# 5. Les métiers d'un groupe bancaire

Une banque moderne est souvent organisée comme un conglomérat.

```mermaid
graph LR

Banque

Banque --> BanqueDeDetail

Banque --> Assurance

Banque --> GestionActifs

Banque --> BanqueInvestissement

Banque --> CréditConso

Banque --> Leasing

Banque --> Paiement

Banque --> Immobilier
```

---

# 6. Exemple : Crédit Agricole

```mermaid
graph TD

Clients

Clients --> Agence

Agence --> CaisseLocale

CaisseLocale --> CaisseRegionale

CaisseRegionale --> CreditAgricoleSA

CreditAgricoleSA --> LCL

CreditAgricoleSA --> Amundi

CreditAgricoleSA --> Sofinco

CreditAgricoleSA --> CACIB

CreditAgricoleSA --> Assurances
```

---

# 7. Exemple : BNP Paribas

```mermaid
graph TD

BNP

BNP --> BanqueParticuliers

BNP --> BanqueEntreprises

BNP --> BanqueInvestissement

BNP --> Cardif

BNP --> HelloBank

BNP --> Arval

BNP --> Leasing

BNP --> PersonalFinance
```

---

# 8. Comparaison des principaux groupes

| Groupe            | Coté en bourse | Mutualiste | Banque en ligne | Assurance | Gestion d'actifs | Banque d'investissement |
| ----------------- | -------------- | ---------- | --------------- | --------- | ---------------- | ----------------------- |
| BNP Paribas       | ✅              | ❌          | Hello bank!     | Cardif    | Oui              | Oui                     |
| Société Générale  | ✅              | ❌          | BoursoBank      | Oui       | Oui              | Oui                     |
| Crédit Agricole   | ✅ (holding)    | ✅          | BforBank        | Oui       | Amundi           | Oui                     |
| BPCE              | ❌              | ✅          | Oui             | Oui       | Oui              | Oui                     |
| Crédit Mutuel     | ❌              | ✅          | Fortuneo        | Oui       | Oui              | Oui                     |
| La Banque Postale | ❌              | Public     | Oui             | Oui       | Oui              | Limité                  |

---

# 9. Les participations croisées

Les grands groupes possèdent souvent plusieurs sociétés spécialisées.

```mermaid
graph LR

Banque

Banque --> Assurance

Banque --> GestionActifs

Banque --> Paiement

Banque --> Crédit

Banque --> Immobilier

Banque --> Leasing

Banque --> BanqueEnLigne
```

---

# 10. Résumé

```mermaid
mindmap
  root((Banques françaises))
    BNP Paribas
      Hello bank!
      Cardif
      Arval
      Personal Finance
    Crédit Agricole
      LCL
      Amundi
      Sofinco
      Assurances
    BPCE
      Banque Populaire
      Caisse d'Épargne
      Natixis
      Oney
    Société Générale
      BoursoBank
      ALD
      SG CIB
    Crédit Mutuel
      CIC
      Cofidis
      Fortuneo
    La Banque Postale
      CNP Assurances
      LBP AM
```

## À retenir

* Les **banques commerciales** (BNP Paribas, Société Générale) sont principalement détenues par des actionnaires via les marchés financiers.
* Les **banques mutualistes** (Crédit Agricole, BPCE, Crédit Mutuel) appartiennent indirectement à leurs **sociétaires**, selon une structure en caisses locales et régionales.
* **La Banque Postale** est un groupe public, contrôlé via La Poste et la Caisse des Dépôts.
* Chaque grand groupe est organisé autour de nombreuses **filiales spécialisées** : banque de détail, assurance, crédit à la consommation, gestion d'actifs, banque d'investissement, leasing, paiements et banque en ligne.
* Les grands groupes français sont devenus des **conglomérats financiers**, capables de couvrir presque tous les besoins des particuliers, des entreprises et des investisseurs.
