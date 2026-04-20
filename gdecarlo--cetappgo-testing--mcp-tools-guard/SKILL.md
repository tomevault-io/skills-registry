---
name: mcp-tools-guard
description: Enforce uso exclusivo de Playwright MCP y bloquear acciones prohibidas (no .spec.ts, no playwright.config.ts, no CLI tests, no herramientas externas). Use when this capability is needed.
metadata:
  author: gdecarlo
---

# Skill: MCP Tools Guard

## Objetivo
Evitar acciones prohibidas antes y durante la ejecución de pruebas, garantizando el uso exclusivo del stack definido.

## Stack autorizado (ÚNICO)
| Herramienta | Permitido | Uso |
|-------------|-----------|-----|
| Playwright MCP (`mcp_playwright_*`) | ✅ | Interacción con browser |
| Script `html_to_pdf.js` | ✅ | Generación de PDF |
| Script `optimize-images.js` | ✅ | Optimización de imágenes |
| `sharp` | ✅ | Procesamiento de imágenes |

## Reglas obligatorias

### ❌ PROHIBICIONES DE ARCHIVOS
- No crear archivos `.spec.ts`, `.test.ts`, `.spec.js`, `.test.js`
- No crear ni modificar `playwright.config.ts` o `playwright.config.js`
- No crear archivos de configuración de testing (jest.config, cypress.config, etc.)

### ❌ PROHIBICIONES DE COMANDOS CLI
- `pnpm test`, `npm test`, `yarn test`
- `npx playwright test`, `npx cypress`, `npx jest`
- Cualquier comando que ejecute suites de test automatizados

### ❌ PROHIBICIONES DE INSTALACIÓN DE DEPENDENCIAS
- `pnpm add puppeteer`, `npm install puppeteer`, `yarn add puppeteer`
- `pnpm add selenium-webdriver`, `npm install selenium-webdriver`
- `pnpm add cypress`, `npm install cypress`
- `pnpm add -D <cualquier-herramienta-de-testing>`
- **Cualquier comando de instalación de herramientas de automatización de browser**

### ❌ PROHIBICIONES DE HERRAMIENTAS EXTERNAS
- **Puppeteer**: No usar para screenshots, PDF, ni ningún propósito
- **Selenium/WebDriver**: Prohibido completamente
- **Cypress**: Prohibido completamente
- **TestCafe**: Prohibido completamente
- **Cualquier alternativa** a Playwright MCP

### ❌ PROHIBICIONES DE CÓDIGO
- No ejecutar `node -e` con código de automatización
- No crear scripts ad-hoc para interacción con browser
- No usar `page.screenshot()` en código generado (usar herramienta MCP)

## ✅ Uso correcto
- Usar exclusivamente herramientas MCP de Playwright para interacción UI
- Usar `html_to_pdf.js` para generación de PDF
- Usar `optimize-images.js` para optimización de imágenes
- Reportar limitaciones sin proponer herramientas alternativas

## Ante errores o limitaciones
1. **DETENER** la ejecución
2. **REPORTAR** el problema específico al usuario
3. **ESPERAR** instrucciones
4. **NUNCA** intentar instalar herramientas alternativas

## Uso
Ejecutar este guard **antes** de iniciar cualquier flujo de test.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdecarlo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
