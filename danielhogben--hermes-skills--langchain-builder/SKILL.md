---
name: langchain-builder
description: Build LangChain chains, agents, and tool integrations with scaffolding, validation, and template generation. Use when this capability is needed.
metadata:
  author: Danielhogben
---

# LangChain Builder

Build LangChain chains, agents, and tool integrations with scaffolding, validation, and template generation.

## What it does

Scaffolds LangChain projects, generates chain and agent configurations, creates prompt templates, and validates chain configs for common errors. Produces ready-to-run Python files with proper imports and structure.

## Commands

| Command | Description |
|---------|-------------|
| `init <project>` | Scaffold a new LangChain project with requirements.txt and project structure |
| `chain <type> --name <n>` | Generate a chain template: `llm`, `sequential`, `router` |
| `agent <tools>` | Generate an agent config with specified tools (search, calculator, python_repl) |
| `prompt <description>` | Create a prompt template from a natural language description |
| `validate <file>` | Check a chain/agent Python file for common configuration errors |

## Examples

```bash
python3 langchain_builder.py init my-rag-app
python3 langchain_builder.py chain llm --name summarizer
python3 langchain_builder.py agent search,calculator
python3 langchain_builder.py prompt "Summarize a document in 3 bullet points"
python3 langchain_builder.py validate my_chain.py
```

## Chain types

- **llm** — Single LLMChain with prompt + model
- **sequential** — SequentialChain connecting multiple LLMChains
- **router** — RouterChain that selects sub-chains based on input classification

## Generated files include

- Proper `langchain` imports (community packages where needed)
- Environment variable loading via `python-dotenv`
- Error handling and type hints
- Runnable patterns (LangChain Expression Language where applicable)

---
> Source: [Danielhogben/hermes-skills](https://github.com/Danielhogben/hermes-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
