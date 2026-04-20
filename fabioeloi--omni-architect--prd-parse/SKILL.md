---
name: prd-parser
description: Score de completude do PRD (0.0 - 1.0). Use when this capability is needed.
metadata:
  author: fabioeloi
---

# PRD Parser

## Instruções

1. Tokenizar o PRD por heading levels (H1, H2, H3)
2. Classificar cada seção por tipo semântico (feature, story, requirement, entity, flow)
3. Extrair entidades nomeadas (NER) para identificar domínio
4. Mapear relacionamentos entre entidades
5. Calcular grafo de dependências entre features
6. Computar score de completude
7. Emitir warnings se score < 0.6 com sugestões de melhoria

## Heurísticas de Classificação

| Padrão no texto | Classificação |
|-----------------|---------------|
| "Como [persona], quero..." | User Story |
| "Requisito:", "Deve..." | Requisito Funcional |
| "Performance:", "Segurança:" | Requisito Não-Funcional |
| Tabelas com atributos | Entidade de Domínio |
| "Fluxo:", listas numeradas de passos | Fluxo de Negócio |
| "Critério de aceite", checkboxes | Acceptance Criteria |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fabioeloi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
