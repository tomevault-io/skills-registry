---
name: research-pro
description: Deep research via Gemini CLI (MiniPC) or Local — Spawns sub-agent for efficiency. Use when this capability is needed.
metadata:
  author: kjaylee
---

# Research Pro (Miss Kim Edition)

Conduct deep research on any topic using Gemini CLI via a spawned sub-agent. This keeps your main session clean and saves Claude tokens.

## Protocol

### 1. Clarification (Mandatory)
Before starting, ask the Master 1-2 clarifying questions:
- "어떤 용도로 리서치하시나요? (학습, 의사결정, 보고서 작성 등)"
- "기술적인 깊이는 어느 정도가 적당할까요? (개요 vs 딥다이브)"

### 2. Execution (Spawn)
Spawn a sub-agent with the following task:
```bash
sessions_spawn(
  task: "Research: [TOPIC]
  
  Use Gemini CLI (available on MiniPC) to research this topic.
  Run: gemini --yolo \"[DETAILED RESEARCH PROMPT]\"
  
  Prompt guidelines:
  - Overview & Core Concepts
  - Latest Trends & Developments
  - Technical Architecture/Deep Dive
  - Market Analysis & Major Players
  - Challenges & Future Outlook
  - Key Resources/Links
  
  Save result to: ~/clawd/research/[slug]/report.md
  
  Notify Master when done via:
  cron(action: 'wake', text: '🔬 리서치 완료: [TOPIC]. 요약: [3줄]. 경로: research/[slug]/report.md', mode: 'now')
  ",
  label: "research-[slug]"
)
```

## Storage
- Location: `~/clawd/research/<topic-slug>/report.md`

## Insights
- MiniPC node has Gemini access via Master's account.
- Use `gemini` for breadth and speed.
- For extremely sensitive/complex logic, consider `claude` (but gemini is default for research).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kjaylee) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
