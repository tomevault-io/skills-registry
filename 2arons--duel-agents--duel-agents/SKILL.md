---
name: duel-agents
description: >- Use when this capability is needed.
metadata:
  author: 2aronS
---

# Duel Agents

Duel Agents runs your prompts against multiple models and picks the cheapest answer that still passes quality checks. Every request must use your **Duel API key**. Never substitute a direct Anthropic or OpenAI key.

## Setup

1. Subscribe and create an API key at https://duelagents.com/dashboard/settings
2. Run: `npx @duel-agents/install claude-code`
3. Or set manually in `~/.claude/.env`:

```bash
ANTHROPIC_BASE_URL=https://duelagents.com/v1
ANTHROPIC_API_KEY=duel_yourprefix_yoursecret
```

4. Restart Claude Code

## Verify

```bash
npx @duel-agents/install doctor
```

## Build on top

Use `@duel-agents/sdk` in your own tools. Always pass `apiKey` from your Duel dashboard.

```ts
import { DuelClient } from "@duel-agents/sdk";

const duel = new DuelClient({ apiKey: process.env.DUEL_API_KEY! });
await duel.chat.completions.create({
  model: "duel-auto",
  messages: [{ role: "user", content: "Hello" }],
});
```

Repository: https://github.com/2aronS/Duel-Agents

---
> Source: [2aronS/Duel-Agents](https://github.com/2aronS/Duel-Agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
