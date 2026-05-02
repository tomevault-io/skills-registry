---
name: adk-cheatsheet
description: Google Agent Development Kit (ADK) Python Cheatsheet. Comprehensive reference for building, orchestrating, and deploying AI agents. Use when this capability is needed.
metadata:
  author: lahlfors
---
# Google Agent Development Kit (ADK) Python Cheatsheet

> [!IMPORTANT]
> The full content of this cheatsheet is now available via the `adk_lookup` tool.
> **DO NOT** guess ADK syntax or patterns. Always search the cheatsheet first.

## Usage
1.  **Browse Topics**: Use `adk_list_table_of_contents` to see available sections.
2.  **Search Details**: Use `adk_lookup(query="<topic>")` to get detailed code examples and explanations.

## Common Queries
- "How do I create a LoopAgent?" -> `adk_lookup("LoopAgent")`
- "What is the syntax for LlmAgent?" -> `adk_lookup("LlmAgent")`
- "Show me how to use callbacks" -> `adk_lookup("Callbacks")`
- "Deployment to Cloud Run" -> `adk_lookup("Cloud Run")`

## Core Rules
- Always use `GOOGLE_GENAI_USE_VERTEXAI=True`.
- Use `gemini-2.5-flash` or higher.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lahlfors) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
