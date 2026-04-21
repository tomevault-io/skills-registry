---
name: qa-wdio-add-setup
description: Adiciona ou estende setup compartilhado dos testes E2E: hooks no config WebdriverIO (before, after, beforeSuite, afterSuite) ou funções de setup em helpers. Use quando o usuário pedir para adicionar hook, setup global ou lógica compartilhada antes/depois dos testes. Use when this capability is needed.
metadata:
  author: rondleysg
---

# Add setup / hooks (E2E WebdriverIO)

No WebdriverIO não existem "fixtures" no sentido Playwright. O equivalente é **hooks no config** e **helpers de setup**.

## Onde

- **Hooks globais:** __tests__/e2e/config/wdio.shared.conf.ts (ou wdio.android.conf.ts, wdio.ios.conf.ts). Propriedades: `before`, `after`, `beforeSuite`, `afterSuite`, `beforeTest`, `afterTest`. Todas podem ser `async` e usar `await`.
- **Setup por suite/spec:** Dentro do arquivo de spec, usar `before()`/`after()` do Mocha no `describe()` e chamar funções de **__tests__/e2e/helpers/** (ex.: loginApp, flushRedisCache) para evitar duplicação.

## Hooks no config

- **before / after:** Executados uma vez por worker (antes de todos os specs / depois de todos os specs). Acesso a `capabilities`, `specs`; para interagir com dispositivo, usar `getDeviceFromCapabilities('deviceA')` (import de helpers/Utils).
- **beforeSuite / afterSuite:** Por suite Mocha. Recebem objeto `suite`.
- **beforeTest / afterTest:** Por teste. Úteis para screenshot em falha, limpeza, etc.
- Manter export do config; não remover hooks existentes sem necessidade.

## Helpers de setup

- Funções reutilizáveis em __tests__/e2e/helpers/ (ex.: Utils.ts com loginApp, flushRedisCache, reLaunchApp). Nos specs, importar e chamar no `before()` do describe quando vários testes precisarem do mesmo setup.

## Checklist

- [ ] Setup global → config (wdio.shared.conf.ts ou específico)
- [ ] Setup por fluxo/suite → before()/after() no describe + helpers
- [ ] Uso de async/await em hooks

## References

- Config: __tests__/e2e/config/wdio.shared.conf.ts
- Helpers: __tests__/e2e/helpers/Utils.ts, Manager.ts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rondleysg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
