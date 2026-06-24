---
name: doc-generator
description: Generates comprehensive documentation from Python source code. Analyzes modules, classes, and functions using AST parsing and AI interpretation, then outputs Sphinx RST or MkDocs Markdown docs. Identifies missing docstrings and generates them. Writes a DOCS_REPORT.md with coverage stats. Use when you need to document an existing Python project or fill in missing docstrings.
metadata:
  author: mk-knight23
---

# doc-generator

Python source → polished documentation. Nanobot reads your code and writes the docs.

## Usage
```
@Nanobot doc-generator --path src/ --format mkdocs
@Nanobot doc-generator --path src/ --format sphinx
@Nanobot doc-generator --path src/core/engine.py --fill-docstrings
@Nanobot doc-generator --path src/ --output docs/
```

## What It Does

1. **Parse**: AST-parses all `.py` files — extracts modules, classes, functions, type hints
2. **Audit**: Identifies missing docstrings, undocumented parameters, no return type annotations
3. **Generate**: Creates one doc file per module. AI writes docstrings for undocumented items if `--fill-docstrings`
4. **Structure**: Builds navigation tree (MkDocs: `mkdocs.yml` nav section; Sphinx: `index.rst` toctree)
5. **Report**: Writes `DOCS_REPORT.md` with coverage stats

## Files Created
```
docs/
  api/<module_name>.md          # (MkDocs format) or .rst (Sphinx)
  index.md                      # Home page with project overview
DOCS_REPORT.md                  # Docstring coverage %, missing items list
mkdocs.yml                      # (if --format mkdocs and not present)
```

## DOCS_REPORT.md Contents
- Overall docstring coverage: XX%
- Modules with 0% coverage (list)
- Functions with missing return-type annotations
- Parameters with no type hints

## Formats

| Format | Files | Preview |
|--------|-------|---------|
| MkDocs | `.md` files + `mkdocs.yml` | `mkdocs serve` |
| Sphinx | `.rst` files + `conf.py` | `make html` |

## Fill Docstrings Mode
With `--fill-docstrings`, Nanobot reads the function implementation and writes a docstring inline — it modifies your source files. Review with `git diff` before committing.

## Philosophy
Documentation generated from code is always in danger of drifting. The report's coverage stats are designed to be run in CI — fail the build when docstring coverage drops below a threshold.

---
> Source: [mk-knight23/AI-Agent-Nanobot](https://github.com/mk-knight23/AI-Agent-Nanobot) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
