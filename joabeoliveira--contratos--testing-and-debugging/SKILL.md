---
name: testing-debugging
description: Padrões de testes automáticos e estratégias de depuração para garantir estabilidade e qualidade do código. Use when this capability is needed.
metadata:
  author: joabeoliveira
---

# Skill: Especialista em Qualidade e Debugging

## Goal
Garantir que a aplicação seja resiliente a falhas através de uma estratégia sólida de testes e facilitar a resolução de problemas com práticas de debug desde o dia zero.

## Instructions

### 1. Estratégia de Testes
- **Testes Unitários (Vitest)**: Criar testes para lógica de negócio pura, utilitários e funções do Prisma em `@/lib` ou `@/services`.
- **Testes de Integração**: Testar Server Actions e rotas de API simulando entradas do usuário e verificando efeitos no banco de dados.
- **E2E (Playwright)**: Criar fluxos críticos (ex: login, checkout, integração n8n) para garantir que as partes principais do sistema funcionam juntas.

### 2. Práticas de Debugging
- **Logging Estruturado**: Usar um padrão de log (ex: `console.info`, `console.error`) com prefixos claros para identificar se o log vem do Server ou do Client.
- **Tratamento de Erros**: Sempre envolver operações críticas em blocos `try/catch` e utilizar o componente `error.tsx` do Next.js para capturar falhas de renderização.
- **Zod Debug**: Ao falhar uma validação de esquema, disparar logs detalhados do erro do Zod para identificar rapidamente qual campo está inválido.

### 3. Ambiente de Desenvolvimento
- **Debug no VS Code**: Manter um arquivo `.vscode/launch.json` configurado para debugar o servidor Next.js e o navegador.
- **Previews**: Utilizar estados de carregamento (`loading.tsx`) e esqueletos de UI (`Skeleton`) para testar a percepção de performance durante o desenvolvimento.

## Constraints
- **CRITICAL**: Nunca subir código para produção com `console.log` de depuração esquecidos.
- **Mocking**: Evitar mocks excessivos em testes de integração; prefira usar um banco de dados de teste real (ex: Docker com Postgres).
- **Cobertura**: Focar na cobertura de caminhos críticos (Happy Path e Error Handling) em vez de buscar 100% de cobertura de linhas irrelevantes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/joabeoliveira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
