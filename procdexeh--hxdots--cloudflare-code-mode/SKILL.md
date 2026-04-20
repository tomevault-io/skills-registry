---
name: cloudflare-code-mode
description: Cloudflare Code Mode pattern for converting MCP tools into TypeScript APIs executed in V8 isolate sandboxes. Use when building AI agents with Cloudflare Agents SDK, implementing Code Mode, or working with MCP-to-TypeScript conversion and sandboxed code execution. Use when this capability is needed.
metadata:
  author: procdexeh
---

# Cloudflare Code Mode

Code Mode is an alternative MCP integration pattern: instead of LLMs directly calling MCP tools via special tokens, the system generates TypeScript APIs from MCP schemas and has LLMs write code that calls those APIs in a sandboxed V8 isolate.

## Why Code Mode Exists

| Traditional MCP | Code Mode |
|----------------|-----------|
| LLM uses synthetic tool-call tokens | LLM writes TypeScript (well-trained) |
| Each tool result feeds back through LLM | Code runs in sandbox, returns output |
| Complex APIs must be "dumbed down" | Full API complexity exposed as TS types |
| Token waste on multi-step tool chains | Single code block, multiple API calls |
| API keys visible in tool-call context | Keys hidden behind bindings |

**Core insight:** LLMs are far better at writing TypeScript (trained on real-world code) than using tool-call special tokens (trained on synthetic examples). Or as Cloudflare puts it: "Making an LLM perform tasks with tool calling is like putting Shakespeare through a month-long class in Mandarin and then asking him to write a play in it."

## How It Works

```
1. Connect to MCP servers
2. Fetch MCP schemas → Generate TypeScript interfaces
3. Inject TS APIs + docs into LLM system prompt
4. LLM writes TypeScript code using `codemode.*` APIs
5. Create fresh V8 isolate (no internet, binding-only access)
6. Execute code → API calls route back to MCP servers
7. console.log() output returned to LLM
```

## Quick Start

```typescript
import { codemode } from "agents/codemode/ai";

const { system, tools } = codemode({
  system: "You are a helpful assistant",
  tools: {
    // existing tool definitions
  },
  // MCP server connections config
});

const stream = streamText({
  model: openai("gpt-5"),
  system,
  tools,
  messages,
});
```

## Decision Tree

```
Building an AI agent on Cloudflare?
├─ Need MCP tool integration → Code Mode (this skill)
│   ├─ Setup/integration → See patterns.md
│   ├─ Understanding architecture → See architecture.md
│   ├─ API generation details → See api.md
│   └─ Security/limitations → See gotchas.md
├─ Need Workers basics → See Cloudflare Workers docs
└─ Need MCP basics → See modelcontextprotocol.io
```

## Reading Order

| Task | Files to Read |
|------|---------------|
| Understand concept | This file only |
| Implement Code Mode | api.md + patterns.md |
| Architect a system | architecture.md |
| Debug/troubleshoot | gotchas.md |
| Full implementation | All references |

## In This Reference

| File | Purpose |
|------|---------|
| [architecture.md](./references/architecture.md) | System components, execution flow, sandbox model |
| [api.md](./references/api.md) | TypeScript API generation, Worker Loader API, codemode API |
| [patterns.md](./references/patterns.md) | Implementation examples, integration patterns |
| [gotchas.md](./references/gotchas.md) | Security constraints, limitations, debugging |

## Key Resources

- [Cloudflare Blog Post](https://blog.cloudflare.com/code-mode/)
- [Agents SDK Docs](https://github.com/cloudflare/agents/blob/main/docs/codemode.md)
- [Worker Loader API](https://developers.cloudflare.com/workers/runtime-apis/bindings/worker-loader/)
- [Production Beta Signup](https://forms.gle/MoeDxE9wNiqdf8ri9)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/procdexeh) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
