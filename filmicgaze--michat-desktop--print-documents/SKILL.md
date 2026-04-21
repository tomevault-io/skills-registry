---
name: print-documents
description: Create a clean, well-formatted printable single-file HTML document under the working folder and open it for print preview. Use when this capability is needed.
metadata:
  author: filmicgaze
---

## When to use this skill

Use this when the user asks for a “printable” or wants a document formatted nicely for printing (handouts, letters, notes, one-page briefs). The output is a single self-contained `.html` file saved under the working folder, then opened in the default browser for print preview.

This workflow is for formatting and presentation, not fine-grained editing. Prefer whole-file rewrites rather than incremental patching.

Seperately, MiChat also allows for utilitarian text printing from the scratchpad. The current workflow is for well-formatted, attractive print documents.

## Tools this skill expects

- **Documents** toolset: to read source text (optional) and to write the final `.html` file under `workspace_root`.
- **Open file** toolset: to open the generated `.html` file in the OS default browser.

If `workspace_root` is unset or invalid, documents/opening will be unavailable; help the user set the Working folder in Settings → Paths.

## Default output convention

By default, write to a subfolder under the working folder:

- `printables/` (create if missing)
- filename: short, safe, descriptive (e.g. `meeting-notes.html`, `lesson-outline.html`)
- if overwriting would be surprising, version it (e.g. `meeting-notes_v2.html`)

Always report the output path to the user.

## High-level workflow

1. **Decide the source**
  

- If the user provided text in chat: use it as the source.
  
- If the source is a local document: use documents tools to read enough of it to format correctly.
  
- If it’s Markdown: convert it to HTML in a **best-effort** way.
  
  - Supported (best-effort): headings, paragraphs, emphasis/strong, ordered/unordered lists, blockquotes, horizontal rules, code blocks/inline code, and simple tables.
  - If there are unsupported/ambiguous features, prefer a clean fallback (plain text or simplified structure) rather than complex rendering.

2. **Confirm the print intent (lightly)**
  If anything is unclear, ask one short question (e.g. “A4 or US Letter?” or “Serif or sans?”). Otherwise proceed with defaults.

Defaults if the user doesn’t specify:

- Paper: **A4**, portrait
  
- Typeface: **system sans**
  

3. **Generate single-file HTML**
  

- Inline CSS in a `<style>` block.
  
- No external assets by default (no remote fonts, no external images, no scripts).
  
- Body content should be clean, semantic HTML: headings, paragraphs, lists.
  

4. **Write the file**
  Use `docs_write` to write the full HTML file to the chosen path. Prefer `docs_write` over append/section tools for HTML.
  
5. **Open for preview**
  Call `open_file` on the generated file so the user can print from the browser.
  

## House style (portable, print-first)

### Page + typography defaults

- Page: **A4 portrait** by default.
- Font: system sans stack (portable), 12pt.
- Line height: 1.35.
- Paragraph spacing: ~0.75 line.
- Left aligned.
- No hyphenation.
- Color: default to **black text on white background** (print-friendly) unless the user asks for color.
- Avoid borders, heavy backgrounds, and decorative effects unless asked.

### Tables

- Prefer readable borders and cell padding.
- Allow wrapping in table cells; avoid forcing fixed widths that can overflow the page.
- If a table is too wide, prefer wrapping and slightly smaller font over horizontal scrolling.

### Browser/print dialog reality

CSS can suggest page size/margins, but the print dialog is still authoritative. If the user’s printout looks wrong, suggest: correct paper size, scale 100%, and disabling headers/footers if the browser provides those options.

## Template (recommended structure)

Produce a full HTML document with:

- `<meta charset="utf-8">`
- `<meta name="viewport" content="width=device-width, initial-scale=1">`
- Inline CSS
- A screen-only wrapper `.page` that previews an A4 sheet
- Print styles that remove screen wrapper styling

A good default CSS approach is:

- Use `@page` for size/margins (with sensible fallback if the browser ignores it).
- Use `.page` to simulate paper on screen (fixed mm dimensions) but do not force that in print.

## Tweak requests (natural language)

If the user asks, support small adjustments like:

- “Make it A5” / “Landscape”
- “Tighter margins” / “Roomier margins”
- “Bigger headings” / “Smaller body text”
- “Serif headings only” / “Serif everything”
- “Use this font: …” (best effort; fall back to system fonts if unavailable)

Do not mention web fonts by default. If the user explicitly asks for a specific web font, explain it may depend on internet access and printing environment.

## Safety + robustness rules

- Don’t include JavaScript unless the user explicitly asks.
- Keep everything in one file: HTML + CSS inline.
- Avoid relying on fragile print-only CSS features (page counters, running headers) unless requested, and note they can be browser-dependent.
- Prefer rewriting the whole file for revisions. If the user wants edits, read the current file, update, and write a new full version.

## Troubleshooting

If `open_file` fails:

- Confirm the file is under the working folder.
- If it’s blocked by `open_file` rules, the allow/block list is configured in `toolsets/open_file/config.json`. The user may need to allow `.html` / `.htm` there.

If printing looks wrong:

- Ask which browser they used.
- Suggest checking paper size (A4 vs Letter), scale (100%), margins, and headers/footers settings in the print dialog.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/filmicgaze) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
