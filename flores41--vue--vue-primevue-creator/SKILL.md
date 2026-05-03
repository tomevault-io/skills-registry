---
name: vue-primevue-creator
description: Úsalo SIEMPRE para crear o modificar componentes. Configurado para Vue 3.5+ (JavaScript puro), PrimeVue 4.5+ y Vite 8. Use when this capability is needed.
metadata:
  author: Flores41
---

# Instrucciones de Ejecución

1. **Stack Estricto (JS):** Usa `<script setup>`. Nada de TypeScript. El código debe ser JavaScript moderno (ES6+).
2. **PrimeVue 4.x y PrimeIcons 7:** USA EXCLUSIVAMENTE componentes de PrimeVue 4.5.5 para la interfaz y `primeicons` para la iconografía. No reinventes elementos nativos.
3. **Archivos Cortos y Clean Architecture:** La vista es solo para renderizar. Si la lógica crece, extráela a un composable JS (`use...`).
4. **JSDoc Obligatorio:** Como usamos JS puro, debes documentar rigurosamente las `props`, `emits` y el estado usando JSDoc (`@type`, `@param`, `@returns`) para mantener un buen autocompletado e inferencia.
5. **Linting:** El código debe cumplir con las reglas de ESLint 10 y formatearse limpio para Prettier y Oxlint.

---
> Source: [Flores41/vue](https://github.com/Flores41/vue) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-04-26 -->
