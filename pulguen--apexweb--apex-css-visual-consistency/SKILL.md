---
name: apex-css-visual-consistency
description: Corregir y mejorar estilos CSS del sitio Apex manteniendo consistencia visual, uso de variables de tema en App.css y comportamiento responsive. Usar cuando se pidan ajustes de espaciado, tipografia, contraste, alineacion o refinamiento visual entre componentes. Use when this capability is needed.
metadata:
  author: pulguen
---

# Apex CSS Visual Consistency

## Objetivo

Aplicar mejoras visuales sin romper la identidad actual ni introducir deuda de estilos.

## Ejecutar este flujo

1. Revisar tokens globales en `src/App.css` antes de cambiar reglas locales.
2. Priorizar variables CSS (`--primary-color`, `--radius`, `--shadow`, etc.) sobre valores hardcodeados.
3. Editar CSS del componente y dejar en `App.css` solo reglas globales compartidas.
4. Resolver responsive con media queries minimas y puntuales.
5. Mantener estados interactivos visibles (`hover`, `focus`) en botones y links.

## Reglas del proyecto

- Reutilizar `titulo` y `parrafo` para coherencia tipografica.
- Mantener contraste legible en todas las secciones.
- Preservar jerarquia visual entre titulos, contenido y detalles.
- No incorporar nuevos frameworks CSS.

## Checklist de salida

- Confirmar ausencia de overflow horizontal.
- Confirmar consistencia de espaciado vertical entre secciones.
- Confirmar que colores nuevos provienen de variables del tema.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pulguen) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
