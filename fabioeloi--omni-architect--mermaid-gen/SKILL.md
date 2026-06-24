---
name: mermaid-generator
description: Array de objetos {type, code, coverage_pct, source_features}. Use when this capability is needed.
metadata:
  author: fabioeloi
---

# Mermaid Generator

## Mapeamento

| Elemento PRD | Tipo Mermaid | Condição |
|-------------|-------------|----------|
| flows | flowchart TD | Sempre que existirem fluxos |
| user_stories + entities | sequenceDiagram | Quando houver interações ator-sistema |
| entities + relationships | erDiagram | Quando houver >= 2 entidades |
| features com estados | stateDiagram-v2 | Quando features tiverem lifecycle |
| system overview | C4Context | Quando PRD mencionar sistemas externos |
| personas + journeys | journey | Quando personas estiverem definidas |

## Regras

- Validar sintaxe Mermaid antes de emitir (parser check)
- Labels no idioma do locale configurado
- Máximo 50 nós por diagrama; se exceder, auto-split com diagrama índice
- Referenciar IDs de features/stories como comentários no código Mermaid

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioeloi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
