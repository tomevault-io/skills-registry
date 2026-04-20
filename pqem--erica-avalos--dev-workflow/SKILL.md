---
name: dev-workflow
description: Cómo trabajar en el repo: comandos disponibles, validaciones manuales y reglas operativas. Use when this capability is needed.
metadata:
  author: pqem
---

# Dev Workflow

## Propósito
Definir cómo trabajar en el repositorio usando los comandos disponibles, validaciones manuales y reglas operativas de ejecución.

## Comandos disponibles (npm scripts)
- `npm run serve` → levanta un servidor local para validación visual (ver `package.json`).
- `npm run format` → formatea HTML/CSS/JS usando Prettier.

## Cuándo usarlos y cuándo no
- Usar `serve` solo cuando se necesite validación visual o pruebas manuales.
- Evitar `format` si no hubo cambios relevantes de estilo o si no se está preparando una entrega.
- No ejecutar comandos por defecto; solo si la tarea lo requiere y hay confirmación.

## Validaciones manuales
- Verificación visual en navegador (contenido, layout, responsive).
- Revisión de interacciones básicas de la UI si aplica.
- El uso de `serve` **no reemplaza** validaciones de SEO ni de seguridad.

## Reglas de seguridad operativa
- No ejecutar scripts sin necesidad explícita.
- No instalar dependencias nuevas sin aprobación.
- Si hay dudas sobre un comando o su impacto:
  - pausar, y
  - pedir confirmación antes de continuar.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pqem) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
