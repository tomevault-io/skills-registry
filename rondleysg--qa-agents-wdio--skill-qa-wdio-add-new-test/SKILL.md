---
name: qa-wdio-add-new-test
description: Adiciona um novo teste E2E (WebdriverIO) seguindo as convenções do projeto: Mocha describe/it, imports relativos, screenobjects, helpers e data. Use quando o usuário pedir para adicionar teste, criar teste ou cenário em um fluxo. Use when this capability is needed.
metadata:
  author: rondleysg
---

# Add new test (E2E WebdriverIO)

Ao adicionar um novo teste ao projeto E2E em **__tests__/e2e/**, seguir estas convenções.

## Localização

- Arquivo em **__tests__/e2e/specs/** (na subpasta do fluxo, se houver), com sufixo **.spec.ts**.

## Imports

- Sempre **relativos** a partir do spec (ex.: `../../data/Constants`, `../../screenobjects/HomeScreen`, `../../helpers/Utils`).
- Nunca importar `@playwright/test` ou APIs Playwright.

## Estrutura do teste

- **Runner:** Mocha — `describe`, `it`, `before`, `after`.
- **Device/Browser:** Usar `getDeviceFromCapabilities('deviceA')` ou `getDeviceFromCapabilities('browserA')` conforme o config (helpers/Utils.ts).
- **Screenobjects:** Usar métodos dos screenobjects para interações (ex.: `HomeScreen.waitForIsShown()`, `CartScreen.tapOnAddButton()`).
- **Data:** Usar constantes de **data/Constants.ts** ou **data/Logins.ts** (ex.: `PLACE_NAME_TO_TEST`, `LOGINS_TO_TEST`) em vez de valores fixos quando fizer sentido.
- **Seletores:** Via helpers `getElementByTestID`, `getElementByText`, `getElementByAccessibilityLabel` (Utils.ts) ou dentro de screenobjects.
- **Assertions:** `expect` (Mocha/Chai); aguardar com `waitForDisplayed`, `waitForExist` quando necessário. Ex.: `expect(element).toBeDisplayed()`, `expect(value).toEqual(expected)`.
- **Arrange-Act-Assert:** Estrutura clara; nome do teste descritivo (cenário + resultado esperado).
- **Async:** Todas as interações e assertions assíncronas com async/await.

## Allure (opcional)

Se o projeto usar `@wdio/allure-reporter`, seguir o padrão dos specs existentes (ex.: `allureReporter.addFeature`, `allureReporter.startStep`/`endStep`).

## Checklist

- [ ] Arquivo em __tests__/e2e/specs/ com sufixo .spec.ts
- [ ] Imports relativos (data, screenobjects, helpers)
- [ ] Uso de data/ quando há dados reutilizáveis
- [ ] Nome do teste descreve cenário e resultado esperado
- [ ] Async/await usado de forma consistente

## References

- Exemplos: __tests__/e2e/specs/basic-features.spec.ts, __tests__/e2e/specs/cart/checkout-cart.spec.ts
- Helpers: __tests__/e2e/helpers/Utils.ts

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rondleysg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
