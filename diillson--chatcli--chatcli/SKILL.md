---
name: mermaid-diagrams
description: Create diagrams as code — flowcharts, sequence, class, ER, gantt, architecture — using Mermaid, and render them to SVG/PNG locally (mermaid-cli) or via the keyless Kroki service. Use when asked to "draw a diagram", "make a flowchart", "diagram this architecture", "sequence diagram of". Use when this capability is needed.
metadata:
  author: diillson
---

# Mermaid Diagrams

Diagrams-as-code. Author Mermaid markup, then render it. Two render paths — prefer local, fall
back to the keyless Kroki HTTP service.

## 1. Author the Mermaid

Write a `.mmd` file. Examples:

Flowchart:
```
flowchart TD
  A[Client] --> B{Auth?}
  B -- yes --> C[Serve]
  B -- no --> D[401]
```

Sequence:
```
sequenceDiagram
  User->>API: POST /login
  API-->>User: 200 + token
```

Mermaid also covers `classDiagram`, `erDiagram`, `gantt`, `stateDiagram-v2`, `mindmap`.

## 2. Render

**Local (preferred)** — `@mermaid-cli` (`mmdc`):
```
mmdc -i diagram.mmd -o diagram.svg          # or -o diagram.png -t dark -b transparent
```
Detect: `command -v mmdc` / `Get-Command mmdc`. Install: `npm i -g @mermaid-js/mermaid-cli`.

**Keyless service (no install)** — Kroki:
```
curl -s -X POST https://kroki.io/mermaid/svg -d @diagram.mmd -o diagram.svg
```
(Use `@webfetch`/curl. For private content prefer local rendering — Kroki receives the source.)

## Rules

- Keep the diagram readable: group/label nodes, limit to what answers the question.
- For chat/messaging contexts, you can also just send the fenced ```mermaid block as text — many
  clients render it. Render to an image when the user wants a file.
- Report the output path after rendering.

---
> Source: [diillson/chatcli](https://github.com/diillson/chatcli) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
