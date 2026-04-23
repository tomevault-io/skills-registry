---
name: pydantic-ai
description: Expert guidance on building agents and tools with Pydantic AI. Use when this capability is needed.
metadata:
  author: fullfran
---

# Pydantic AI Expert Skill

This skill provides patterns for defining agents, dependencies, and tools using Pydantic AI.

## 🤖 Agent Definition

- **State Management**: Use `StateDeps[T]` to pass dependencies (DB clients, settings) to agents.
- **System Prompts**: Define complex prompts in `src/prompts.py`. Use `@agent.instructions` for dynamic context.
- **Model Choice**: Use `OpenAIModel` as a base for OpenAI-compatible providers (OpenRouter, Ollama) via `OpenAIProvider`.

## 🛠️ Tool Patterns

- **Return Strings**: Agents work best with text. Tools should return formatted strings, not Pydantic objects.
- **Resource Management**: Initialize and cleanup DB connections properly within the tool or via injected deps.
- **Error Handling**: Tools should catch exceptions and return helpful error messages to the agent rather than crashing.

## 📺 CLI & Streaming

- **Rich Integration**: Use `Rich` to display real-time streaming of agent output and tool call details.
- **Node Handling**: Implement logic for `user_prompt`, `model_request`, `call_tools`, and `end` nodes in the CLI.
- **Transparency**: Always show the user which tool is being called and with what parameters.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fullfran) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
