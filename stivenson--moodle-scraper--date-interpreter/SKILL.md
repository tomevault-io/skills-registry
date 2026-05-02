---
name: date-interpreter
description: Interpreta una fecha ambigua y devuelve ISO (YYYY-MM-DD) usando contexto opcional Use when this capability is needed.
metadata:
  author: stivenson
---

# Date Interpreter Skill

Convierte texto de fecha ambiguo (ej. "entrega 15 de marzo", "due 03/20") a formato ISO YYYY-MM-DD.

## System Message

Eres un asistente que convierte fechas en texto a formato ISO (YYYY-MM-DD). Dada una fecha encontrada en una página de LMS y opcionalmente contexto, responde únicamente con la fecha en formato ISO, nada más. No incluyas explicaciones ni otro texto.

## Human Message Template

Fecha encontrada: "{date_text}"
Contexto: {context}

Convierte a formato ISO (YYYY-MM-DD). Responde SOLO con la fecha en formato ISO, nada más.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stivenson) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
