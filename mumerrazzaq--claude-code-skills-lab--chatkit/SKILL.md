---
name: chatkit
description: Build AI chat applications with OpenAI ChatKit SDK. Use when creating chat UIs, multi-session systems, integrating AI agents (OpenAI, Gemini, Claude, Ollama), adding tools with MCP, widgets, or building conversational apps. Covers server setup, Store implementations, frontend integration (@openai/chatkit-react), function tools, MCP servers, multi-user sessions, and streaming responses. Use when this capability is needed.
metadata:
  author: mumerrazzaq
---

# ChatKit Development Guide

Build production AI chat applications with OpenAI ChatKit.

## Before You Start

**Clarify these before proceeding:**

| Question | Why It Matters | Default |
|----------|----------------|---------|
| Single-user or multi-user? | Determines Store implementation | Single-user |
| Which LLM provider? | Affects dependencies and model config | OpenAI |
| Need widgets/forms? | Adds complexity to respond() | No |
| Persistent storage? | InMemory vs PostgreSQL | InMemory (dev) |

**Skip clarification if:** User says "just get it working" - use defaults.

## Version Compatibility

| Package | Tested Version | Check Latest |
|---------|---------------|--------------|
| `openai-chatkit` | 0.1.x | `pip index versions openai-chatkit` |
| `openai-agents` | 0.0.x | `pip index versions openai-agents` |
| `@openai/chatkit-react` | 0.1.x | `npm view @openai/chatkit-react version` |
| `next` | 16.x | `npm view next version` |

**Breaking change risk:** ChatKit is early-stage. Pin versions in production.

**Next.js 16 verified patterns:**
- Route Handlers use standard Web API `Response` with `response.body` for streaming
- Use `request.text()` to read request body
- Use `Cache-Control: no-store` for SSE responses

## Architecture

```
Frontend (@openai/chatkit-react)  <--->  FastAPI + ChatKitServer  <--->  Agent (LLM)
              |                                    |
         domainKey                              Store (DB)
```

## Fastest Path (60 seconds)

```bash
# Create project with script
python scripts/init_chatkit_project.py my-app --provider openai

# Add API key
echo "OPENAI_API_KEY=sk-xxx" > my-app/.env

# Run server
cd my-app && uv run uvicorn main:app --reload --port 8000
```

**Verify it works:**
```bash
curl -X POST http://localhost:8000/chatkit \
  -H "Content-Type: application/json" \
  -d '{"type":"new_thread"}'
# Should return: {"thread_id": "thread_xxx", ...}
```

## Required Dependencies

```bash
# Backend (Python)
uv add openai-chatkit openai-agents fastapi "uvicorn[standard]"

# For non-OpenAI models (Gemini, Claude, etc.)
uv add "openai-agents[litellm]"

# For PostgreSQL persistence
uv add asyncpg
```

```bash
# Frontend (JavaScript/React)
npm install @openai/chatkit-react
# or vanilla JS
npm install @openai/chatkit
```

## Minimal Working Server

```python
from datetime import datetime
from typing import AsyncIterator
from collections import defaultdict

from fastapi import FastAPI, Request
from fastapi.responses import Response, StreamingResponse
from fastapi.middleware.cors import CORSMiddleware

from agents import Agent, Runner
from chatkit.server import ChatKitServer, StreamingResult
from chatkit.store import Store, NotFoundError
from chatkit.types import (
    ThreadMetadata, ThreadItem, Page, UserMessageItem,
    ThreadStreamEvent, ThreadItemDoneEvent, AssistantMessageItem, AssistantMessageContent
)
from chatkit.agents import AgentContext, stream_agent_response, simple_to_agent_input

app = FastAPI()

# CORS required for frontend
app.add_middleware(
    CORSMiddleware,
    allow_origins=["*"],  # Restrict in production
    allow_methods=["*"],
    allow_headers=["*"],
)

class InMemoryStore(Store[dict]):
    """Development store - data lost on restart."""
    def __init__(self):
        self.threads, self.items = {}, defaultdict(list)

    async def load_thread(self, thread_id, context):
        if thread_id not in self.threads: raise NotFoundError(f"Thread {thread_id} not found")
        return self.threads[thread_id]
    async def save_thread(self, thread, context): self.threads[thread.id] = thread
    async def delete_thread(self, thread_id, context): self.threads.pop(thread_id, None); self.items.pop(thread_id, None)
    async def load_threads(self, limit, after, order, context):
        return Page(data=list(self.threads.values())[:limit], has_more=False, after=None)
    async def load_thread_items(self, thread_id, after, limit, order, context):
        return Page(data=self.items.get(thread_id, [])[:limit], has_more=False, after=None)
    async def add_thread_item(self, thread_id, item, context): self.items[thread_id].append(item)
    async def save_item(self, thread_id, item, context): pass
    async def load_item(self, thread_id, item_id, context): raise NotFoundError("")
    async def delete_thread_item(self, thread_id, item_id, context): pass
    async def save_attachment(self, attachment, context): raise NotImplementedError()
    async def load_attachment(self, attachment_id, context): raise NotImplementedError()
    async def delete_attachment(self, attachment_id, context): raise NotImplementedError()

class MyChatKitServer(ChatKitServer[dict]):
    agent = Agent(model="gpt-4.1", name="Assistant", instructions="You are helpful.")

    async def respond(self, thread: ThreadMetadata, input: UserMessageItem | None, context: dict) -> AsyncIterator[ThreadStreamEvent]:
        # Load recent conversation history (REQUIRED for multi-turn)
        items_page = await self.store.load_thread_items(thread.id, after=None, limit=20, order="asc", context=context)
        input_items = await simple_to_agent_input(items_page.data)

        agent_context = AgentContext(thread=thread, store=self.store, request_context=context)
        result = Runner.run_streamed(self.agent, input_items, context=agent_context)
        async for event in stream_agent_response(agent_context, result):
            yield event

store = InMemoryStore()
server = MyChatKitServer(store=store)

@app.post("/chatkit")
async def chatkit_endpoint(request: Request):
    result = await server.process(await request.body(), context={})
    if isinstance(result, StreamingResult):
        return StreamingResponse(result, media_type="text/event-stream")
    return Response(content=result.json, media_type="application/json")
```

