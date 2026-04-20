---
name: asset-delivery
description: Pacote com todos artefatos, logs e documentação de handoff. Use when this capability is needed.
metadata:
  author: fabioeloi
---

# Asset Delivery

## Artefatos Gerados

| Artefato | Formato | Localização |
|----------|---------|-------------|
| PRD Parseado | JSON | `output/parsed-prd.json` |
| Diagramas Mermaid | .mmd + .svg | `output/diagrams/` |
| Relatório de Validação | JSON + MD | `output/validation/` |
| Figma Assets (links) | JSON | `output/figma-assets.json` |
| Orchestration Log | JSON | `output/orchestration-log.json` |
| Design Handoff Doc | Markdown | `output/HANDOFF.md` |

## Sanitização

- Tokens e secrets são removidos do log
- Informações sensíveis substituídas por `[REDACTED]`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioeloi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
