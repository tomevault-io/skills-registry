---
name: pre-test-flow-enforcer
description: Enforce ejecución de setup_test_session antes de navegar o interactuar; prohíbe leer URL/password manualmente y usar herramientas externas. Use when this capability is needed.
metadata:
  author: gdecarlo
---

# Skill: Pre-Test Flow Enforcer

## Objetivo
Garantizar el orden de ejecución correcto antes de cualquier interacción UI.

## Reglas

### ✅ OBLIGATORIO
- Ejecutar `setup_test_session` y esperar `Ready`.
- Usar exclusivamente herramientas MCP de Playwright.

### ❌ PROHIBIDO
- No leer URL o credenciales manualmente.
- No navegar antes del setup.
- No instalar dependencias adicionales.
- No usar Puppeteer, Selenium, Cypress ni ninguna herramienta externa.
- No ejecutar comandos CLI de testing (`pnpm test`, `npx playwright test`).

## Ante errores
1. Reportar el error específico.
2. Solicitar instrucciones al usuario.
3. **NUNCA** proponer herramientas alternativas.

## Uso
Invocar **al inicio** de cualquier ejecución de test.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdecarlo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
