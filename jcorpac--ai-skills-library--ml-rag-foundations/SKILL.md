---
name: ml-rag-foundations
description: Comprehensive guide to building Retrieval-Augmented Generation (RAG) applications by combining specific library skills. Use when this capability is needed.
metadata:
  author: jcorpac
---

# RAG Foundations

Retrieval-Augmented Generation (RAG) is the architecture of choice for building AI applications that need to answer questions based on specific, private, or up-to-date data.

This skill is a **meta-guide** that shows you how to combine other specialized skills in this library to build a production-grade RAG pipeline.

## The Architecture

A standard RAG pipeline has five distinct stages:

1.  **Ingestion ("ETL")**: Loading and cleaning your data.
2.  **Embedding**: Converting text into vector representations.
3.  **Storage**: Saving vectors in a database for fast similarity search.
4.  **Retrieval**: Finding the most relevant context for a user query.
5.  **Generation**: Using an LLM to synthesize an answer from the retrieved context.

## Skill Stack

To build this, you will combine the following skills:

| Stage | Skill | Role |
| :--- | :--- | :--- |
| **Generation** | **[`gemini-api-dev`](../api/gemini-api-dev/SKILL.md)** | The "Brain". Handles generation, context caching, and final answer synthesis. |
| **Embeddings** | **[`ml-nlp-transformers`](./ml-nlp-transformers/SKILL.md)** | The "Translator". Converts your text into vectors using `sentence-transformers`. |
| **Storage** | **[`ops-docker-compose-orchestration`](../ops/ops-docker-compose-orchestration/SKILL.md)** | The "Memory". Deploys a Vector DB (e.g., Qdrant, Chroma, Pgvector) via Docker. |
| **Ingestion** | **[`ds-data-wrangling`](../ds/ds-data-wrangling/SKILL.md)** | The "Cleaner". Cleans, normalizes, and chunks your text data. |
| **API** | **[`api-fastapi-modern`](../api/api-fastapi-modern/SKILL.md)** | The "Interface". Serves your RAG pipeline as a REST API. |

## Implementation Steps

### Phase 1: The Data Pipeline (Ingestion & Indexing)

Before you can answer questions, you must index your data.

1.  **Chunking Strategy**:
    *   Use **`ds-data-wrangling`** patterns to split your documents.
    *   *Tip*: A 500-token chunk with 50-token overlap is a good starting point.
2.  **Vectorization**:
    *   Use **`ml-nlp-transformers`** to load an embedding model (e.g., `all-MiniLM-L6-v2`).
    *   Generate vectors for each chunk.
3.  **Storage**:
    *   Use **`ops-docker-compose-orchestration`** to spin up a vector store.
    *   Upsert your vectors + metadata (original text, source URL) into the DB.

### Phase 2: The Inference Pipeline (Retrieval & Generation)

When a user asks a question:

1.  **Embed Query**: Use the *same* model from Phase 1 to embed the user's question.
2.  **Retrieve**: Query your Vector DB for the top $k$ (usually 3-5) most similar chunks.
3.  **Augment Context**:
    *   Format the retrieved chunks into a prompt.
    *   *Tip*: Use **`meta-context-budgeting`** to ensure you don't exceed the token limit.
4.  **Generate**:
    *   Use **`gemini-api-dev`** to send the prompt + context to the model.
    *   Stream the response back to the user.

## Example: Minimal RAG Script (Python)

```python
import google.genai as genai
from sentence_transformers import SentenceTransformer
import chromadb

# 1. Setup
client = genai.Client(api_key="GEMINI_API_KEY")
embedder = SentenceTransformer('all-MiniLM-L6-v2')
db = chromadb.PersistentClient(path="./rag_db")
collection = db.get_or_create_collection("docs")

# 2. Ingest (Simplified)
docs = ["RAG is great.", "Gemini has a 1M token context."]
ids = ["1", "2"]
embeddings = embedder.encode(docs).tolist()
collection.add(documents=docs, embeddings=embeddings, ids=ids)

# 3. Retrieve
query = "What is Gemini context size?"
query_vec = embedder.encode([query]).tolist()
results = collection.query(query_embeddings=query_vec, n_results=1)
context = results['documents'][0][0]

# 4. Generate
prompt = f"Context: {context}\n\nQuestion: {query}"
response = client.models.generate_content(
    model="gemini-2.0-flash", 
    contents=prompt
)
print(response.text)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcorpac) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
