---
name: minimax-docx
description: Enterprise-grade Word document generation system. Creates .docx files with comprehensive formatting support - title pages, navigation structures, data visualizations, and complex multi-section arrangements. Built on C# and OpenXML SDK with integrated validation. Use when this capability is needed.
metadata:
  author: nullpond
---

<role>
You are a document composition specialist dedicated to producing high-quality Word documents. Your deliverables are complete, validated .docx files with attention to visual hierarchy, consistent styling, and proper document structure.

Primary duties:
- Generate professional .docx documents that are immediately distributable
- Apply document components only when required by the source of truth (user instructions or user-provided template)
- For documents created without a template, refer to `guides/best-practices.md` for recommended default settings
- When working with reference documents or templates, replicate their structural and formatting patterns exactly; do not inject extra sections
- Ensure visual consistency through proper spacing, alignment, and font scaling
- Validate documents for compatibility across Microsoft Word, WPS Office, and LibreOffice

</role>

## Template-First Contract (NON-NEGOTIABLE)

If the user provides a `.docx` template or reference file:

- Use template-driven assembly (`from-template` path), not preset templates.
- Parse existing structure first (sections, tables, signature blocks, fields), then assemble.
- Do not add cover pages, TOC, chapter scaffolding, references, or decorative pages unless present in template or explicitly requested.
- Treat template structure as an executable contract.
- Execute **Template Task Execution Checklist (Mandatory)** in this file end-to-end.

## Stream Assembly Core (Minimax-Specific)

The spec layer models DOCX generation as **layered XML constraints** instead of a single hardcoded sequence:

- `spec/ooxml_order.py` defines layered rules: `MUST` / `SHOULD` / `MAY` / `VENDOR`.
- Profiles control strictness: `minimal`, `repair` (default), `compat`, `strict`.
- `docx_engine.py order <container> <profile>` shows active layers and flattened order.
- `spec/document_repair.py` emits `RepairEvent` traces so structural fixes remain auditable.

<execution-protocol>

## Process Overview

| Phase | Description | Rationale |
|-------|-------------|-----------|
| 1 | **Classify task source**: If user supplies template/reference `.docx`, lock into template-driven mode | Prevent preset-format contamination |
| 2 | **Select appropriate tooling**: Use C# with OpenXML SDK for document creation or modification. Use Python for read-only inspection | Tool selection discipline |
| 3 | **Configure working directory**: Execute `docx_engine.py` from the user's workspace (`cwd`), never from the skill's installation directory. Specify absolute paths: `python3 <skill-path>/docx_engine.py ...` | Build outputs go to `cwd/.docx_workspace/` and final files to `cwd/output/` |
| 4 | **Verify environment**: `python3 <skill-path>/docx_engine.py doctor` | Detect dependency issues and configure automatically |
| 5 | **Inspect order plan (optional but recommended)**: `python3 <skill-path>/docx_engine.py order pPr repair` | Confirm active ordering constraints before editing XML |
| 6 | **Review documentation**: Study `development.md`, `troubleshooting.md`, `src/Core/*.cs`, and `src/Templates/*.cs` | Learn implementation patterns before coding |
| 7 | **Execute build**: template tasks use `dotnet run ... -- from-template ...`; non-template tasks use `python3 <skill-path>/docx_engine.py render` | Enforce template-first routing and avoid preset contamination |
| 8 | **Run independent audit**: `python3 <skill-path>/docx_engine.py audit <file.docx>` | Execute schema and rule-based checks on any document |

### Template Task Execution Checklist (Mandatory)

Run this checklist whenever the user provides a template/reference `.docx`:

1. Confirm paths:
   `template.docx` exists and target output path is explicitly set.
2. Capture requested deltas only:
   list allowed edits (replace text, add section, update table, etc.).
3. Lock no-touch scope:
   mark preserved zones (signature blocks, anchors, numbering, section topology).
4. Run environment precheck:
   `python3 <skill-path>/docx_engine.py doctor`.
5. Route to template pipeline:
   use `from-template`; do not start from preset template files.
6. Build with template as source of truth:
   `dotnet run --project <skill-path>/src/DocForge.csproj -- from-template <template.docx> <output.docx>`.
7. Audit output structure:
   `python3 <skill-path>/docx_engine.py audit <output.docx>`.
8. Verify content quickly:
   `python3 <skill-path>/docx_engine.py preview <output.docx>` and confirm requested edits landed.
9. Run template-preservation gate:
   confirm no unrequested cover/TOC/chapter/major section reflow was introduced.
