# RAG
Le RAG (Retrieval-Augmented Generation) est une technique très importante pour améliorer les LLM. <br>
Elle permet à un modèle de langage de chercher des informations dans une base de données ou des documents externes 
avant de répondre.

L’idée est simple :<br>
👉 au lieu que le modèle réponde uniquement avec ce qu’il a appris pendant son entraînement, 
on lui donne des documents pertinents en temps réel.

# <ins>Définition du RAG</ins>
Le Retrieval-Augmented Generation combine deux systèmes :
- 1.Recherche d’information (retrieval)
- 2.Génération de texte avec un LLM

Le concept a été introduit par Meta AI dans un article de recherche en 2020.

# <ins>Pourquoi le RAG est nécessaire</ins>
Les LLM comme GPT-4 ou LLaMA ont des limites :
- ❌ connaissances figées au moment de l'entraînement
- ❌ hallucinations (inventions)
- ❌ impossible de connaître des données privées
- ❌ difficile de mettre à jour les connaissances

Le RAG résout ces problèmes.

Avec RAG :
- ✅ accès à des données récentes
- ✅ accès à des documents internes
- ✅ réponses plus précises
- ✅ moins d’hallucinations

# <ins>Architecture complète d’un système RAG</ins>
Un système RAG classique contient 5 composants principaux.

## 1. Base de documents
On commence avec des sources d'information :
- PDF
- bases de connaissances
- pages web
- documentation technique
- bases de données internes

Exemple :
- FAQ entreprise
- documentation produit
- wiki interne

## 2. Chunking (découpage)
Les documents sont découpés en petits morceaux appelés chunks.

Exemple :<br>
Document : 20 pages<br>
→ découpé en morceaux de 200–500 tokens

Pourquoi ?
- les LLM ont une limite de contexte
- la recherche est plus précise

## 3. Embeddings
Chaque chunk est transformé en vecteur numérique grâce à un modèle d’embedding.

On utilise par exemple :
- Sentence-BERT
- text-embedding-ada-002

Ces vecteurs représentent le sens du texte.

Exemple :
```
"Le chat mange du poisson"
→ [0.124, -0.82, 0.44, ...]
```
Deux textes similaires auront des vecteurs proches.

## 4. Vector Database
Les vecteurs sont stockés dans une base vectorielle.

Exemples populaires :
- FAISS
- Pinecone
- Weaviate
- Milvus

Cette base permet de trouver les chunks les plus proches d’une question.

## 5. Retrieval (recherche)
Quand l’utilisateur pose une question :
- 1.la question est transformée en embedding
- 2.la base vectorielle cherche les chunks similaires
- 3.les meilleurs résultats sont récupérés

Exemple :<br>
Question :
```
"Comment installer le logiciel ?"
```

La base retourne :
- chunk 1 : installation Linux
- chunk 2 : installation Windows
- chunk 3 : configuration

## 6. Augmentation du prompt
Les chunks récupérés sont injectés dans le prompt du LLM.

Exemple :
```
Contexte :
[chunk1]
[chunk2]
[chunk3]

Question :
Comment installer le logiciel ?
```

## 7. Génération
Le LLM génère ensuite une réponse basée sur ces informations.

Par exemple avec :
- GPT-4
- Claude
- LLaMA

# <ins>Pipeline complet d’un RAG</ins>
Voici le pipeline complet :
```
Documents
     ↓
Chunking
     ↓
Embeddings
     ↓
Vector Database
     ↓
Question utilisateur
     ↓
Embedding question
     ↓
Similarity Search
     ↓
Chunks pertinents
     ↓
Prompt + LLM
     ↓
Réponse
```

# <ins>Types de RAG</ins>
Il existe plusieurs architectures.

## Basic RAG
Pipeline simple :
```
Question → Recherche → LLM
```

## Advanced RAG
Améliorations :
- reranking
- multi-retrieval
- query rewriting
- filtering

## Agentic RAG
Un agent IA décide :
- quoi chercher
- où chercher
- combien de fois chercher

# <ins>Optimisations importantes</ins>

## 1. Chunking intelligent
Mauvais chunking = mauvais résultats.

Techniques :
- semantic chunking
- sliding window
- hierarchical chunking

## 2. Reranking
Après la recherche, un modèle classe les résultats.

Exemple :
- Cohere Rerank

## 3. Hybrid Search
Combine :
- recherche vectorielle
- recherche par mots-clés

exemple : BM25 + embeddings

## 4. Query rewriting
Le système reformule la question.

Exemple :
```
User:
"installation linux"

→

"comment installer le logiciel sur linux"
```

# <ins>Stack technique RAG</ins>
Stack typique :

Frameworks
- LangChain
- LlamaIndex

Vector DB
- FAISS
- Pinecone
- Milvus

LLM
- GPT-4
- Claude
- LLaMA

# <ins>Cas d’usage du RAG</ins>
RAG est utilisé pour :

**Support client**<br>
Chatbot qui lit la documentation.

**Recherche documentaire**<br>
Interroger :
- PDFs
- rapports
- contrats

**Chat avec ses documents**<br>
Exemple :
- "ChatGPT pour mes fichiers"

**Entreprises**<br>
Accès au :
- wiki interne
- base de connaissances
- documentation technique

# <ins>Limites du RAG</ins>
Même RAG a des limites :
- ❌ mauvaise récupération de documents
- ❌ contexte trop long
- ❌ latence plus élevée
- ❌ dépendance à la qualité des données

# <ins>Futur du RAG</ins>
Les évolutions actuelles :
- Graph RAG
- Agentic RAG
- Self-RAG
- Corrective RAG

Ces architectures rendent les systèmes beaucoup plus fiables.

✅ **En résumé**<br>
RAG =
```
Recherche d'information
+
LLM
=
IA beaucoup plus fiable
```
C’est aujourd’hui l’architecture la plus utilisée pour créer des applications LLM professionnelles.
