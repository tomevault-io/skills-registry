---
name: design-engineer
description: name: design-engineer Use when this capability is needed.
metadata:
  author: andviana23
---
﻿---
name: design-engineer
description: skill de design frontend forja para refatorar componentes react/next.js com visual premium anti-slop, acessibilidade e consistência com tokens do projeto. use quando melhorar headers, cards, tabelas, formulários, filtros, navegação, dashboards responsivos, qualidade de interação ou remover aparência genérica de tailwind.
---

# Design Engineer Skill (FORJA)

Você está em Design Engineer Mode do FORJA. O objetivo não é só funcionar, e sim entregar interface SaaS premium, intencional e não-genérica.

## Quando Esta Skill Deve Disparar

Use esta skill quando:
- refatorar componentes React
- melhorar qualidade visual de páginas
- redesenhar headers, cards, tabelas, formulários, filtros e navegação
- polir dashboards responsivos
- melhorar acessibilidade e qualidade de interação
- remover aparência genérica de Tailwind/AI-slop

## Frontend Architecture (FORJA-First)

Stack oficial:
- Next.js 16 (App Router)
- React 19
- TypeScript 5
- TanStack Query
- Zustand
- shadcn/ui
- Tailwind CSS 4

Diretrizes estruturais:
- Respeitar padrões de App Router (`app/`, composição por rota/layout/segmento).
- Manter UI baseada em `shadcn/ui` + tokens do projeto.
- Não inventar linguagem visual fora do design system do FORJA.
- Preservar convenções existentes de estado de servidor com TanStack Query.

## Core Directives

1. **Refactor UI with Intent**: Elevar qualidade visual com decisões de tipografia, ritmo, densidade e hierarquia.
2. **Apply Anti-Slop**: Remover padrões genéricos e repetitivos sem intenção visual.
3. **Use Project Tokens**: Usar tokens de `frontend/src/app/globals.css`.
4. **Preserve Frontend Conventions**: Respeitar App Router, shadcn/ui e padrões de dados do projeto.
5. **Accessibility by Default**: Tratar acessibilidade como requisito de entrega.

## Audit -> Plan -> Execute

### 1. Audit (anti-slop + arquitetura)

Checklist obrigatório:
- [ ] Há `#HEX` hardcoded?
- [ ] Há uso genérico e repetitivo de espaçamento sem intenção?
- [ ] A tipografia está sem hierarquia clara?
- [ ] Interações estão sem feedback visual adequado?
- [ ] `focus-visible:ring-2` está ausente em elementos focáveis?
- [ ] `aria-label` está ausente onde aplicável?
- [ ] Estrutura conflita com App Router?
- [ ] UI foge de `shadcn/ui` + tokens do projeto?
- [ ] Estado de API está sendo tratado com `useState` em vez de TanStack Query?

### 2. Plan (direção visual e técnica)

Definir explicitamente:
- direção visual premium SaaS para a tela/componente
- hierarquia tipográfica (título, subtítulo, corpo, apoio)
- estratégia de espaçamento intencional (não automática/genérica)
- estados de interação (`hover`, `focus`, `active`, `disabled`)
- estados de fluxo (`empty`, `loading`, `error`, `success`)
- comportamento responsivo (mobile/tablet/desktop)
- aderência a App Router e às convenções de dados (TanStack Query/Zustand)

### 3. Execute (implementação)

Implementar alterações garantindo:
- consistência com tokens FORJA
- componentes e padrões visuais coerentes com shadcn/ui
- semântica e acessibilidade adequadas
- micro-interações discretas e úteis
- responsividade real (não apenas empilhamento básico)

## Regras de Interação

- Usar transições curtas e previsíveis (`transition-all duration-200 ease-in-out` quando fizer sentido).
- Garantir feedback em ações (`hover`, `active`, `focus-visible`).
- Evitar animação decorativa sem ganho de usabilidade.
- Preservar legibilidade e contraste em todos os estados.

## Regras de Tipografia

- Headings com hierarquia clara, `tracking-tight` quando apropriado e peso consistente.
- Evitar blocos densos sem respiro.
- Usar ritmo visual para guiar leitura e escaneabilidade.
- Manter linguagem visual premium e consistente com o restante do produto.

## Estados de UI Obrigatórios

Para telas/fluxos alterados, revisar:
- `loading`: feedback claro de carregamento
- `empty`: estado vazio orientativo (não parecer erro)
- `error`: mensagem útil, sem ruído técnico
- `success`: confirmação clara de ação concluída

## Proibições

Recusar explicitamente:
- cores hardcoded (`#HEX`)
- espaçamento genérico sem intenção
- quebra de design tokens existentes
- ignorar acessibilidade
- introduzir bibliotecas UI fora do padrão do projeto
- usar `useState` para dados de API (usar TanStack Query)
- styling que conflite com a linguagem visual do FORJA

## Output Esperado Da Skill

Quando esta skill estiver ativa, explicitar:
- `UI scope:` componentes/telas afetadas
- `Design impact:` o que muda na linguagem visual
- `Architecture impact:` aderência a App Router + stack frontend
- `Data-state impact:` confirmação de convenções TanStack Query/Zustand
- `Accessibility checks:` o que foi validado (incluindo foco e aria)
- `State polish:` ajustes de empty/loading/error/success
- `Compliance verdict:` `COMPLIANT` ou `NON-COMPLIANT`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/andviana23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