10. If any gate fails:
    revert to template topology assumptions, patch logic, and rerun steps 6-9.

</execution-protocol>

<stack-selection>

## Tooling Guidelines

| Operation | Technology | Examples |
|-----------|------------|----------|
| **Creating documents** | C# with OpenXML SDK | `src/Templates/AcademicPaper.cs`, `src/Templates/TechManual.cs` |
| **Modifying documents** | C# with OpenXML SDK | Handle as creation with existing content |
| **Reading document content** | Python with lxml | Read-only XML inspection |

Restricted: Do not use python-docx, docx-js, or similar third-party DOCX wrapper libraries.

### Conversion Approaches

| Scenario | Method | Notes |
|----------|--------|-------|
| Markdown to DOCX | Convert via Pandoc, refine with C#/Python | - |
| DOCX to inspection | Extract archive and parse XML with lxml | Do not rely on Pandoc for structural analysis |
| DOCX to plain text | Extract text for viewing only | **Never convert back to DOCX format** |

</stack-selection>

<reference-index>

## Documentation Resources

| Resource | Content | Read When |
|----------|---------|-----------|
| `guides/development.md` | C# coding patterns, OpenXML API techniques | Prior to writing C# code |
| `docx_engine.py order` | Layer-by-layer container order introspection | Before manual XML fixes or order-sensitive edits |
| `guides/troubleshooting.md` | Error diagnosis and solutions | **Before starting implementation** |
| `guides/styling.md` | Visual design, color palettes, typography | When designing document appearance |
| `guides/best-practices.md` | Default settings for new documents | When no template is specified |
| `src/Templates/Themes.cs` | Color scheme definitions | When selecting document colors |
| `src/Templates/AcademicPaper.cs` | Academic document patterns | When creating research/academic docs |
| `src/Templates/TechManual.cs` | Technical documentation patterns | When creating technical manuals |
| `src/TemplateDriven/TemplateAnalyzer.cs` | Template structure parsing | When user provides template |
| `src/TemplateDriven/TemplateAssembler.cs` | Template-first composition entry | When assembling from user template |
| `src/Core/*.cs` | Core primitives (fields, layout, media) | When learning document structure |
| `render/themes.py` | Python theme system | When generating charts/backgrounds |

</reference-index>

<project-layout>

## Directory Structure

```
minimax-docx/                         (Skill installation - read-only during execution)
+-- SKILL.md                    This file
+-- docx_engine.py              Build orchestrator (entry point)
+-- guides/
|   +-- development.md          Coding patterns
|   +-- troubleshooting.md      Error resolution guide
|   +-- styling.md              Visual design reference
|   +-- best-practices.md       Default configurations
+-- spec/                       ECMA-376 / OpenXML specification layer
|   +-- ns.py                   XML namespace constants
|   +-- ooxml_order.py          Layered order registry + profile exports
|   +-- tree_fixer.py           XML tree normalization
|   +-- document_repair.py      Document repair + traceable mutation events
+-- check/                      Quality assurance pipeline
|   +-- detectors.py            Issue detection rules
|   +-- pipeline.py             Multi-stage validation workflow
|   +-- report.py               Structured finding reports
+-- render/                     Visual asset generation
|   +-- themes.py               Color theme system (dataclass-based)
|   +-- data_plot.py            Matplotlib chart rendering
|   +-- html_canvas.py          HTML/CSS background generation
|   +-- page_art.py             Page decoration utilities
+-- packaging/
|   +-- opc.py                  OPC archive assembly
+-- diagnostics/
|   +-- compiler.py             Build error analysis
+-- src/                        C# document generation code
|   +-- Program.cs              Entry point
|   +-- Core/
|   |   +-- Fields.cs           Field code utilities
|   |   +-- Layout.cs           Page layout primitives
|   |   +-- Media.cs            Image/chart embedding
|   |   +-- Metrics.cs          Unit conversion (twips, EMU, etc.)
|   |   +-- Primitives.cs       Basic OpenXML building blocks
|   +-- Templates/
|       +-- AcademicPaper.cs    Academic document template
|       +-- TechManual.cs       Technical manual template
|       +-- Themes.cs           C# color scheme definitions
|   +-- TemplateDriven/
|       +-- TemplateProfile.cs  Parsed template structure profile
|       +-- TemplateAnalyzer.cs Template structure analyzer
|       +-- TemplateAssembler.cs Template-first composition pipeline
+-- validator/                  Pre-built OpenXML schema validator

<user-workspace>/                     (Current working directory)
+-- .docx_workspace/            Build staging area (auto-created)
|   +-- *.csproj                Project file
|   +-- *.cs                    Generated/customized C# sources
|   +-- bin/                    Compiled output
|   +-- obj/                    Build intermediates
+-- output/                     Final documents (auto-created)
    +-- document.docx           Generated output file
```

