---
name: ima-agent-skill
description: Control the IMA (ima.copilot) desktop application for AI search and private knowledge retrieval. Use when this capability is needed.
metadata:
  author: openclaw
---

# IMA Skill

Control the **IMA (ima.copilot)** desktop application for AI search and private knowledge retrieval.

## Tools

### ima_search
Launches IMA and performs a search. Supports "Private Knowledge Base" mode via special tags.

- **query** (required): The search query. Prefix with `@个人知识库` or `@knowledge` to search your private knowledge base (requires `config.json`).
- **autoclose** (optional): "true" to close the app after searching. Default: "false".

**Implementation:**
```bash
/usr/bin/python3 /opt/homebrew/lib/node_modules/clawdbot/skills/ima/scripts/ima.py "{query}" --autoclose="{autoclose}"
```

## Configuration

To enable private knowledge base search, you must providing your `knowledge_id`.
The script looks for config in:
1. `~/.clawd_ima_config.json`
2. `skills/ima/config.json`

**Format:**
```json
{
  "knowledge_id": "your_id_string"
}
```

## Examples

- **Public:** `clawdbot ima_search query="DeepSeek analysis"`
- **Private:** `clawdbot ima_search query="@knowledge project update"`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
