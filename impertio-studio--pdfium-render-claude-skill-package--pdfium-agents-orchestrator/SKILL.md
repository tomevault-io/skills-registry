---
name: pdfium-agents-orchestrator
description: > Use when this capability is needed.
metadata:
  author: Impertio-Studio
---

# pdfium-agents-orchestrator

This is the routing skill for the pdfium-render skill package. It does not teach
a single API area; it maps a PDF goal onto the other 25 skills and the order to
apply them. Use it first, then follow it into the specific skills it names.

Default API surface for the whole package: pdfium-render 0.9.x. The 0.8.x line
is covered as version notes inside each skill.

## The canonical pipeline

Every pdfium-render program follows the same five stages. ALWAYS run them in
this order.

```
1. BIND      load the PDFium library once
2. LOAD      open or create a PdfDocument
3. PAGES     reach the page collection
4. PROCESS   render / extract / edit / fill / merge / inspect
5. OUTPUT    save the document or export an image
```

| Stage | Owning skill | Anchor API |
|-------|--------------|------------|
| 1 BIND | `pdfium-core-bindings-setup` | `Pdfium::default()`, `bind_to_library` |
| 2 LOAD | `pdfium-syntax-document-loading` | `load_pdf_from_file`, `create_new_pdf` |
| 3 PAGES | `pdfium-syntax-pages` | `document.pages()`, `pages_mut()` |
| 4 PROCESS | depends on goal (see decision tree) | varies |
| 5 OUTPUT | `pdfium-impl-saving` or `pdfium-impl-output-formats` | `save_to_file`, `as_image` |

Stage 4 is the only branching stage. Stages 1, 2, 3, and 5 are the same shape
for every task.

## Goal-to-skill decision tree

```
What is the PDF goal?
|
+-- Render a page to a PNG or JPEG image?
|     LOAD -> pdfium-syntax-pages -> pdfium-syntax-rendering
|          -> pdfium-impl-output-formats
|
+-- Extract the text of a page or document?
|     LOAD -> pdfium-syntax-pages -> pdfium-syntax-text
|
+-- Search for a phrase inside a PDF?
|     LOAD -> pdfium-syntax-pages -> pdfium-syntax-text (search)
|
+-- Inspect what objects a page contains?
|     LOAD -> pdfium-syntax-pages -> pdfium-syntax-page-objects
|
+-- Add or edit text/image/shape content on a page?
|     LOAD -> pdfium-impl-page-objects-edit -> pdfium-impl-saving
|
+-- Fill in a PDF form?
|     LOAD -> pdfium-impl-form-fields -> pdfium-impl-saving
|
+-- Add a comment, highlight, stamp, or link annotation?
|     LOAD -> pdfium-impl-annotations -> pdfium-impl-saving
|
+-- Add, delete, rotate, or crop whole pages?
|     LOAD -> pdfium-impl-page-manipulation -> pdfium-impl-saving
|
+-- Merge, split, extract, or reorder pages across files?
|     LOAD -> pdfium-impl-multi-page -> pdfium-impl-saving
|
+-- Create a new PDF from scratch?
|     pdfium-syntax-document-loading (create_new_pdf)
|          -> pdfium-impl-page-manipulation -> pdfium-impl-page-objects-edit
|          -> pdfium-impl-saving
|
+-- Embed or inspect fonts?
|     LOAD -> pdfium-impl-fonts
|
+-- Read bookmarks, links, or the table of contents?
|     LOAD -> pdfium-impl-navigation
|
+-- Read or change metadata, attachments, permissions, signatures?
|     LOAD -> pdfium-impl-document-features -> pdfium-impl-saving
|
+-- Run pdfium-render in a browser?
|     pdfium-impl-wasm (changes BIND and LOAD)
|
+-- The program is slow or processes many PDFs?
|     pdfium-impl-performance
|
+-- Call a raw FPDF_* function the wrapper does not expose?
|     pdfium-core-raw-ffi
|
+-- The library will not load or crashes at bind time?
|     pdfium-errors-binding
|
+-- A runtime error, panic, or wrong colors?
      pdfium-errors-runtime
```

## The 26-skill map

### core/ : cross-cutting concerns

| Skill | When to reach for it |
|-------|----------------------|
| `pdfium-core-architecture` | understand the ownership tree and runtime model |
| `pdfium-core-bindings-setup` | STAGE 1: bind functions, binaries, feature flags |
| `pdfium-core-coordinates` | `PdfPoints` vs `Pixels`, `PdfRect` vs `PdfQuadPoints` |
| `pdfium-core-memory` | drop order, lifetimes, self-referential struct trap |
| `pdfium-core-raw-ffi` | escape hatch: raw `FPDF_*` calls |

### syntax/ : API syntax and reading

