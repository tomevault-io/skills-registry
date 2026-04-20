---
name: generate-html-report
description: Generar el reporte HTML final usando el template oficial y la lista de evidencias; usar al finalizar el test. Use when this capability is needed.
metadata:
  author: gdecarlo
---

# Skill: Generate HTML Report

## Objetivo
Crear el reporte HTML final en:
`evidence/{sourceFile}/{ticketId}/{ticketId}_reporte.html`

Además, incluir métricas de tiempo:
- **Tiempo de evidencia**: desde que el usuario pide la ejecución hasta justo antes de generar el reporte.
- **Tiempo de generación de reporte**: tiempo total invertido en generar el HTML.

## Pasos
1. Leer el template: `.github/skills/evidence-generator/template-html-base.html`.
2. Reemplazar variables requeridas (ticket, status, ambiente, URL, usuario, objetivo, datos, response, errores).
3. Insertar la sección de evidencias con las imágenes generadas.
4. **Insertar las métricas de tiempo** en el reporte (sección de Información General o Nota):
	- `evidenceDurationMs` y `evidenceDurationHuman`
	- `reportGenerationDurationMs` y `reportGenerationDurationHuman`
5. Guardar el HTML en la carpeta de evidencia.
6. Ejecutar el skill `generate_pdf_report` para crear el PDF en la misma carpeta del HTML.
7. Generar el archivo `raw-{TC_ID}-result.md` en `evidence/{ticketId}/{TC_ID}/` con el resultado de la prueba formateado para copiar y pegar en un comentario de Jira, usando la siguiente estructura:

   ```markdown
   Historia de Usuario: [Titulo de la HU]

   Ambiente: test X

   Resultado de Prueba:
   {TC_ID} - [Titulo del TC] - ✅ PASS / ❌ FAIL

   Validaciones:
   ✓ [Validación 1]
   ✓ [Validación 2]
   ...

   Fecha de ejecución: DD de Mes de AAAA
   ```

8. Eliminar evidencias temporales en `.playwright-mcp/evidence/` **solo después** de confirmar que las evidencias finales ya fueron generadas en `evidence/`.

## Entrada mínima
- `ticketId`, `ticketTitle`, `status`
- `sourceFile`, `evidenceDir`
- `environment`, `baseUrl`, `user`
- `objective`, `steps`, `validations`
- `testData`, `response`, `consoleErrors`
- `evidenceImages[]`
- `evidenceStartTime`, `evidenceEndTime`
- `reportGenerationStartTime`, `reportGenerationEndTime`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gdecarlo) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
