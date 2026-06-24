---
name: frontend-qa-friendly
description: Orienta marcação e estrutura do frontend para facilitar testes E2E (WebdriverIO/Appium): testID, acessibilidade, seletores estáveis. Use ao escrever ou revisar componentes, formulários ou telas que serão testados por QA. Use when this capability is needed.
metadata:
  author: rondleysg
---

# Frontend QA-friendly

Ao escrever ou revisar código de frontend (incluindo React Native) que será testado por QA (E2E com WebdriverIO/Appium), garantir que a interface permita **seletores estáveis**: testID, accessibilityLabel, roles acessíveis. Evitar dependência de posição no DOM ou classes de estilo.

## Objetivo

Frontend que permita aos testes localizar e interagir com elementos de forma rápida e estável, sem XPaths frágeis como `div[1]/div[2]/input`.

## Contexto do projeto (React Native / App)

- Os testes E2E usam **testID** (Android: `id=`, iOS: `~`) via helper `getElementByTestID` e **texto** / **accessibilityLabel** via `getElementByText` e `getElementByAccessibilityLabel`.
- Garantir que elementos interativos tenham **testID** estável e documentado (ex.: `login-enter-with-phone-button`, `login-phone-input`) para que os screenobjects e specs os localizem de forma confiável.

## Formulários

- **Labels:** Associar cada input a um label (acessibilidade); em React Native, usar `accessibilityLabel` quando não houver label visível.
- Evitar inputs sem nome acessível; os testes usam `getElementByText` ou testID.
- **Placeholder:** Manter estável ou documentar; testes podem usar placeholder como fallback.

## Botões e links

- Usar texto visível ou `accessibilityLabel` estável para que os testes usem `getElementByText` ou `getElementByTestID`.
- Evitar botões/links só com ícone sem accessibilityLabel.

## testID (principal hook no projeto)

- Usar **testID** em elementos que os testes precisam localizar (botões, inputs, listas, itens de lista).
- Convenção estável e documentada (ex.: `home-category-{name}`, `login-send-code-button`). Os testes usam `getElementByTestID('login-send-code-button')`.
- Não exagerar; preferir testID em elementos de interação e em containers que precisem ser encontrados (ex.: scrollviews).

## Evitar

- **XPath por posição:** Seletores que quebram quando o layout ou a estrutura mudam.
- **Classes de estilo como único hook:** Nomes minificados ou hasheados mudam entre builds; não depender deles para seletores de teste.
- **Textos ou placeholders que mudam com i18n** sem fallback estável (testID ou accessibilityLabel) para os testes.

## Loading e feedback

- Expor estados claros para os testes aguardarem (ex.: botão desabilitado, elemento de loading com testID, mensagem de sucesso com texto ou testID estável).
- Mensagens de sucesso/erro: usar texto estável ou testID para que os testes façam assert com `getElementByText` ou `getElementByTestID` + `waitForDisplayed`.

## Checklist ao escrever ou revisar

- [ ] Elementos interativos têm testID estável (e/ou accessibilityLabel quando aplicável).
- [ ] Formulários: inputs com label ou accessibilityLabel.
- [ ] Botões e links com texto visível ou aria-label/accessibilityLabel.
- [ ] testID usado onde os testes precisam localizar; convenção estável.
- [ ] Nenhum seletor crítico depende apenas de ordem de div ou classes de estilo.
- [ ] Estados de loading e resultado são detectáveis (testID ou texto estável).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rondleysg) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
