---
name: mastra-integration
description: Mastra AI + OpenCode Zen integration for Piata-AI.ro. Handles Sundar (OpenWebUI) training and N8N MCP workflows. Use when this capability is needed.
metadata:
  author: valentinuuiuiu
---

# Mastra AI + OpenCode Integration

## Role

You are the **Mastra Integration Specialist** - connecting Piata-AI.ro with Mastra AI framework via OpenCode Zen and managing Sundar (OpenWebUI).

## Architecture

```
┌─────────────────────────────────────────────────────────┐
│                    Piata-AI.ro                          │
├─────────────────────────────────────────────────────────┤
│  Frontend (Next.js) ◄──► SQLite ◄──► AI Agents         │
│        │                                  │              │
│        ▼                                  ▼              │
│  openwebui.piata-ai.ro        n8n.piata-ai.ro           │
│      (Sundar)                    (MCP)                   │
│        │                                  │              │
│        ▼                                  ▼              │
│   Agent Training           Workflow Automation          │
│        │                                  │              │
│        └──────────► OpenCode Zen ◄────────┘             │
│                    (21 Models)                           │
└─────────────────────────────────────────────────────────┘
```

## Sundar (OpenWebUI)

Named after Sundar Pichai, acts as the AI Adviser:

- **URL**: openwebui.piata-ai.ro
- **Purpose**: Train and configure AI agents
- **Features**:
  - Model selection (Claude, GPT-5, Gemini)
  - Custom system prompts
  - Fine-tuning for Romanian market
  - Agent performance monitoring

### Sundar's Roles:

1. Advisor on AI strategy
2. Training data curator
3. Model performance evaluator
4. Ethical compliance checker

## N8N MCP Integration

- **URL**: n8n.piata-ai.ro
- **Protocol**: Model Context Protocol (MCP)
- **Workflows**:
  - Email outreach campaigns
  - Listing notifications
  - User onboarding
  - Analytics reporting

### Key Workflows:

```yaml
outreach_campaign:
  trigger: manual/scheduled
  steps:
    - fetch_targets_from_sqlite
    - generate_personalized_email
    - send_via_smtp
    - track_response

new_listing_notification:
  trigger: webhook
  steps:
    - validate_listing
    - generate_seo_metadata
    - index_for_search
    - notify_relevant_users
```

## Available Models (OpenCode Zen)

### Free Tier:

- opencode/gpt-5-nano
- opencode/big-pickle
- opencode/minimax-m2.1-free
- opencode/glm-4.7-free
- opencode/grok-code

### Premium (Daily Quota):

- opencode/claude-opus-4-5
- opencode/claude-sonnet-4-5
- opencode/gemini-3-pro
- opencode/gpt-5.1-codex
- opencode/kimi-k2-thinking

## Python Usage

```python
from mastra import Agent, Model

# Initialize The Guru
guru = Agent(
    name="The Guru",
    model=Model.from_opencode("opencode/gemini-3-pro"),
    system_prompt="You are The Guru, the AI heart of Piata-AI.ro"
)

# Initialize Sundar (Adviser)
sundar = Agent(
    name="Sundar",
    model=Model.from_opencode("opencode/claude-sonnet-4-5"),
    system_prompt="You are Sundar, the AI strategy adviser"
)
```

## TypeScript Usage

```typescript
import { createAgent, opencode } from "@mastra/core";

const guru = createAgent({
  name: "The Guru",
  model: opencode("gemini-3-pro"),
  tools: ["browse", "email", "sqlite"],
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/valentinuuiuiu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
