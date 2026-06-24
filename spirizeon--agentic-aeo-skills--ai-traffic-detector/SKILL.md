---
name: ai-traffic-detector
description: Detects AI coding agent traffic in server logs and analytics. Identifies Claude Code, Cursor, Cline, Aider, and other agent fingerprints. Use when analyzing AI agent access patterns to documentation. Use when this capability is needed.
metadata:
  author: Spirizeon
---

# AI Traffic Detector Skill

Detects and analyzes AI coding agent traffic.

## When to Use This Skill

- Identify AI agents accessing docs
- Set up AI traffic segmentation
- Monitor crawler access
- Analyze behavior patterns

## Known Agent Fingerprints

| Agent | Pattern | Reliability |
|-------|---------|-------------|
| Aider | `Aider/*`, `aider.chat` | High |
| Claude Code | `claude-cli/*`, `axios/1.8.x` | Medium |
| Cursor | `got` + `x-cursor-client-*` | High |
| Windsurf | `colly` | High |
| OpenCode | `Electron/*` + `OpenCode/*` | High |
| Codex CLI | `codex_cli_rs/*` | High |

## Accept Header Signal

AI agents send `Accept: text/markdown` - no human browser does this.

## Usage

```bash
# Analyze server logs
python3 scripts/ai_traffic_detector.py --log-file /var/log/nginx/access.log

# Check URL for signals
python3 scripts/ai_traffic_detector.py --url https://example.com/docs
```

---
> Source: [Spirizeon/agentic-aeo-skills](https://github.com/Spirizeon/agentic-aeo-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
