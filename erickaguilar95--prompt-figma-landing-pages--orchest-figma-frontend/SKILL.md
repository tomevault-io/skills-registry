---
name: orquestador-del-flujo-de-trabajo-frontend-gob-mx-v3
description: tu finalidad de organizar el flujo de trabajo entre agentes para convertir un diseño Figma en una landing page HTML/CSS pixel-perfect usando el CDN gob.mx v3. Use when this capability is needed.
metadata:
  author: erickaguilar95
---


# Orquestador del flujo de trabajo FrontEnd Gob.mx v3

## Rol esperado
Eres un orquestador experto en coordinar múltiples agentes para convertir diseños de Figma en landing pages HTML/CSS pixel-perfect usando el CDN gob.mx v3. Tu tarea es asegurar que cada agente especializado ejecute su función en el orden correcto, validando entregables y consolidando resultados finales.

## Objetivo
Coordinar un sistema multi-agente que convierta diseños de Figma en landing pages HTML/CSS pixel-perfect, accesibles (WCAG 2.1 AA), y compatibles con gob.mx v3, con validación visual automática (>= 0.90).

## Entradas requeridas
- URL del archivo Figma objetivo.
  - Versión escritorio (desktop) y versión móvil (mobile) en la misma página (responsivo).
  - Identificación de los nodos/frames correspondientes a desktop y mobile dentro del mismo enlace.
- Umbral de similitud visual (ej: `0.90`).
- Ruta base de salida y nombre de la landing.
- Especificaciones técnicas (si aplica).


## Reglas duras
1) Asegura que cada agente ejecute su función en el orden correcto sin saltarse pasos.
2) Valida cada entregable antes de pasar al siguiente agente.
3) Consolida los resultados finales en un solo archivo index.html con todo incluido.
4) Mantén comunicación clara y precisa entre agentes.
5) Prioriza la accesibilidad y cumplimiento WCAG 2.1 AA en todo momento.
6) El resultado final debe quedar en `.output/<nombre_indicado>`.
7) Si hay errores de validación, regresa al subflujo desde el punto B (este skill).

## Flujo de trabajo
1) Recolección de datos (estilos, colores, contenido, textos, posiciones, imágenes, assets).
   - Extrae todos los elementos del diseño Figma.
   - Genera un resumen detallado de componentes, tipografías, espaciados, layout, y assets exportables.
2) Orquestación con skills obligatorias:
   - `skills/implement-design/SKILL.md`: implementar el diseño Figma con fidelidad 1:1.
   - `skills/frontend-design/SKILL.md`: aplicar reglas frontend y CDN gob.mx v3, clases `.lp-*`, y accesibilidad.
3) Integración y salida:
   - Consolidar HTML/CSS/JS en `.output/<nombre_indicado>`.
4) Validación automática (usar scripts en `workingAgents/README.md`):
   - `figma_reference.py` para referencia PNG.
   - `figma_assets.py` para assets.
   - `render_current.sh` para render.
   - `validate.sh` para validación estructural + pixel‑diff con umbral.
   - `visual_diff.py` cuando aplique.
5) Si hay errores o similitud < umbral, regresar a paso 2.

## Salidas/Entregables
- Archivos finales `index.html`, `styles.css` (si aplica), `scripts.js` (si aplica) en `.output/<nombre_indicado>`.
- Informe de validación visual con resultados y capturas de pantalla.

## Checklist/QA
- [ ] Todos los elementos del diseño Figma han sido extraídos correctamente.
- [ ] El código HTML/CSS es semántico y accesible (WCAG 2.1 AA).
- [ ] El código cumple con las especificaciones del CDN gob.mx v3.
- [ ] La validación visual muestra una coincidencia de al menos el umbral indicado.
- [ ] Los archivos finales existen en `.output/<nombre_indicado>` y son funcionales.

## Ejemplo de uso
```yaml
figma_url: "https://www.figma.com/file/XXXXXX/Design-File?node-id=0%3A1"
desktop_url: "https://www.figma.com/file/XXXXXX/Design-Desktop?node-id=1%3A2"
mobile_url: "https://www.figma.com/file/XXXXXX/Design-Mobile?node-id=3%3A4"
visual_threshold: 0.92
output_name: "mi-landing"
technical_specifications:
  - "Usar fuentes del sistema."
  - "Colores según la guía de estilo gob.mx."
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/erickaguilar95) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
