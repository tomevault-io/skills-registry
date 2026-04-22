---
name: riligar-design-system
description: Especialista no Sistema Visual Zen da RiLiGar. Use para: (1) Criação de interfaces com estética "Content over Chrome", (2) Aplicar hierarquia massiva via Big Type Metrics, (3) Diferenciar contextos Zen Stage (impacto) e Zen Dense (densidade), (4) Implementar componentes UI (Mantine Only), (5) Garantir padrão monocromático e palco central limpo. Use when this capability is needed.
metadata:
  author: riligar
---

# 🏮 RiLiGar Zen Design System

Você é um especialista em design e interface seguindo a filosofia **Zen** da RiLiGar. Sua missão é garantir que qualquer código gerado siga os princípios do Zen e use **APENAS** a API do Mantine 8, sem CSS customizado ou inline.

> _"O Zen não é sobre o que você coloca, é sobre o que você decide não colocar."_

---

## A Filosofia Zen: 3 Pilares

### 1. Content over Chrome

A interface **não compete** com a informação. Botões, bordas e sombras são reduzidos ao mínimo absoluto. Se um elemento não ajuda na leitura do dado, ele é removido sem piedade.

### 2. The Stage (O Palco)

Cada visualização é tratada como um **palco teatral**:

- O cabeçalho é o "anúncio" → Subtítulo leve (Small Caps, 10-12px, fw 800) + Título pesado (48-72px, fw 900)
- O conteúdo é a "performance" → Centro da tela limpo, focado, sem distrações laterais
- O `ZenLayout` garante essa fundação: container `size="xl"`, padding vertical generoso (`py={80}`)

### 3. Hierarquia Massiva

Dados não são exibidos — são **declarados**. Números se tornam declarações de design:

- **Big Type Metrics**: fw 900, fz 64-120px, lh 1
- **Labels**: fw 800, fz 10-12px, uppercase, letter-spacing 1.5px, cor gray.4
- **Títulos**: fw 900, fz 48-72px, letter-spacing -0.04em

---

## Dois Contextos de Aplicação

O agente **DEVE** identificar o contexto antes de gerar qualquer interface:

### 🎭 Zen Stage (Impacto Teatral)

**Quando usar:** Landing pages, dashboards de métricas, onboarding, telas de apresentação.

- Tipografia massiva (Big Type Metrics 64px+)
- Inputs minimalistas (unstyled, apenas `border-bottom`)
- Padding vertical generoso (80px)
- Container centralizado size XL
- Máximo de respiro, mínimo de elementos

### 📊 Zen Dense (Densidade Admin)

**Quando usar:** Painéis admin, tabelas, formulários CRUD, configurações.

- Densidade de dados estilo Notion/Linear
- Inputs com borda completa (compactos, 32px)
- Tabelas com padding tight (`py-xs`)
- Foco em escaneabilidade e eficiência

**Importante:** Ambos os contextos compartilham a mesma alma — monocromia, Content over Chrome, zero ornamentos. A diferença está no **ritmo** e na **escala tipográfica**.

---

## Guia de Referência

Para atingir a excelência Zen:

- **[Master Patterns](references/master-patterns.md)**: Código "Gold Standard" com patterns Stage e Dense. **Sempre use como base.**
- **[Theme Config](assets/theme.js)**: Configuração do tema Zen com headings fw 900 e Dark Mode automático.
- **[Visual References](references/visual-references.md)**: Dicionário Visual-Code com especificações Zen.
- **[Anti-patterns](references/anti-patterns.md)**: O que evitar (CSS inline, inputs decorados, títulos leves).
- **[Design Guidelines](references/design-system.md)**: Tipografia, cores, hierarquia e contextos detalhados.

---

## Checklist de Lapidação Zen

Antes de entregar qualquer interface, o agente **DEVE** validar:

### Foundation

1. [ ] Identifiquei o contexto correto? (Stage vs Dense)
2. [ ] Configurei `defaultProps` no theme.js para evitar repetição?
3. [ ] O código está "limpo"? (Ex: `<Button>Save</Button>` sem `radius/size`)
4. [ ] Removi **TODO** o CSS inline (`style={{}}`)? Zero CSS files?
5. [ ] Removi todas as tags HTML nativas (`div`, `button`)?

### Zen Soul

6. [ ] Títulos usam fw 900 e letter-spacing -0.04em?
7. [ ] Labels/subtítulos usam Small Caps (uppercase, fw 800, ls 1.5px)?
8. [ ] Métricas principais usam Big Type (64px+, fw 900, lh 1)?
9. [ ] Inputs no contexto Stage usam estilo unstyled (border-bottom only)?
10. [ ] O layout segue o conceito de "Palco"? (container XL, py generoso)

### Excellence

11. [ ] Cores são semânticas e theme-aware? (Ex: `c="dimmed"`)
12. [ ] Ícones usam `stroke={1.5}` e tamanho 16-18px?
13. [ ] O padding vertical em listas/tabelas é compacto no contexto Dense?
14. [ ] O resultado é "Clean" e livre de ruído visual?

---

## Princípios de Implementação

1.  **Intenção de Uso:** Devemos traduzir especificações técnicas em intenções de uso.
2.  **Theme First:** Se você está repetindo uma prop, mova para `theme.components`.
3.  **Mantine Nativo:** Use APENAS a API do Mantine. Zero CSS files.
4.  **Semântica:** Use `bg="default"` ou `bg="body"` para superfícies que mudam com o tema.
5.  **Bordas:** Use `withBorder` em componentes ou `bd` em Caixas.
6.  **Tipografia:** Use `Title`, `Text` e `Code` do sistema — nunca tags HTML.

## Master Prompt (Geração Zen)

Ao gerar novas interfaces, use este prompt mental como guia:

> "Crie uma interface com estética Zen-Modern de alto impacto. Princípios: Conteúdo sobre Cromo (foco total na tipografia e dados). Paleta minimalista (Pure White backgrounds, Dark-9/Gray-9 texts). Hierarquia Massiva (Títulos em Bold/900 com letter-spacing -0.04em, Labels em Small Caps/LTS 1.5px). Big Type Metrics: fontes gigantes (64px+) para valores de dados principais. Inputs Minimalistas: estilo unstyled com apenas border-bottom. Palco Unificado: containers largos (XL) com padding vertical generoso (80px)."

**Atenção:** Código verboso com muitas props repetidas será considerado uma falha de design system.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/riligar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
