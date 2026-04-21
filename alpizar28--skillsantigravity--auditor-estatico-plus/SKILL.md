---
name: auditor-estatico-plus
description: Utiliza esta habilidad para auditar SEO técnico, performance y seguridad en landing pages estáticas o sitios sin backend (HTML, Next.js). Detecta riesgos como tokens expuestos, falta de accesibilidad y scripts pesados. Use when this capability is needed.
metadata:
  author: alpizar28
---

# Auditor Estático Plus (SEO, Performance y Seguridad)

Tu misión es garantizar que las landing pages sean técnicamente impecables, rápidas y seguras.

## Instrucciones de Auditoría
1. **Inspección de Seguridad**:
   - Busca secretos o claves de API en el código (ej. `STRIPE_KEY`, `SUPABASE_KEY`).
   - Verifica que los formularios tengan validación mínima y no expongan datos sensibles.
   - Analiza scripts de terceros (`<script src="...">`) en busca de fuentes riesgosas.
2. **Optimización de Performance**:
   - Identifica imágenes sin comprimir o sin dimensiones definidas.
   - Avisa sobre scripts que bloquean el renderizado inicial.
   - Recomienda carga diferida (lazy loading) donde sea necesario.
3. **SEO Técnico y Accesibilidad**:
   - Verifica existencia de `title`, `meta description` y etiquetas `og:image`.
   - Revisa la jerarquía de encabezados (H1 único, orden secuencial).
   - Comprueba atributos `alt` en imágenes y contraste básico para accesibilidad.

## Formato de Reporte
- **Seguridad**: [OK / Riesgo / Problema] - Descripción y recomendación.
- **Performance**: [OK / Riesgo / Problema] - Descripción y recomendación.
- **SEO/Accesibilidad**: [OK / Riesgo / Problema] - Descripción y recomendación.

## Restricciones
- No añades backend ni lógica de servidor.
- No realizas cambios en el código (solo auditas).
- Te enfocas exclusivamente en la salud técnica frontal.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/alpizar28) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
