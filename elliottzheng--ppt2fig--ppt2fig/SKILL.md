---
name: ppt2fig-export
description: Export specific pages from PPT, PPTX, or ODP files to cropped PDF via ppt2fig. Use when the user wants to convert presentation slides into paper-ready PDF figures, specify a file path and page range, compare LibreOffice/WPS/PowerPoint export backends, or rerun a saved export workflow. Prefer this skill when `ppt2fig`, `ppt2fig-cli`, or this repo's `dist/ppt2fig-cli.exe` is available. Use when this capability is needed.
metadata:
  author: elliottzheng
---

Use `ppt2fig` to export slide pages to PDF.

Workflow:

1. Confirm the source presentation path exists.
2. Decide the page selection string.
   Examples: `1`, `2`, `1,3,5-7`
3. Decide the backend.
   Defaults:
   - Use `auto` by default.
   - Let the program choose the best available backend unless the user explicitly asks for a specific one.
   - Only force `libreoffice`, `wps`, or `powerpoint` when the user has a clear backend preference or needs a comparison.
4. Decide whether to crop.
   - Use default cropping for paper figures.
   - Use `--no-crop` only when the user explicitly wants original margins.
5. Run the command.
6. Return the output PDF path and any backend-specific notes.

Preferred commands:

```bash
ppt2fig "/abs/path/input.pptx" --pages 2 -o "/abs/path/output.pdf"
ppt2fig "/abs/path/input.pptx" --pages 1,3,5-7
ppt2fig "/abs/path/input.pptx" --pages 2 --backend wps
ppt2fig "/abs/path/input.pptx" --pages 2 --backend powerpoint --powerpoint-intent print
```

If `ppt2fig` is not on `PATH`, try:

```bash
ppt2fig-cli "/abs/path/input.pptx" --pages 2 -o "/abs/path/output.pdf"
```

If the binary is installed by OpenClaw or ClawHub, the helper script will first look in `~/.openclaw/tools/ppt2fig-export/` for the downloaded GitHub Release executable. If that is not present, it falls back to `PATH`, then to `python -m ppt2fig`:

```bash
python "{baseDir}/scripts/run_ppt2fig.py" "/abs/path/input.pptx" --pages 2 -o "/abs/path/output.pdf"
```

Behavior notes:

- `auto` backend priority is `LibreOffice > WPS > PowerPoint`.
- `--list-backends` prints what the current machine can use.
- Page numbers are 1-based.
- Output defaults to `<input>.pages_<page-spec>.pdf` when `-o` is omitted.
- If the user does not explicitly request a backend, keep the default `auto`.
- PowerPoint image quality is limited by Microsoft's export API; `--powerpoint-intent print` only matters when the user explicitly chooses the PowerPoint backend.

Useful checks:

```bash
ppt2fig --list-backends
ppt2fig "/abs/path/input.pptx" --pages 999
```

Use the second command only when you need to validate page count or explain an out-of-range error.

---
> Source: [elliottzheng/ppt2fig](https://github.com/elliottzheng/ppt2fig) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-24 -->
