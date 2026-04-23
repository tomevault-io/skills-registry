---
name: docling
description: Expert guidance on document processing with Docling and audio transcription with Whisper. Use when this capability is needed.
metadata:
  author: fullfran
---

# Docling & Ingestion Expert Skill

This skill provides patterns for processing diverse document formats and audio files.

## 📝 Document Processing

- **Multi-format support**: Use `docling` for converting PDF, DOCX, PPTX, XLSX, HTML, and Markdown to a clean Markdown representation.
- **HybridChunker**: Always use Docling's `HybridChunker` to preserve document structure (headings, lists, tables).
- **Token Awareness**: Configure chunker with `max_tokens=512` to align with embedding models (e.g., `text-embedding-3-small`).

## 🔊 Audio Transcription

- **Whisper ASR**: Use `docling`'s integration with Whisper Turbo for transcribing audio files (`.mp3`, `.wav`, `.m4a`, `.flac`).
- **Path Handling**: Pass `pathlib.Path` objects to `DocumentConverter` for audio files.

## 🏗️ Pipeline Architecture

- **Ingestion Pipeline**: Centralize logic in `src/ingestion/ingest.py`.
- **Metadata Extraction**: Extract YAML frontmatter and standard document properties.
- **Batching**: Use batch embedding generation and `insert_many` for chunks to optimize performance.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fullfran) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
