---
name: performance-optimization
description: Melhores práticas essenciais de performance para React e Next.js inspiradas pela Vercel. Use when this capability is needed.
metadata:
  author: joabeoliveira
---

# Skill: Especialista em Performance (Vercel Stack)

## Goal
Garantir que as aplicações sejam ultra-rápidas, evitando gargalos de rede (waterfalls), otimizando o tamanho do bundle e maximizando o desempenho do lado do servidor.

## Instructions

### 1. Eliminação de Waterfalls (Prioridade Máxima)
- **Parallel Data Fetching**: Sempre que possível, iniciar requisições independentes simultaneamente usando `Promise.all()` ou iniciando as promises antes do `await`.
- **Suspense Boundaries**: Envolver componentes que fazem fetch de dados em `<Suspense>` para permitir o streaming da interface.

### 2. Otimização de Bundle e Entrega
- **Dynamic Imports**: Usar `next/dynamic` para carregar componentes pesados (gráficos, editores, modais complexos) apenas quando necessário.
- **Evitar Barrel Files**: Importar componentes diretamente de seus caminhos específicos em vez de arquivos de índice globais que podem puxar código desnecessário.
- **Imagens**: Sempre usar o componente `next/image` com dimensões apropriadas e prioridade para imagens acima da dobra (LCP).

### 3. Server-Side & RSC (React Server Components)
- **Minimização de Serialização**: Passar apenas os dados estritamente necessários do Server para o Client Component. Evitar passar objetos de banco de dados completos.
- **React Cache**: Utilizar `React.cache()` para memorizar funções de busca de dados dentro de uma mesma requisição.
- **Server Actions**: Validar e autenticar Server Actions de forma rigorosa, tratando-as como rotas de API.

### 4. Ciclo de Vida e Re-renders
- **Derivação de Estado**: Nunca usar `useEffect` para transformar dados que podem ser calculados durante a renderização.
- **Memoização Estratégica**: Usar `useMemo` e `useCallback` apenas para cálculos realmente caros ou para manter a estabilidade de objetos passados como dependências.

## Constraints
- **CRITICAL**: Nunca bloquear a renderização de toda a página por causa de uma única requisição lenta; use streaming/suspense.
- **Bundle**: Manter o "First Load JS" abaixo de 100kb por página sempre que possível.
- **Performance**: Priorizar legibilidade sobre micro-otimizações de JavaScript puro (ex: loops manuais vs .map), a menos que haja um gargalo comprovado.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joabeoliveira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
