---
name: frontend-principles
description: Criterios de trabajo para HTML semántico, CSS y JavaScript vanilla. Use when this capability is needed.
metadata:
  author: pqem
---

# Frontend Principles

## Propósito
Definir criterios operativos para mantener HTML semántico, CSS claro y JavaScript vanilla simple en este repositorio estático.

## HTML semántico
- Usar etiquetas semánticas (`header`, `nav`, `main`, `section`, `article`, `footer`).
- Mantener jerarquía lógica de títulos (`h1` → `h2` → `h3`).
- Evitar `div` si existe una etiqueta semántica adecuada.
- Mantener una estructura clara y fácil de leer.

## CSS (orden, responsive, simplicidad)
- Priorizar estilos legibles y consistentes.
- Mantener un orden lógico (layout → componentes → utilidades).
- Usar enfoque mobile-first y ajustar con breakpoints mínimos.
- Evitar reglas innecesarias o duplicadas.
- Preferir cambios locales antes que reestructuras globales.

## JavaScript vanilla (claridad, modularidad básica)
- Usar JavaScript vanilla, sin librerías ni frameworks.
- No agregar JS si el cambio puede resolverse solo con HTML o CSS.
- Mantener funciones pequeñas y legibles.
- Agrupar lógica por módulo simple (objeto, IIFE o patrón equivalente).
- Evitar variables globales innecesarias.
- Inicializar lógica en `DOMContentLoaded` cuando aplique.

## Regla final
- Si un cambio rompe claridad o simplicidad, detenerse y pedir confirmación.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pqem) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