Run: `uv run uvicorn main:app --reload`

## Minimal Frontend (React)

```tsx
import { ChatKit, useChatKit } from '@openai/chatkit-react';

export function Chat() {
  const { control } = useChatKit({
    api: {
      url: 'http://localhost:8000/chatkit',
      domainKey: 'local-dev',  // Required - use 'local-dev' for development
    },
  });

  return <ChatKit control={control} className="h-[600px] w-[400px]" />;
}
```

See [frontend.md](references/frontend.md) for complete frontend integration.

## What Can Go Wrong

| Symptom | Cause | Fix |
|---------|-------|-----|
| CORS error in browser | Missing middleware | Add `CORSMiddleware` with frontend origin |
| "domainKey is required" | Missing config | Set `domainKey: 'local-dev'` in useChatKit |
| No streaming (all at once) | Wrong Runner method | Use `Runner.run_streamed()` not `Runner.run()` |
| Chat history lost | Wrong order parameter | Use `order="asc"` in `load_thread_items()` |
| Agent ignores history | Not loading items | Call `simple_to_agent_input(items_page.data)` |
| 401 Unauthorized | Auth header missing | Pass `headers: { Authorization: ... }` in api config |

**Full troubleshooting:** [troubleshooting.md](references/troubleshooting.md)

## Choose Your Path

### Store Selection

| Need | Solution | Command |
|------|----------|---------|
| Prototyping | InMemoryStore (above) | - |
| Production single-user | PostgreSQL | `python scripts/create_postgres_store.py store.py` |
| Production multi-user | PostgreSQL + sessions | `python scripts/create_postgres_store.py store.py --multi-user` |

See [store.md](references/store.md) for implementation details.

### LLM Provider

| Provider | Config |
|----------|--------|
| OpenAI | `Agent(model="gpt-4.1")` |
| Gemini | See [models.md](references/models.md#gemini) |
| OpenRouter | See [models.md](references/models.md#openrouter) |
| Ollama | See [models.md](references/models.md#ollama) |

## Adding Tools

```python
from agents import function_tool

@function_tool
def search_products(query: str) -> str:
    """Search the product catalog."""
    return f"Found results for: {query}"

agent = Agent(tools=[search_products], ...)
```

See [mcp.md](references/mcp.md) for MCP servers and hosted tools.

## Multi-User Sessions

```python
from dataclasses import dataclass

@dataclass
class RequestContext:
    user_id: str
    thread_id: str | None = None

# Filter by user_id in all store queries
async def load_threads(self, limit, after, order, ctx: RequestContext):
    # WHERE user_id = ctx.user_id
    ...
```

See [sessions.md](references/sessions.md) for complete implementation.

## Scripts

| Script | Purpose |
|--------|---------|
| `python scripts/init_chatkit_project.py <name> --provider <x>` | Create new project |
| `python scripts/create_postgres_store.py <file> [--multi-user]` | Generate PostgreSQL store |

Providers: `openai`, `gemini`, `openrouter`, `ollama`

## How Do I Know I'm Done?

### Minimum Viable ChatKit

- [ ] Server starts without errors (`uv run uvicorn main:app`)
- [ ] `curl -X POST .../chatkit -d '{"type":"new_thread"}'` returns thread_id
- [ ] Frontend renders ChatKit component (check: element visible, no console errors)
- [ ] Sending a message returns a streaming response
- [ ] Multi-turn conversation works (agent remembers previous messages)

### Production Ready

- [ ] PostgreSQL store connected and tables created
- [ ] CORS restricted to production domain
- [ ] API key in environment variable (not hardcoded)
- [ ] User authentication extracts user_id into RequestContext
- [ ] Store queries filter by user_id
- [ ] Error handling returns graceful messages
- [ ] domainKey registered with OpenAI (not 'local-dev')

## References

| File | Content |
|------|---------|
| [store.md](references/store.md) | Store interface, PostgreSQL, SQLite implementations |
| [models.md](references/models.md) | LLM provider configurations |
| [mcp.md](references/mcp.md) | Function tools, MCP servers, hosted tools |
| [sessions.md](references/sessions.md) | Multi-user session management, API endpoints |
| [frontend.md](references/frontend.md) | React/JS integration, customization, widgets |
| [troubleshooting.md](references/troubleshooting.md) | Common errors and solutions |

## External Documentation

- ChatKit Python: https://openai.github.io/chatkit-python/
- ChatKit JS: https://openai.github.io/chatkit-js/
- OpenAI Agents SDK: https://openai.github.io/openai-agents-python/

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/mumerrazzaq) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
