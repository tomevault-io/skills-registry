---
name: ui-audit
description: Audita componentes UI do FORJA para identificar violações do design system, problemas de acessibilidade e desvios da arquitetura frontend. Use when this capability is needed.
metadata:
  author: andviana23
---

# UI Audit Skill (FORJA)

Analisa componentes UI e identifica violações das regras do FORJA Design System e da arquitetura frontend.

## Regras de Auditoria

### 1. Typography & Readability (Anti-Slop)
- **Headings**: Devem usar `tracking-tight`, peso `semibold` e `text-balance` (multiline).
- **Body**: Dashboards usam `text-sm`, textos secundários `text-muted-foreground`.

### 2. Spacing & Layout
- **Vertical Rhythm**: Substituir `gap-4` por `gap-1.5` (Tiny) a `gap-12` (Section).
- **Containers**: Usar `container mx-auto` e evitar larguras fixas.

### 3. Visuals & Depth
- **Borders**: Usar apenas `border` ou `bg-muted`. Proibido cores hardcoded.
- **Shadows**: Cards usam `shadow-sm`, Popovers `shadow-lg`.

### 4. Accessibility (WCAG AA)
- **Foco**: `focus-visible:ring-2` obrigatório.
- **Semântica**: Usar tags HTML5 (`nav`, `section`, `article`).

### 5. Arquitetura Frontend
- **Checklist**: Página usa Hook? Hook usa TanStack Query? Formulário usa Zod?

## Classificação de Impacto
- 🔴 **P0 (Crítico)**: Cores hardcoded, sem acessibilidade, breach de arquitetura (`useState` para API).
- 🟡 **P1 (Alto)**: AI Slop (`gap-4`, `rounded-md` genérico), lógica no service.
- 🟢 **P2 (Médio)**: Micro-interações ausentes, otimização de shadows.

## Template de Relatório
Use o comando `/ui-audit` para gerar um relatório estruturado com diagnóstico, plano de correção e checklist de qualidade.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andviana23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
