---
name: tex-environment-check
description: Verify and document local TeX/LaTeX installations (especially BasicTeX on macOS) with smoke tests, package checks, and reproducibility notes. Use when you need to confirm whether LaTeX is available, diagnose engine-specific issues (for example LuaLaTeX cache paths), or generate a Markdown report for project setup docs. Use when this capability is needed.
metadata:
  author: arivero
---

# TeX Environment Check

Run a deterministic audit and generate a report.

## Workflow

1. Run the checker script:

```bash
python3.12 skills/tex-environment-check/scripts/check_tex_env.py docs/tex-env-report.md
```

2. Review key sections in the report:
- Binary paths and versions.
- TeX Live root (`basic` vs full install).
- Package availability.
- Smoke test results for `pdflatex`, `xelatex`, and `lualatex`.

3. If LuaLaTeX fails with cache errors, rerun builds with a writable cache:

```bash
TEXMFVAR="$PWD/.texmf-var" lualatex -interaction=nonstopmode -halt-on-error file.tex
```

4. Keep the report in version control when reproducibility matters.

## Notes

- Treat `pdflatex` + `xelatex` success as baseline viability for this repo.
- Treat missing packages as install-time requirements and record them in project docs.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/arivero) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
