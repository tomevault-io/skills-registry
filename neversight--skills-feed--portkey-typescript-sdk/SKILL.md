---
name: portkey-typescript-sdk
description: Integrate Portkey AI Gateway into TypeScript/JavaScript applications. Use when building LLM apps with observability, caching, fallbacks, load balancing, or routing across 200+ LLM providers. Use when this capability is needed.
metadata:
  author: neversight
---

# Portkey TypeScript SDK

<!-- 
  PLACEHOLDER: Replace this content with your actual SKILL.md content.
  
  This file should contain comprehensive instructions for AI agents on how to:
  - Install and configure the Portkey TypeScript SDK
  - Use core features (completions, chat, embeddings)
  - Implement observability and tracing
  - Set up caching, fallbacks, and load balancing
  - Handle errors properly
  - Work with different LLM providers through Portkey
  
  Keep the file under 500 lines. Move detailed reference material to the 
  references/ directory.
-->

## When to use this skill

Use this skill when:
- The user wants to integrate Portkey into a TypeScript or JavaScript application
- The user needs LLM observability, caching, or reliability features
- The user wants to route requests across multiple LLM providers
- The user mentions "Portkey", "AI gateway", or "LLM observability" in Node.js/TS context

## Installation

```bash
npm install portkey-ai
# or
yarn add portkey-ai
# or
pnpm add portkey-ai
```

## Quick Start

```typescript
import Portkey from 'portkey-ai';

const client = new Portkey({
  apiKey: 'YOUR_PORTKEY_API_KEY',
  virtualKey: 'YOUR_VIRTUAL_KEY', // Optional: for provider routing
});

const response = await client.chat.completions.create({
  model: 'gpt-4',
  messages: [
    { role: 'user', content: 'Hello!' }
  ],
});

console.log(response.choices[0].message.content);
```

<!-- Add your complete SDK documentation below -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