| Skill | When to reach for it |
|-------|----------------------|
| `pdfium-syntax-document-loading` | STAGE 2: open or create a document |
| `pdfium-syntax-pages` | STAGE 3: reach and iterate pages |
| `pdfium-syntax-rendering` | STAGE 4: render a page to a `PdfBitmap` |
| `pdfium-syntax-text` | STAGE 4: extract and search text |
| `pdfium-syntax-page-objects` | STAGE 4: read and inspect page objects |

### impl/ : development workflows

| Skill | When to reach for it |
|-------|----------------------|
| `pdfium-impl-page-objects-edit` | STAGE 4: create and transform page objects |
| `pdfium-impl-output-formats` | STAGE 5: bitmap to PNG, JPEG, raw bytes |
| `pdfium-impl-annotations` | STAGE 4: read, modify, create annotations |
| `pdfium-impl-form-fields` | STAGE 4: fill form fields |
| `pdfium-impl-page-manipulation` | STAGE 4: add, delete, rotate, crop pages |
| `pdfium-impl-multi-page` | STAGE 4: merge, split, reorder across documents |
| `pdfium-impl-saving` | STAGE 5: save a document to file, writer, bytes |
| `pdfium-impl-fonts` | STAGE 4: embed and inspect fonts |
| `pdfium-impl-navigation` | STAGE 4: bookmarks, links, destinations |
| `pdfium-impl-document-features` | STAGE 4: metadata, attachments, permissions |
| `pdfium-impl-wasm` | browser target: changes binding and loading |
| `pdfium-impl-performance` | scaling: bind once, threading, render cost |

### errors/ : diagnosis

| Skill | When to reach for it |
|-------|----------------------|
| `pdfium-errors-binding` | STAGE 1 failed: `LoadLibraryError` diagnosis |
| `pdfium-errors-runtime` | a `PdfiumError` at run time, wrong byte order |

### agents/ : orchestration

| Skill | When to reach for it |
|-------|----------------------|
| `pdfium-agents-orchestrator` | this skill: route a goal to the right skills |
| `pdfium-agents-validator` | review generated pdfium-render code for correctness |

## Cross-skill consistency rules

These rules hold across every skill. Enforce them when composing a pipeline.

- ALWAYS target one version line. Default to 0.9.x. NEVER mix a removed 0.8.x
  name (`PdfBitmapConfig`, `get_bitmap`, `as_bytes`, `set_matrix`,
  `load_pdf_from_bytes`, `get_bindings`, `PdfBitmapRotation`, direct `PdfFont`
  constructors, `copy_onto_new_page_*`) with the 0.9.x API in one program.
- ALWAYS bind PDFium once. In a loop, a server, or a batch job, reuse one
  `Pdfium` instance. See `pdfium-impl-performance`.
- ALWAYS respect the ownership tree: a `PdfPage` cannot outlive its
  `PdfDocument`, which cannot outlive its `Pdfium`. See `pdfium-core-memory`.
- ALWAYS save after any edit. Render, extraction, and inspection produce output
  directly; create, edit, fill, annotate, and merge all need an explicit
  `pdfium-impl-saving` step or the changes are lost.
- ALWAYS keep coordinate units straight: page geometry is `PdfPoints`, bitmap
  geometry is `Pixels`. See `pdfium-core-coordinates`.
- After generating a pdfium-render program, route through
  `pdfium-agents-validator` to check it against the removed-name and
  consistency checklist.

## Pipeline patterns

The verified end-to-end code for each pattern is in `references/examples.md`.

- Render to image: bind, load, iterate pages, `render_with_config`, `as_image`,
  save the image.
- Extract text: bind, load, iterate pages, `text()`, `all()`.
- Edit and save: bind, load, edit via the page-objects or annotations skill,
  `save_to_file`.
- Merge documents: bind, `create_new_pdf`, `append` each source, `save_to_file`.

## Critical rules

- ALWAYS start a pdfium-render task here, then follow the decision tree into
  the specific skill.
- ALWAYS run the five pipeline stages in order: bind, load, pages, process,
  output.
- NEVER reach for `pdfium-core-raw-ffi` when a high-level skill covers the goal.
  Raw FFI is the escape hatch, not the default.
- NEVER skip the output stage after an editing process.
- For a browser target, route through `pdfium-impl-wasm` first; it changes the
  bind and load stages.

## Companion skills

This skill maps all 25 other skills in the package. It is the entry point;
every other skill is a companion. `pdfium-agents-validator` is its review
counterpart.

## Reference files

- `references/methods.md` : the API-anchor to skill-owner map.
- `references/examples.md` : verified end-to-end pipeline examples.
- `references/anti-patterns.md` : orchestration mistakes and the fix.

---
> Source: [Impertio-Studio/pdfium-render-Claude-Skill-Package](https://github.com/Impertio-Studio/pdfium-render-Claude-Skill-Package) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
