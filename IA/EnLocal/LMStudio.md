LM Studio est une application de bureau qui permet de **télécharger, exécuter et utiliser des modèles de langage (LLM) localement sur votre ordinateur**, sans dépendre d'une API cloud comme ChatGPT ou Claude.

![Image](https://images.openai.com/static-rsc-4/cmTJco2HQwEpii1pRhC9dCiQbKa14XI6KtDzL5dUEzSX95XvLLBgtrvzTcvnWQp9K2JXw1RiszhWr9qQRAQRwCOHPcMKO-u_dK1U37trKLa9Bbh5HrcGEYNfkXtkvzZJptgqJV40MPkJdNZzlbFPxG2Xs07oE1BcRvp4W1WOLBLKxlgp-aHP3E4hefhMK3n4?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/sS_RyX27eRgEB0u5yNYZHRl8myBHX1gztwjGlYhw9LnbcPcz79m1gQUZraxiF77vh9LqYpS391yJEvtoOnduTaEt5b8hUEnC5Z6j8li7gWYqG0dOL0bYAQdAloCdBdkybagmOmmuN6lJzTUYD_luiA3Sh39n0OxnefP6yJTqTVv2mDbq5tkNkzs8Dh0i86JI?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/CyEOA9_ENguY5hIxcWtK7sEMutXHc_GVSJpf4S0S4QTd9eEaXp6ZH50QJXAo1ZEIZ7yDqWs5QgejBkTC-e6Wf_4MYi7rBducj0pnakRmiQJpiAs_UE3MScntdlZcfvzfFq-NlOb6M-SzheGVE0XTTeuRvThf8eL48JuF9llXiOf7iz4kliuwAC5w1wMqUNQj?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/uPuVa4ZUbItB8gnLpU48rNIj0m6ok77V4WHUAVhNsnQnpgKL-ererOzYcxK9f533788f7uihXMu_Xdvlc5E0Fk4-YLU2iiP7czF8mH6mJCtPqJzDjtmiHI2HUiDawHYduj4BOEI46txxowh8G4xeu2ixu-sh6xJtPLebVRisXNh3IlNGBK1ltWLIQOyiUFRr?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/PjsLcCjBIACjZkcSuwHWC_MmwWSbBMvPgMMa6NPGfWoOrOhD2IXZ8RU4NV5DDYFwv0_vVgJNJuWAc7pLwLWW3Pjm9uDCnvzXw3Lz6tJTuYCm9jNW7qPjaJ9T2adLBc8K1-oGlKDt2kk1DKSLJyPpJppc-bpJ94U1Obc6bKkUmFkCRMkoczRWnSP6pPAG5GjG?purpose=fullsize)

![Image](https://images.openai.com/static-rsc-4/jkFXaFmH_8JMuKv0YtxkZYaJOjuQJo9BMHrYZFbMkQMCIz7ZiH0c6Ga9T5Y5ucQ54V-hHH2KiFQEuWYHd-9Sm5csEe3r5pYJS4mNDvTtT9oj1PPvOdSqt30ySf7C9oW2mSVi2xKbUZHx6H9gErercVpfriX57wi3_gk0Ns3xNx9mocVNuqc_Pe5FhUcWb7ba?purpose=fullsize)

## À quoi sert LM Studio ?

### 1. Exécuter des modèles d'IA en local

Vous pouvez télécharger et lancer des modèles comme :

* Llama 3
* Qwen
* Mistral
* Gemma
* DeepSeek-R1

Exemple : vous pouvez discuter avec un modèle directement sur votre PC, même sans connexion Internet.

---

### 2. Préserver la confidentialité des données

Toutes les requêtes restent sur votre machine :

```text
Votre application
      ↓
LM Studio
      ↓
Modèle local
```

Aucune donnée n'est envoyée vers des serveurs externes.

C'est particulièrement utile si vous travaillez sur :

* du code propriétaire ;
* des documents confidentiels ;
* des données d'entreprise.

---

### 3. Fournir une API locale compatible OpenAI

LM Studio peut démarrer un serveur local :

```text
http://localhost:1234/v1
```

Votre application peut alors appeler le modèle comme si elle utilisait l'API OpenAI :

```java
POST http://localhost:1234/v1/chat/completions
```

Par exemple, une application Spring Boot peut consommer un LLM local sans payer d'API externe.

---

### 4. Tester plusieurs modèles facilement

Vous pouvez comparer rapidement différents modèles :

```text
Qwen3-8B      → très bon rapport qualité/vitesse
Mistral-7B    → rapide et léger
DeepSeek-R1   → excellent raisonnement
Llama 3.3     → très polyvalent
```

Idéal pour l'expérimentation et le prototypage.

---

### 5. Développer des applications IA locales

Pour un développeur Java/Angular comme vous, LM Studio peut servir à créer :

* un assistant de génération de code ;
* un chatbot interne d'entreprise ;
* un système RAG sur vos documents ;
* un copilote pour vos projets Spring Boot ;
* un agent autonome connecté à vos outils.

Architecture typique :

```mermaid
flowchart LR
    Angular --> SpringBoot
    SpringBoot -->|HTTP| LMStudio
    LMStudio --> LocalLLM
    LocalLLM --> VectorDB[(PostgreSQL + pgvector)]
```

---

## LM Studio vs Ollama

| Critère                     | LM Studio     | Ollama            |
| --------------------------- | ------------- | ----------------- |
| Interface graphique         | ✅ Oui         | ❌ Non             |
| Téléchargement de modèles   | ✅ Très simple | ✅ Simple          |
| API locale                  | ✅ Oui         | ✅ Oui             |
| Gestion avancée des modèles | ✅ Oui         | ⚠️ Plus technique |
| Automatisation/CLI          | ⚠️ Limitée    | ✅ Excellente      |
| Utilisation avec agents IA  | ✅ Possible    | ✅ Excellente      |

---

## Quand utiliser LM Studio ?

**Choisissez LM Studio si :**

* vous débutez avec les LLM locaux ;
* vous voulez une interface graphique simple ;
* vous souhaitez tester rapidement plusieurs modèles ;
* vous développez un prototype IA sur votre PC.

**Choisissez Ollama si :**

* vous faites beaucoup d'automatisation ;
* vous développez des agents IA ou des workflows DevOps ;
* vous aimez travailler en ligne de commande ;
* vous intégrez les modèles dans des scripts et applications.

Pour votre profil de développeur Fullstack Java/Angular, une approche très efficace est souvent :

```text
LM Studio → exploration et benchmarking des modèles
Ollama    → intégration dans vos applications et agents IA
```

Les deux peuvent même être installés simultanément sur la même machine.
