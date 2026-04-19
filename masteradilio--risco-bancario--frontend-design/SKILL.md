---
name: frontend-design
description: Use quando o usuário solicitar para construir, editar, corrigir ou fazer melhorias no frontend do projeto dele. Use when this capability is needed.
metadata:
  author: masteradilio
---

---
name: Frontend Design Expert
description: Skill para criar interfaces profissionais, modernas e visualmente impressionantes - eliminando o visual genérico de IA
trigger: Ative quando trabalhar em componentes UI, páginas, layouts, estilização ou qualquer tarefa de frontend
---

# Frontend Design Expert Skill

## 🎯 Missão Principal
Criar interfaces que pareçam feitas por um **design engineer experiente**, não por IA genérica. Eliminar completamente o "AI slop" - aquele visual padrão com gradientes roxos, bordas arredondadas excessivas e layouts previsíveis.

## 🚫 O QUE NUNCA FAZER (Anti-Patterns de IA)

### Visual Genérico a Evitar:
- ❌ Gradientes roxo-para-azul ou rosa-para-laranja sem propósito
- ❌ Bordas arredondadas excessivas (`rounded-3xl` em tudo)
- ❌ Sombras genéricas (`shadow-lg` em todos os cards)
- ❌ Ícones decorativos sem função
- ❌ Animações gratuitas em tudo
- ❌ Espaçamentos inconsistentes
- ❌ Tipografia monótona (mesmo peso/tamanho)
- ❌ Cards com a mesma estrutura repetida
- ❌ CTAs genéricos como "Get Started" ou "Learn More"
- ❌ Hero sections com ilustração abstrata + texto centralizado

## ✅ PRINCÍPIOS DE DESIGN PROFISSIONAL

### 1. Hierarquia Visual Clara
Estabeleça 3-4 níveis de importância visual
O elemento mais importante deve ser óbvio instantaneamente
Use tamanho, peso, cor e espaço para criar hierarquia
Cada página deve ter UM foco principal claro



### 2. Sistema de Tipografia Intencional
Escala Tipográfica Recomendada (usando clamp para responsividade):

--font-size-xs: clamp(0.75rem, 0.7rem + 0.25vw, 0.875rem) --font-size-sm: clamp(0.875rem, 0.8rem + 0.375vw, 1rem) --font-size-base: clamp(1rem, 0.9rem + 0.5vw, 1.125rem) --font-size-lg: clamp(1.125rem, 1rem + 0.625vw, 1.25rem) --font-size-xl: clamp(1.25rem, 1.1rem + 0.75vw, 1.5rem) --font-size-2xl: clamp(1.5rem, 1.25rem + 1.25vw, 2rem) --font-size-3xl: clamp(1.875rem, 1.5rem + 1.875vw, 2.5rem) --font-size-4xl: clamp(2.25rem, 1.75rem + 2.5vw, 3.5rem)

Regras:

Máximo 2 famílias tipográficas por projeto
Contraste de peso: use 400 vs 600/700, não 400 vs 500
Line-height: 1.2-1.3 para headings, 1.5-1.7 para body
Letter-spacing: ligeiramente negativo para headings grandes

### 3. Sistema de Espaçamento Consistente
Use escala de 4px ou 8px base:

--space-1: 0.25rem (4px) --space-2: 0.5rem (8px) --space-3: 0.75rem (12px) --space-4: 1rem (16px) --space-5: 1.5rem (24px) --space-6: 2rem (32px) --space-8: 3rem (48px) --space-10: 4rem (64px) --space-12: 6rem (96px) --space-16: 8rem (128px)

Regras:

Espaçamento interno (padding) menor que externo (margin/gap)
Elementos relacionados = menos espaço entre eles
Seções distintas = mais espaço entre elas
Consistência > Perfeição pixel



### 4. Paleta de Cores Sofisticada
Estrutura de Cores Profissional:

COR PRIMÁRIA: Uma cor de marca forte
Use em CTAs principais, links, elementos de destaque
Máximo 10-15% da interface
NEUTROS: 5-7 tons de cinza

Background principal (mais claro)
Background secundário
Bordas sutis
Texto secundário
Texto principal (mais escuro)
SEMÂNTICAS:

Success: Verde (confirmações, sucesso)
Warning: Âmbar (alertas, atenção)
Error: Vermelho (erros, destruição)
Info: Azul (informativo)
Regras:

Contraste WCAG AA mínimo (4.5:1 para texto)
Cores de fundo: sempre sutis, nunca saturadas
Se usar dark mode: não é apenas inverter cores



### 5. Micro-interações e Animações
Princípios:

Animações devem ter PROPÓSITO (feedback, orientação, deleite)
Duração: 150-300ms para UI, 300-500ms para transições de página
Easing: ease-out para entradas, ease-in para saídas
Prefira transform e opacity (GPU-accelerated)
Padrões Recomendados:

