---
name: logic-validator
description: Relatório com score, status, breakdown, warnings, suggestions. Use when this capability is needed.
metadata:
  author: fabioeloi
---

# Logic Validator

## Critérios (peso)

| Critério | Peso | Verificação |
|----------|------|-------------|
| coverage | 0.25 | Cada feature/story mapeada em >= 1 diagrama |
| consistency | 0.25 | Mesma entidade = mesmos atributos em todos diagramas |
| completeness | 0.20 | Happy path + sad path presentes |
| traceability | 0.15 | Todo nó Mermaid rastreável a um ID do PRD |
| naming_coherence | 0.10 | Nomenclatura consistente (sem aliases conflitantes) |
| dependency_integrity | 0.05 | Ordem de dependências respeitada nos fluxos |

## Modos de Validação

- **interactive**: apresenta cada diagrama + score, aguarda approve/reject/modify
- **batch**: apresenta tudo, aguarda approve_all/reject_all/select
- **auto**: aprova se score >= threshold, rejeita caso contrário

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioeloi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
