---
name: rag-retrieval
description: Query the local RAG knowledge base using a python script. Use when this capability is needed.
metadata:
  author: regyo997
---

# RAG Retrieval Skill

This skill allows you to query the local Retrieval Augmented Generation (RAG) knowledge base.
It uses a Python script to interact with the `rag_core` module.

## Usage

To use this skill, run the following command from the project root or the skill directory.
The script handles adding the project root to the python path.

```bash
python skills/rag_retrieval/query_rag.py "Your question here"
```

## Example

```bash
python skills/rag_retrieval/query_rag.py "im facing a git conflict help"
```

## Implementation Details

The script `query_rag.py` imports `get_retriever` from `rag_core` and invokes it with the provided question.
It prints the retrieved documents to standard output.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/regyo997) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
