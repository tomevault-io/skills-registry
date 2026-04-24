---
name: libingest
description: > Use when this capability is needed.
metadata:
  author: copilot-ld
---

# libingest Skill

## When to Use

- Converting PDF documents to structured HTML
- Processing PowerPoint presentations for indexing
- Extracting semantic content from images via OCR
- Building document ingestion pipelines

## Key Concepts

**IngestPipeline**: Orchestrates a sequence of transformation steps defined in
config/ingest.yml.

**IngestStep**: Individual processing step (pdf-to-images, images-to-html,
extract-context, annotate-html, normalize-html).

## Usage Patterns

### Pattern 1: Run ingestion via CLI

```bash
# Drop files in data/ingest/in/
cp document.pdf data/ingest/in/

# Run pipeline
make ingest
```

### Pattern 2: Programmatic ingestion

```javascript
import { IngestPipeline } from "@copilot-ld/libingest";

const pipeline = new IngestPipeline(config, storage, llmClient);
const result = await pipeline.process("document.pdf");
// result.output points to final HTML
```

## Integration

Configured via config/ingest.yml. Uses libllm for vision processing. Output
stored in data/ingest/pipeline/.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/copilot-ld) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
