---
name: task-planning
description: Skill para quebrar PRDs em tasks implementaveis Use when this capability is needed.
metadata:
  author: renanlido
---

# Task Planning Skill

Este skill e ativado quando o usuario quer quebrar um PRD em tasks implementaveis.

## Quando Usar

- Apos PRD estar pronto
- Para planejar sprint
- Para estimar esforco
- Para definir ordem de implementacao

## Template

Use o template em `templates/task-template.md` como base.

## Processo

1. **Analisar PRD**
   - Identificar todos os requisitos funcionais
   - Mapear dependencias entre requisitos
   - Identificar componentes tecnicos

2. **Decompor em Tasks**
   - Uma task = uma entrega atomica
   - Cada task deve ser testavel
   - Cada task deve ter criterios claros

3. **Ordenar Tasks**
   - Dependencias tecnicas primeiro
   - Setup antes de implementacao
   - Testes junto com implementacao (TDD)

4. **Documentar**
   - Arquivos a criar/modificar
   - Criterios de aceitacao
   - Testes necessarios

## Principios

- **Atomicidade**: Task faz uma coisa so
- **Testabilidade**: Pode validar que esta pronto
- **Independencia**: Pode ser feita em uma sessao
- **Clareza**: Sem ambiguidade no que fazer

## Outputs

- `.claude/plans/features/<nome>/tasks.md`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/renanlido) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
