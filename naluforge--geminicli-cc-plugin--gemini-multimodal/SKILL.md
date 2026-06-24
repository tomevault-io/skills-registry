---
name: gemini-multimodal
description: >- Use when this capability is needed.
metadata:
  author: NaluForge
---

# Multimodal Processing with Gemini

**Invoke using `/gemini-media` or `mcp__gemini__gemini_execute` with file paths in the prompt.**

## Supported Media Types

Gemini can directly process:
- **Video**: MP4, WebM, MOV — analysis, summarization, scene detection
- **Audio**: MP3, WAV, FLAC — transcription, speaker detection, analysis
- **Images**: PNG, JPG, WebP, GIF — OCR, analysis, diagram interpretation
- **PDFs**: Multi-page document analysis, extraction, summarization

## Pre-Flight Validation

Before sending files to Gemini:
1. **Verify files exist**: Use Glob or Read to confirm all paths are valid
2. **Check file sizes**: Very large files (>1GB video) may need segmenting
3. **Confirm file type**: Verify the extension matches expected content

## Parameter Selection by Media Type

| Media | Model | Timeout | Notes |
|-------|-------|---------|-------|
| Video (long) | pro | 2400000 | Complex temporal analysis |
| Video (short) | flash | 300000 | Quick extraction |
| Audio (long) | pro | 2400000 | Full transcription |
| Audio (short) | flash | 300000 | Quick transcription |
| Images | flash | 300000 | Most image tasks are fast |
| Complex diagrams | pro | 300000 | Architecture, flowcharts |
| PDFs (long) | pro | 2400000 | Multi-page analysis |
| PDFs (short) | flash | 300000 | Quick extraction |

## Output Structure by Media Type

### Video
- Include timestamps: "At 2:34, the speaker discusses..."
- Reference visual elements: "The diagram shown at 5:12 illustrates..."
- For long videos, provide a timeline summary first, then details

### Audio
- Include timestamps for key moments
- Attribute speakers when possible: "Speaker A (likely the interviewer)..."
- Note audio quality issues that may affect accuracy

### Images
- Use spatial references: "In the top-right corner...", "The second row..."
- For diagrams, describe the structure before details
- For screenshots, identify UI elements and their state

### PDFs
- Reference page numbers: "On page 3, section 2.1..."
- For tables, describe structure and key data points
- For forms, list fields and their values

## Combining with Code Context

Multimodal analysis often feeds into code work:
- Screenshot → identify UI components → generate code
- Architecture diagram → map to file structure → verify alignment
- Error screenshot → identify the error → find relevant code
- PDF spec → extract requirements → plan implementation

---
> Source: [NaluForge/geminicli-cc-plugin](https://github.com/NaluForge/geminicli-cc-plugin) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
