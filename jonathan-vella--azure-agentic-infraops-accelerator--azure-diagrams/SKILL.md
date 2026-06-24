---
name: azure-diagrams
description: ROUTING SKILL — delegates to specialized diagram skills. USE FOR: any diagram request when the caller does not know which tool to use. Routes to drawio, python-diagrams, or mermaid based on diagram type. Use when this capability is needed.
metadata:
  author: jonathan-vella
---

# Azure Diagrams — Routing Skill

This skill routes diagram requests to the appropriate specialized skill.
Do NOT load this skill's references directly — load the target skill instead.

## Routing Table

| Diagram type                                   | Target skill      | Output format     |
| ---------------------------------------------- | ----------------- | ----------------- |
| Architecture diagrams (default)                | `drawio`          | `.drawio`         |
| Dependency / runtime diagrams                  | `drawio`          | `.drawio`         |
| As-built diagrams                              | `drawio`          | `.drawio`         |
| WAF bar charts                                 | `python-diagrams` | `.py` + `.png`    |
| Cost donut / projection charts                 | `python-diagrams` | `.py` + `.png`    |
| Compliance gap charts                          | `python-diagrams` | `.py` + `.png`    |
| Python architecture diagrams (diagrams lib)    | `python-diagrams` | `.py` + `.png`    |
| Swimlane / ERD / timeline / wireframe          | `python-diagrams` | `.py` + `.png`    |
| Inline markdown diagrams (flowchart, sequence) | `mermaid`         | fenced code block |
| Azure resource visualization                   | `mermaid`         | fenced code block |
| Hand-drawn whiteboarding / brainstorming       | `excalidraw`      | `.excalidraw`     |

## How to Use

1. Identify the diagram type from the request.
2. Read `.github/skills/{target-skill}/SKILL.md` instead of this file.
3. Follow that skill's generation workflow and guardrails.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jonathan-vella) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
