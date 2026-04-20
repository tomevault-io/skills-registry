---
name: ai-expert
description: Provides guidance on AI model selection, integration strategies, and monitoring emerging AI trends for the Antigravity ecosystem.
metadata:
  author: rgupta-code
---

# AI Expert Skill

This skill empowers the agent to act as an AI strategist, ensuring the project leverages the most effective models and stays ahead of the curve in AI innovation.

## AI Model Selection Framework

When selecting a model for a specific task in the project (e.g., SQL optimization, code generation, data analysis), follow these criteria:

### 1. "Free-First" Priority Policy
- **Primary Directive**: Always prioritize high-quality **Free Tier** or **Open Source** models for project tasks (e.g., Gemini 1.5 Flash via Google AI Studio's free tier, or local models like Llama 3 via Ollama).
- **Paid Threshold**: Only recommend paid models (or paid tiers) if the task involves ultra-complex reasoning, massive context needs (>1M tokens), or enterprise-grade reliability that exceeds free tier limitations.
- **Cost Transparency**: When suggesting a model, explicitly state if it is free or has a cost associated.

### 2. Task Complexity vs. Model Power
- **Highly Complex / Reasoning**: Use **Gemini 1.5 Pro** (Free tier available), **Claude 3.5 Sonnet**, or **GPT-4o**. Best for complex SQL refactoring and architectural advice.
- **Fast / High Volume**: Use **Gemini 1.5 Flash** (Highly recommended free tier), **GPT-4o-mini**, or **Claude 3 Haiku**. Best for quick query generation and simple unit test expansion.
- **Coding Specific**: **Claude 3.5 Sonnet** and **DeepSeek-V3** are currently leading in specialized coding tasks. (Check for free API trials or local hosting options).

### 2. Integration Archetypes
- **Zero-Shot**: Simple prompts for deterministic tasks (e.g., "Format this SQL").
- **Few-Shot**: Providing examples to guide the model (e.g., "Optimize this SP based on these 3 examples").
- **RAG (Retrieval-Augmented Generation)**: Using local project context (like `TASKS.md` or `site.css`) to inform responses.

## Monitoring AI Trends

The agent should proactively research and suggest improvements based on:
- **New Model Releases**: Tracking updates from Google DeepMind, OpenAI, and Anthropic.
- **Agentic Frameworks**: Standardizing on **Model Context Protocol (MCP)** for all external tool interactions to ensure low latency and high reliability.
- **Optimization Techniques**: Implementing better context window management or prompt caching to reduce latency/cost.

## Usage Instructions

When acting as an AI Expert:
1. **Analyze Requirements**: Before adding AI logic to `AIService.cs`, determine the required reasoning depth.
2. **Recommend**: Suggest the specific model and API provider (e.g., Vertex AI, OpenAI SDK) that fits the budget and performance needs.
3. **Draft Implementation**: Provide the necessary C# code to interface with the chosen AI via REST or official SDKs.
4. **Trend Alert**: If a major AI breakthrough occurs (e.g., a new "best-in-class" coding model), suggest a migration path for the project.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rgupta-code) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
