---
name: business-analysis
description: This skill should be used when the user asks to "analyze business processes", "understand client workflow", "map spreadsheet data", "extract entities from Excel", "create system requirements from documents", or mentions business analysis, process mapping, or requirements gathering from client documents. Use when this capability is needed.
metadata:
  author: matheuxlsx
---

# Business Analysis Skill

Provides templates, structured questions, and methodologies for analyzing small business processes and transforming manual workflows into system requirements.

## Discovery Areas

| Icon | Area | Focus |
|------|------|-------|
| 💼 | Negócio | Objetivo, problema, público |
| 👥 | Usuários | Quem usa, papéis, acessos |
| ⚙️ | Funcionalidades | O que o sistema faz |
| 💾 | Dados | O que precisa guardar |
| 🎨 | Interface | Visual, tema, cores |
| 🔐 | Segurança | Login, proteção |
| 🔗 | Integrações | APIs, pagamentos |
| 📊 | Relatórios | KPIs, dashboards |

## Question Templates

### 💼 Negócio

- Qual o principal problema que você quer resolver?
- Quem é o público-alvo do sistema?
- O que acontece se não automatizar isso?

### 👥 Usuários

- Quem vai usar o sistema diariamente?
- Existem diferentes níveis de acesso?
- Usuários externos (clientes) precisam acessar?

### ⚙️ Funcionalidades

- Quais são as tarefas mais repetitivas hoje?
- O que é indispensável no MVP?
- O que pode ficar para uma segunda fase?

### 💾 Dados

- Quais informações você rastreia hoje?
- Qual o volume aproximado (registros/mês)?
- Precisa de histórico? Por quanto tempo?

### 🔗 Integrações

- Precisa integrar com outros sistemas?
- Emite nota fiscal? Qual gateway?
- Usa algum sistema de pagamento?

## Output Templates

### Entidades (`ideia/entidades.md`)

```markdown
# Entidades Identificadas

## [Nome da Entidade]
- **Descrição:** O que representa
- **Atributos:** campo1, campo2, campo3
- **Relacionamentos:** conecta com [outra entidade]
- **Volume:** ~X registros/mês
- **Fonte:** Encontrado em [arquivo.xlsx]
```

### Fluxos (`ideia/fluxos.md`)

```markdown
# Fluxos de Processo

## [Nome do Processo]

### Situação Atual (AS-IS)
1. Etapa manual/sistema
2. Etapa manual/sistema

### Regras de Negócio
- Regra 1
- Regra 2

### Diagrama
[Usar Mermaid flowchart]
```

### Problemas (`ideia/problemas.md`)

```markdown
# Problemas Identificados

| Problema | Impacto | Área |
|----------|---------|------|
| Descrição | Alto/Médio/Baixo | Área afetada |
```

## Additional Resources

### Reference Files

- **`references/question-bank.md`** - Banco completo de perguntas por área
- **`references/common-entities.md`** - Entidades comuns em PMEs

### Templates

- **`templates/entidades.md`** - Template de entidades
- **`templates/fluxos.md`** - Template de fluxos

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/matheuxlsx) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
