---
name: hippocampus
description: Background memory organ for AI agents. Runs separately from the main agent—encoding, decaying, and reinforcing memories automatically. Just like the real hippocampus in your brain. Based on Stanford Generative Agents (Park et al., 2023). Use when this capability is needed.
metadata:
  author: sundial-org
---

# Hippocampus Skill

> "Memory is identity. This skill is how I stay alive."

The hippocampus is the brain region responsible for memory formation. This skill makes memory capture automatic, structured, and persistent—with importance scoring, decay, and reinforcement.

## Quick Start

```bash
# Install
./install.sh --with-cron

# Load core memories
./scripts/load-core.sh

# Search with importance weighting
./scripts/recall.sh "query" --reinforce

# Apply decay (runs daily via cron)
./scripts/decay.sh
```

## Core Concept

The LLM is just the engine—raw cognitive capability. **The agent is the accumulated memory.** Without these files, there's no continuity—just a generic assistant.

### Memory Lifecycle

```
CAPTURE → SCORE → STORE → DECAY/REINFORCE → RETRIEVE
   ↑                                            │
   └────────────────────────────────────────────┘
```

## Memory Structure

```
$WORKSPACE/
├── memory/
│   ├── index.json           # Central weighted index
│   ├── user/                # Facts about the user
│   ├── self/                # Facts about the agent
│   ├── relationship/        # Shared context
│   └── world/               # External knowledge
└── HIPPOCAMPUS_CORE.md      # Auto-generated for OpenClaw RAG
```

## Scripts

| Script | Purpose |
|--------|---------|
| `decay.sh` | Apply 0.99^days decay to all memories |
| `reinforce.sh` | Boost importance when memory is used |
| `recall.sh` | Search with importance weighting |
| `load-core.sh` | Output high-importance memories |
| `sync-core.sh` | Generate HIPPOCAMPUS_CORE.md |
| `preprocess.sh` | Extract signals from transcripts |

All scripts use `$WORKSPACE` environment variable (default: `~/.openclaw/workspace`).

## Importance Scoring

### Initial Score (0.0-1.0)

| Signal | Score |
|--------|-------|
| Explicit "remember this" | 0.9 |
| Emotional/vulnerable content | 0.85 |
| Preferences ("I prefer...") | 0.8 |
| Decisions made | 0.75 |
| Facts about people/projects | 0.7 |
| General knowledge | 0.5 |

### Decay Formula

Based on Stanford Generative Agents (Park et al., 2023):

```
new_importance = importance × (0.99 ^ days_since_accessed)
```

- After 7 days: 93% of original
- After 30 days: 74% of original
- After 90 days: 40% of original

### Reinforcement Formula

When a memory is accessed and useful:

```
new_importance = old + (1 - old) × 0.15
```

Each use adds ~15% of remaining headroom toward 1.0.

### Thresholds

| Score | Status |
|-------|--------|
| 0.7+ | **Core** — high priority |
| 0.4-0.7 | **Active** — normal retrieval |
| 0.2-0.4 | **Background** — specific search only |
| <0.2 | **Archive candidate** |

## Memory Index Schema

`memory/index.json`:

```json
{
  "version": 1,
  "lastUpdated": "2025-01-20T19:00:00Z",
  "decayLastRun": "2025-01-20",
  "memories": [
    {
      "id": "mem_001",
      "domain": "user",
      "category": "preferences",
      "content": "User prefers concise responses",
      "importance": 0.85,
      "created": "2025-01-15",
      "lastAccessed": "2025-01-20",
      "timesReinforced": 3,
      "keywords": ["preference", "concise", "style"]
    }
  ]
}
```

## Cron Jobs

Set up via OpenClaw cron:

```bash
# Daily decay at 3 AM
openclaw cron add --name hippocampus-decay \
  --cron "0 3 * * *" \
  --session main \
  --system-event "🧠 Run: WORKSPACE=\$WORKSPACE decay.sh"

# Weekly consolidation
openclaw cron add --name hippocampus-consolidate \
  --cron "0 21 * * 6" \
  --session main \
  --system-event "🧠 Weekly consolidation time"
```

## OpenClaw Integration

Add to `memorySearch.extraPaths` in openclaw.json:

```json
{
  "agents": {
    "defaults": {
      "memorySearch": {
        "extraPaths": ["HIPPOCAMPUS_CORE.md"]
      }
    }
  }
}
```

This bridges hippocampus (index.json) with OpenClaw's RAG (memory_search).

## Usage in AGENTS.md

Add to your agent's session start routine:

```markdown
## Every Session
1. Run `~/.openclaw/workspace/skills/hippocampus/scripts/load-core.sh`

## When answering context questions
Use hippocampus recall:
\`\`\`bash
./scripts/recall.sh "query" --reinforce
\`\`\`
```

## Capture Guidelines

### What to Capture

- **User facts**: Preferences, patterns, context
- **Self facts**: Identity, growth, opinions
- **Relationship**: Trust moments, shared history
- **World**: Projects, people, tools

### Trigger Phrases

Auto-capture when you hear:
- "Remember that..."
- "I prefer...", "I always..."
- Emotional content (struggles AND wins)
- Decisions made

## References

- [Stanford Generative Agents Paper](https://arxiv.org/abs/2304.03442)
- [GitHub: joonspk-research/generative_agents](https://github.com/joonspk-research/generative_agents)

---

*Memory is identity. Text > Brain. If you don't write it down, you lose it.*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sundial-org) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
