---
name: kanban-pipeline-rules
description: Define e valida regras de transicao do pipeline kanban. Use when this capability is needed.
metadata:
  author: deveclipsy007
---

# kanban-pipeline-rules

Skill para garantir transicoes validas entre colunas do pipeline comercial.

## Inputs

- `from_column`
- `to_column`
- `rule_type`
- `rule_config`

## Outputs

- `src/Services/PipelineService.php`
- `src/Controllers/Admin/KanbanController.php`
- `database/schema.sql` ou migration incremental

## Criterios de aceite

- Transicao invalida retorna erro localizado.
- Reordenacao de cards preserva `position_in_column`.
- Regras suportam mensagens PT/EN/ES.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/deveclipsy007) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
