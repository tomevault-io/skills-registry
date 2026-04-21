---
name: qa-wdio-add-test-data
description: Adiciona ou estende dados de teste em __tests__/e2e/data/ (Constants, Logins ou novos módulos TypeScript). Use quando o usuário pedir para adicionar constantes, dados de login ou dados reutilizáveis para os testes E2E. Use when this capability is needed.
metadata:
  author: rondleysg
---

# Add test data (E2E WebdriverIO)

Ao adicionar ou estender dados de teste no projeto E2E, usar a pasta **__tests__/e2e/data/**.

## Onde

- **Pasta:** __tests__/e2e/data/
- **Arquivos existentes:** Constants.ts (URLs, nomes de place, package name, variáveis de ambiente com fallback), Logins.ts (credenciais app/partners).
- **Novos módulos:** Criar novos arquivos TypeScript quando fizer sentido (ex.: Places.ts, Products.ts) com export nomeado.

## Convenções

- **Constantes e config estática:** Constants.ts ou novo módulo. Usar `process.env` para valores sensíveis ou por ambiente, com fallback (ex.: `process.env.BACKEND_URL || 'https://...'`).
- **Credenciais e logins:** Logins.ts ou módulo dedicado; não commitar segredos em texto puro; preferir variáveis de ambiente quando sensível.
- **Exports:** Export nomeado (ex.: `export const LOGINS_TO_TEST`, `export const PLACE_NAME_TO_TEST`). Nos specs, importar com caminho relativo (ex.: `import { PLACE_NAME_TO_TEST } from '../../data/Constants'`).
- **Dados variáveis:** Para valores que mudam por execução (ex.: único por run), usar funções ou builders em data/ ou em helpers, conforme o padrão já existente no projeto.

## Checklist

- [ ] Pasta __tests__/e2e/data/; arquivo existente (Constants.ts, Logins.ts) ou novo módulo
- [ ] Exports nomeados; specs importam com caminho relativo
- [ ] Dados sensíveis via process.env quando aplicável

## References

- Estrutura atual: __tests__/e2e/data/Constants.ts, __tests__/e2e/data/Logins.ts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rondleysg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