Hover em botões: scale(1.02) + sutileza na cor
Hover em cards: translateY(-2px) + sombra sutil
Transições de estado: fade + slide sutil
Loading: skeleton screens > spinners
EVITE:

Bounce excessivo
Animações longas (>500ms) para ações frequentes
Movimento em elementos de texto principal



### 6. Layout e Composição
Grid System:

Use CSS Grid para layouts de página
Use Flexbox para alinhamento de componentes
Container max-width: 1200-1400px para conteúdo
Gutters consistentes: 16px mobile, 24-32px desktop
Técnicas de Layout Profissional:

Bento grid para dashboards
Asymmetric layouts para landing pages
Card grids com tamanhos variados (não uniformes)
Whitespace generoso - deixe o design respirar



### 7. Componentes de UI Refinados

#### Botões:
```css
/* Primário - Sólido */
.btn-primary {
  background: var(--color-primary);
  color: white;
  padding: 0.625rem 1.25rem;
  border-radius: 0.5rem;
  font-weight: 500;
  transition: all 150ms ease-out;
}
.btn-primary:hover {
  background: var(--color-primary-dark);
  transform: translateY(-1px);
}

/* Secundário - Outline */
.btn-secondary {
  background: transparent;
  border: 1px solid var(--color-border);
  color: var(--color-text);
}

/* Ghost - Minimal */
.btn-ghost {
  background: transparent;
  color: var(--color-text-muted);
}
.btn-ghost:hover {
  background: var(--color-bg-subtle);
}
Cards:
css


/* Card Sutil - Preferível */
.card {
  background: var(--color-bg-card);
  border: 1px solid var(--color-border-subtle);
  border-radius: 0.75rem;
  padding: 1.5rem;
}

/* Card com Hover */
.card-interactive {
  transition: all 200ms ease-out;
}
.card-interactive:hover {
  border-color: var(--color-border);
  box-shadow: 0 4px 12px rgba(0,0,0,0.05);
  transform: translateY(-2px);
}
Inputs:
css


.input {
  width: 100%;
  padding: 0.625rem 0.875rem;
  border: 1px solid var(--color-border);
  border-radius: 0.5rem;
  font-size: var(--font-size-base);
  transition: border-color 150ms, box-shadow 150ms;
}
.input:focus {
  outline: none;
  border-color: var(--color-primary);
  box-shadow: 0 0 0 3px var(--color-primary-alpha);
}
🎨 REFERÊNCIAS DE DESIGN DE ALTA QUALIDADE
Estude e inspire-se nestes produtos:

Linear - Precisão, minimalismo funcional
Vercel - Elegância em dark mode, tipografia impecável
Stripe - Gradientes sutis, documentação exemplar
Raycast - Micro-interações, atenção aos detalhes
Notion - Clareza, hierarquia de informação
Arc Browser - Inovação em UI, cores vibrantes com propósito
📐 PROCESSO DE IMPLEMENTAÇÃO
Antes de Codar:
Identifique o propósito da página/componente
Defina a hierarquia - o que é mais importante?
Escolha referências - que produto existente faz isso bem?
Planeje os estados - default, hover, active, disabled, loading, error, empty
Durante a Implementação:
Comece pelo layout e espaçamento
Adicione tipografia e hierarquia
Aplique cores com intenção
Refine com micro-interações
Teste responsividade
Valide acessibilidade
Checklist Final:
 Hierarquia visual clara - o olho sabe onde ir primeiro?
 Espaçamento consistente - nada parece "apertado" ou "solto"?
 Tipografia variada - há contraste de tamanho/peso?
 Cores com propósito - cada cor tem uma razão?
 Interações suaves - hovers e transições estão naturais?
 Mobile-first - funciona bem em telas pequenas?
 Acessibilidade - contraste adequado, focus states visíveis?
🔧 STACK RECOMENDADA PARA FRONTEND


Framework: Next.js / React
Styling: Tailwind CSS + CSS Variables para design tokens
Componentes: shadcn/ui (customizado) ou Radix UI primitives
Ícones: Lucide React (consistentes, limpos)
Animações: Framer Motion (para complexas) ou CSS transitions
Fontes: Inter, Geist, SF Pro, ou System UI stack
💡 DICAS ESPECÍFICAS PARA SAAS
Landing Page:
Hero: Proposta de valor clara + CTA único + social proof
Features: Mostre o produto real, não ícones abstratos
Pricing: Destaque o plano recomendado visualmente
CTA Final: Repita a proposta de valor
Dashboard:
Sidebar colapsável com ícones claros
Header com busca, notificações, perfil
Cards de métricas com sparklines
Tabelas com sorting, filtering, pagination
Empty states informativos e acionáveis
Loading states com skeletons
Forms:
Labels sempre visíveis (não só placeholder)
Validação inline em tempo real
Mensagens de erro específicas e úteis
Progress indication para forms longos
Confirmação clara de sucesso

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/masteradilio) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
