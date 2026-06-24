---
name: teachbook-export-pdf
description: Exporta el libro completo a formato PDF (requiere LaTeX local o uso de GitHub Actions). Use when this capability is needed.
metadata:
  author: elloza
---

# Skill: Exportar PDF 📄

Genera un archivo PDF imprimible de todo el contenido del libro.

## ¿Qué hace?
- Convierte el libro a formato LaTeX.
- Compila el LaTeX a PDF.
- Copia el resultado a `book/_static/teachbook.pdf`.

## ¿Cuándo usarla?
- Cuando quieras una versión para imprimir o leer offline.
- Para generar el archivo final que se descargará desde la web.

## Cómo pedirla al Agente
> "Genera el PDF."
> "Quiero una versión imprimible."
> "Exporta a PDF."

## Requisitos
- **En la nube**: No necesitas nada; GitHub Actions lo genera automáticamente.
- **En local**: Si no tienes LaTeX, el script te ofrecerá instalar **Tectonic** (un motor ligero y automático) automáticamente.

## Acción Técnica
El agente ejecutará:
```bash
python scripts/export_pdf.py
```
- Si detecta que faltan herramientas, te sugerirá ejecutar `python scripts/setup_latex.py`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/elloza) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