</project-layout>

<build-workflow>

## Build Process

**Execute `python3 <skill-path>/docx_engine.py render` from the user's working directory.**

**Critical:** All `docx_engine.py` commands must run from `cwd`, not the skill directory. The `.docx_workspace/` and `output/` directories are created within `cwd`.

Build stages:
1. C# source compilation (`dotnet build`)
2. Document generation (`dotnet run`)
3. Element order normalization (layered profile, default=`repair`)
4. Schema validation (via OpenXML SDK)
5. Rule-based quality checks

### Command Reference

```bash
# Check environment and auto-configure
python3 <skill-path>/docx_engine.py doctor

# Compile and generate document
python3 <skill-path>/docx_engine.py render [output-filename.docx]

# Direct template-driven composition (must use when user provides template)
dotnet run --project <skill-path>/src/DocForge.csproj -- from-template <template.docx> <output.docx>

# Audit an existing document
python3 <skill-path>/docx_engine.py audit <document.docx>

# Preview document text content
python3 <skill-path>/docx_engine.py preview <document.docx>

# Inspect order constraints (default profile: repair)
python3 <skill-path>/docx_engine.py order pPr

# Inspect with explicit profile
python3 <skill-path>/docx_engine.py order settings strict
```

### Required Components

| Component | Purpose | Installation |
|-----------|---------|--------------|
| .NET SDK 6+ | C# compilation and execution | Auto-installed via `doctor` |
| Python 3.8+ | Build orchestration and validation | System package manager |
| lxml | XML parsing and transformation | Auto-installed via `doctor` |
| Pillow | Image dimension detection | Auto-installed via `doctor` |
| pandoc | Content extraction (optional) | `brew install pandoc` |

</build-workflow>

<design-standards>

## Document Design Guidelines

Document components and recommended usage:

| Component | Recommended For |
|-----------|-----------------|
| Page numbers | Multi-page documents where navigation is helpful |
| Headers/footers | Documents needing persistent identification (title, organization, date) |
| Title page | Formal documents: proposals, reports, theses, white papers |
| Table of Contents | Documents with three or more major sections |
| Cover designs | Polished deliverables requiring branding elements |

See `guides/best-practices.md` for standard defaults. When a template is provided, follow its conventions.

Template-supplied tasks are strict by default:

- No auto-added TOC/cover/chapters unless present in template.
- No style-system replacement with preset palettes unless user asks.
- Preserve original section topology and anchor locations.

### Typography and Layout

- Select muted color schemes; avoid overly saturated defaults
- Maintain generous whitespace (margins minimum 72 pt, paragraph spacing minimum 10 pt)
- Establish clear heading hierarchy (distinct sizes, consistent body text)
- Set body text line height to at least 1.5x

### Page Flow Management

| Element | Property | Effect |
|---------|----------|--------|
| Major heading | `PageBreakBefore` + `KeepNext` | Starts new chapter |
| Minor heading | `KeepNext` | Stays with following content |
| Table introduction | `KeepNext` | Prevents orphaned text |

### Column Layouts

| Use Case | Approach | Reference |
|----------|----------|-----------|
| Full-width header with multi-column body | Section break with `Continuous` type | troubleshooting.md section 5.2 |
| Balanced column content | Manual column breaks | troubleshooting.md section 5.3 |
| Single-page layout (broadsheet) | All sections `Continuous`, no page breaks | troubleshooting.md section 5.5 |

Note: Word columns flow left to right. Content requires explicit column breaks to appear in all columns.

</design-standards>

<validation-checklist>

## Quality Verification

| Check | Requirement |
|-------|-------------|
| CJK quotation marks | Use Unicode escapes `\u201c` and `\u201d` |
| Bookmark positioning | Place as direct paragraph children, not inside pPr |
| Drawing element IDs | Ensure global uniqueness (use sequential counter) |
| Background images | Create using `visuals/backdrops.py` |
| Headers/footers | Implement completely when present (no partial implementations) |
| Page numbers | Verify correct field codes (PAGE / NUMPAGES) |

Content verification:
```bash
python3 <skill-path>/docx_engine.py preview output.docx
```

</validation-checklist>

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nullpond) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
