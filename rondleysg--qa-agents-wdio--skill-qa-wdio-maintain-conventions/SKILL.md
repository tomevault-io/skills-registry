---
name: qa-wdio-maintain-conventions
description: Mantém as convenções do projeto E2E (WebdriverIO) ao editar specs, screenobjects, helpers ou config em __tests__/e2e. Use ao editar specs, refatorar ou quando o usuário pedir alinhamento ao padrão do projeto. Use when this capability is needed.
metadata:
  author: rondleysg
---

# Maintain conventions (E2E WebdriverIO)

Ao editar specs, screenobjects, helpers, data ou config em **__tests__/e2e/**, garantir que as convenções do projeto sejam seguidas.

## Estrutura (__tests__/e2e/)

- **config/** — Configuração WebdriverIO (wdio.shared.conf.ts, wdio.android.conf.ts, wdio.ios.conf.ts). Hooks globais (before, after, etc.) ficam aqui.
- **data/** — Dados reutilizáveis: Constants.ts, Logins.ts e outros módulos TypeScript. Evitar hardcode nos specs.
- **helpers/** — Funções utilitárias: Utils.ts (getElementByTestID, getElementByText, getDeviceFromCapabilities, loginApp, etc.), Manager.ts, Partners.ts, RedisClient.ts.
- **screenobjects/** — Objetos de tela (ex.: LoginScreen, HomeScreen) herdando AppScreen quando aplicável; uso de testID e helpers de Utils para seletores.
- **specs/** — Arquivos de teste (*.spec.ts), organizados por fluxo em subpastas (ex.: specs/cart/). Mocha describe/it, imports relativos.

## Specs

- **Imports:** Sempre relativos (ex.: `../../data/Constants`, `../../screenobjects/HomeScreen`, `../../helpers/Utils`). Nunca importar `@playwright/test` ou APIs Playwright.
- **Data:** Preferir **__tests__/e2e/data/** (Constants, Logins) em vez de valores fixos nos specs; usar `process.env` para config sensível quando aplicável (com fallback em Constants).
- **Seletores:** Usar helpers (`getElementByTestID`, `getElementByText`, `getElementByAccessibilityLabel`) e screenobjects; seletores estáveis (testID, accessibilityLabel).
- **Async:** Uso consistente de async/await; comandos WebdriverIO retornam promises.

## Checklist ao editar

- [ ] Imports relativos a partir do arquivo; nenhum import de Playwright
- [ ] Dados reutilizáveis em data/, não inline nos specs
- [ ] Novos testes em specs/ (e subpastas por fluxo); novos data em data/; novos screenobjects em screenobjects/
- [ ] Testes E2E usam seletores estáveis (testID, accessibilityLabel) via helpers ou screenobjects

## References

- [AGENTS.md](../../../AGENTS.md) — resumo de convenções
- Estrutura: **__tests__/e2e/** (config, data, helpers, screenobjects, specs)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rondleysg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
