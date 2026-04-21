---
name: agent-zero-deep-research
description: Multi-source deep research using Agent Zero's autonomous browsing Use when this capability is needed.
metadata:
  author: jason-easyazz
---
# Agent Zero Deep Research

## When to Use
User wants thorough, multi-source research on a topic. This delegates to Agent Zero which can autonomously browse the web, read documents, and synthesize findings.

## API Endpoints

### Submit Research
POST http://agent-zero-bridge:8101/tools/research
```json
{"query": "topic to research", "depth": "thorough"}
```

### Check Status
GET http://agent-zero-bridge:8101/tools/status

## Behavior
1. Acknowledge the request: "I'll research that for you..."
2. Submit to Agent Zero
3. Wait for results (can take 30-120 seconds)
4. Present findings in structured format with sources

## Important
- Always tell user research is in progress
- Cite sources when available
- For time-sensitive topics, note the date of information

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jason-easyazz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
