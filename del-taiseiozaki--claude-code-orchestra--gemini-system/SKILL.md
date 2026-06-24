---
name: gemini-system
description: | Use when this capability is needed.
metadata:
  author: del-taiseiozaki
---

# Gemini System — Multimodal File Processing

**Gemini CLI is specialized for multimodal file processing (PDF, video, audio, image).**

> **Detailed rules**: `.claude/rules/gemini-delegation.md`
> **Research/codebase analysis**: Use general-purpose subagent (Opus) instead — Opus now supports 1M context.

## Multimodal File Processing

Extract content from PDF, video, audio, and image files.

```bash
# PDF
gemini -p "Extract: {what to extract} @/path/to/file.pdf" 2>/dev/null

# Video
gemini -p "Summarize: key concepts, timestamps @/path/to/video.mp4" 2>/dev/null

# Audio
gemini -p "Transcribe and summarize: decisions, action items @/path/to/audio.mp3" 2>/dev/null

# Image (diagrams, charts)
gemini -p "Analyze: components, relationships, data flow @/path/to/diagram.png" 2>/dev/null
```

| Target | Extensions |
|--------|------------|
| PDF | `.pdf` |
| Video | `.mp4`, `.mov`, `.avi`, `.mkv`, `.webm` |
| Audio | `.mp3`, `.wav`, `.m4a`, `.flac`, `.ogg` |
| Images (advanced analysis) | `.png`, `.jpg`, `.jpeg`, `.gif`, `.webp`, `.svg` |

> Simple screenshot inspection can be done directly with Claude's Read tool.

## Auto-Trigger

When multimodal files appear in a task, automatically pass them to Gemini without waiting for user instructions.

## When NOT to Use Gemini

| Task | Correct Owner |
|------|---------------|
| Research and investigation | **general-purpose subagent** (Opus) |
| Codebase analysis | **general-purpose subagent** (Opus) |
| Design and planning | **Codex** |
| Debugging | **Codex** |
| Code implementation | **Claude / Subagents** |

## How to Use

### Subagent Pattern (for large outputs)

```
Task tool parameters:
- subagent_type: "gemini-explore"
- prompt: |
    {task description}

    gemini -p "{prompt} @/path/to/file" 2>/dev/null

    Return CONCISE summary (5-7 bullet points).
```

### Direct Call (for short extractions)

```bash
gemini -p "{what to extract} @/path/to/file" 2>/dev/null
```

## Language Protocol

1. Ask Gemini in **English**
2. Receive response in **English**
3. Report to user in **the user's language**

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/del-taiseiozaki) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
