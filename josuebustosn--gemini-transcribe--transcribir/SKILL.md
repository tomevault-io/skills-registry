---
name: transcribir
description: Transcribe one or more audio or video files to a structured Markdown document with timestamps and speaker diarization, using Google Gemini 3.5 Flash. Use this skill whenever the user provides audio/video files and wants them transcribed, or when the user says "transcribe esto", "transcribe estos audios", "pasame esto a texto", "que dice este audio", "transcripción", "voice note", "audio nota", "WhatsApp PTT", or similar. Supports .ogg, .mp3, .m4a, .wav, .opus, .flac, .aac, .webm, .mp4, .mov, .mpeg, .mpga. Auto-detects language (Spanish, English, etc.), identifies multiple speakers with timestamps, and outputs a clean MD next to each source file. Processes multiple files in parallel. Defaults to Gemini 3.5 Flash for the best speed/quality/cost balance (handles Spanish, Latin American accents, regionalisms, and multi-speaker diarization). Use the --pro flag to upgrade to gemini-pro-latest for maximum quality on critical audio, or --model X to specify any other model. Use when this capability is needed.
metadata:
  author: josuebustosn
---

# Skill: transcribir

Transcribe audios o videos a Markdown estructurado con timestamps e identificación de hablantes, usando Google Gemini 3.5 Flash (default) o cualquier modelo de Gemini vía flag.

## Cuándo invocar este skill

Activar cuando el usuario:

- Provee uno o varios paths de archivos de audio/video y quiere transcribirlos.
- Dice cualquiera de: "transcribe esto", "transcribe estos audios", "pásame esto a texto", "qué dice este audio", "transcribir", "voice note", "audio nota", "PTT", "WhatsApp PTT".
- Tipea `/transcribir`.
- Menciona ElevenLabs Scribe, Whisper, u otra herramienta de transcripción como referencia.

## Cómo invocar

La skill incluye `transcribe.py` junto a este `SKILL.md`. Se corre directamente con Python.

### Sintaxis básica

```bash
python "<SKILL_DIR>/transcribe.py" "<path-audio-1>" ["<path-audio-2>" ...]
```

Donde `<SKILL_DIR>` es el directorio donde la skill está instalada (típicamente `~/.claude/skills/transcribir/` en Linux/macOS o `%USERPROFILE%\.claude\skills\transcribir\` en Windows).

El script lee `GEMINI_API_KEY` (o `GOOGLE_API_KEY` como fallback) del entorno. Configurala via el gestor de variables de entorno de tu OS (ver el README del proyecto).

Output: por cada `<audio.ext>` el script escribe `<audio.ext>.transcripcion.md` junto al archivo original.

### Flags

- `--pro` — usar `gemini-pro-latest` (alias al último Pro estable de Google) en lugar del default `gemini-3.5-flash`. Más caro y lento, mejor para audios críticos con muchos hablantes, ruido fuerte o acentos atípicos.
- `--model <ID>` — especificar un modelo arbitrario (ej. `gemini-3-flash-preview`, `gemini-3.1-pro-preview`, `gemini-3.1-flash-lite`, `gemini-2.5-pro`).
- `--json` — además del MD, guardar también `<audio.ext>.transcripcion.json` con el dump crudo (útil para reprocesar).
- `--out <DIR>` — escribir los MDs en `<DIR>` en vez de junto al audio.
- `--combined <PATH.md>` — además de los MDs individuales, generar un MD unificado con todos los audios procesados en una sola sesión (índice + secciones por audio).
- `--workers N` — concurrencia (default: 3). No subir más de 5 para no chocar con rate limits.

### Ejemplos

Transcribir un audio:
```bash
python "$HOME/.claude/skills/transcribir/transcribe.py" "$HOME/Downloads/nota.ogg"
```

Transcribir todos los `.ogg` de una carpeta y producir un MD combinado:
```bash
python "$HOME/.claude/skills/transcribir/transcribe.py" --combined "./combinada.md" ./audios/*.ogg
```

Transcribir con Pro (máxima calidad) + JSON crudo:
```bash
python "$HOME/.claude/skills/transcribir/transcribe.py" --pro --json "./importante.ogg"
```

## Flujo recomendado para Claude Code

1. Detectar los paths de audio/video del input del usuario (ya sea un path concreto, un directorio, o un glob).
2. Si el usuario pasa una carpeta o glob, expandirlo a una lista de archivos.
3. Si hay más de un archivo, considerar pasar `--combined` para producir un MD unificado además de los individuales.
4. Ejecutar `python <skill-dir>/transcribe.py <paths...>` directamente. No envolver con gestores de secrets (Doppler, etc.) — la env var debe estar ya disponible en el entorno del usuario.
5. Reportar al usuario:
   - Path(s) del MD generado(s).
   - Resumen breve del contenido (1-2 líneas por audio).
   - Hablantes detectados, idioma, duración total.

## Notas técnicas

- **Modelo default:** `gemini-3.5-flash` con `thinking_budget=0` y `max_output_tokens=32768`. Mejor relación calidad/velocidad/costo según benchmarks de transcripción multilingüe.
- **Modelo --pro:** `gemini-pro-latest` — alias que apunta siempre al último Pro estable. Auto-upgrade cuando Google libere nuevos Pro estables.
- **Idioma:** auto-detect. El skill conserva el voseo/tuteo del hablante y regionalismos sin normalizar.
- **Fidelidad literal:** el prompt fuerza al modelo a NO interpretar palabras por contexto. Falsos comienzos (ej. "una sencilla sub... ah, tiene una función"), tartamudeos ("si si el"), muletillas y autocorrecciones del hablante se conservan tal cual.
- **Diarización:** etiqueta hablantes como `Hablante 1`, `Hablante 2`, etc. Si solo hay uno, todo queda bajo `Hablante 1`.
- **Segmentación:** granular, 4-15s por segmento. Corta en cambios de hablante, cambios de tema, pausas largas, o cada 15s máximo.
- **Tamaño máx por archivo inline:** ~18 MB. Archivos más grandes usan Files API (upload + reference). El script lo decide automáticamente.
- **Concurrencia:** 3 simultáneos default. Cada llamada a `genai.Client` se crea por thread porque el cliente NO es thread-safe (descubierto empíricamente, causa truncations silenciosas si se comparte).

## Anti-patterns

- **No** envolver el script con gestores de secrets en cada invocación — la env var `GEMINI_API_KEY` debe vivir en el entorno permanente del usuario.
- **No** reescribir manualmente la transcripción — el JSON estructurado de Gemini ya viene segmentado.
- **No** subir a más de 5 workers concurrentes — Gemini puede empezar a rate-limitear.
- **No** modificar el prompt para que "limpie" o "mejore" la transcripción — la fidelidad literal es intencional para que Claude Code (u otros consumidores downstream) reciba el contenido real, no una interpretación.

---
> Source: [josuebustosn/gemini-transcribe](https://github.com/josuebustosn/gemini-transcribe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
