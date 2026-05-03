---
name: latex-book-authoring
description: LaTeX book authoring with memoir class, custom environments, and proper compilation workflow Use when this capability is needed.
metadata:
  author: tuliosoria
---

# LaTeX Book Authoring

This skill covers LaTeX book authoring using the `memoir` document class with custom environments for business/technical books.

## Document Class

All books use `memoir` for superior typography:
- Custom chapter styles (`madsen`, `veelo`)
- Epigraphs, flexible layouts
- Professional publishing quality

## Custom Environments

### Insight/Callout Boxes (tcolorbox-based)

```latex
% Blue insight box
\begin{keyinsight}[Optional Title]
Important takeaway.
\end{keyinsight}

% Red warning box  
\begin{warning}[Caution]
Risk or pitfall to avoid.
\end{warning}

% Green practical box
\begin{practicalbox}[Action Step]
What to do next.
\end{practicalbox}

% Orange tip box
\begin{tip}[Pro Tip]
Helpful suggestion.
\end{tip}
```

**⚠️ Use square brackets `[]` for optional titles, NOT curly braces `{}`**

### Other Environments

```latex
\begin{promptexample}[Title]
Prompt template text...
\end{promptexample}

\begin{casestudy}{Title}  % Note: curly braces here
Case study content.
\end{casestudy}

\begin{framework}[Decision Framework]
Framework content.
\end{framework}

\begin{roicalc}[ROI Calculation]
ROI formulas.
\end{roicalc}
```

## Chapter Structure Template

```latex
\chapter{Chapter Title}

\epigraph{Relevant quote here.}{Attribution}

\section{Opening Hook}
% Engage the reader immediately - 1 page

\section{Context}
% Why this matters NOW - 2-3 pages

\section{Core Framework}
% Main mental model - 3-5 pages

\begin{keyinsight}[Key Takeaway]
Most important lesson from this section.
\end{keyinsight}

\section{Application}
% Real-world examples - 3-5 pages

\section{Pitfalls}
% What goes wrong - 1-2 pages

\begin{warning}[Common Mistake]
Risk to avoid.
\end{warning}

\section{Summary}
% Brief recap

\begin{practicaltip}[Try This Today]
Concrete action for the reader.
\end{practicaltip}
```

## Compilation Workflow

**Always run pdflatex TWICE** - first pass generates aux files, second resolves cross-references.

```bash
# Full compile with TOC
cd book-directory && pdflatex main.tex && pdflatex main.tex

# Quick error check
cd book-directory && pdflatex -interaction=nonstopmode main.tex 2>&1 | grep -E "^!|Output written"
```

### Error Detection
- Lines starting with `!` are fatal errors - must fix
- Warnings are usually okay
- "Output written on main.pdf" = success

## File Naming Conventions

- Chapter files: `ch##-descriptive-name.tex` (e.g., `ch03-ai-fundamentals.tex`)
- Use hyphens, not underscores
- Keep names lowercase

## Adding a New Chapter

1. Create `chapters/ch##-name.tex` using chapter template
2. Add `\include{chapters/ch##-name}` to `main.tex` in correct position
3. Compile twice to update TOC
4. Verify no errors

## TikZ Diagrams

Define reusable diagrams in `figures/figures.tex`:

```latex
% In figures/figures.tex
\newcommand{\mydiagram}{
  \begin{tikzpicture}
    % diagram code
  \end{tikzpicture}
}

% In chapters
\begin{center}
\mydiagram
\end{center}
```

## Quality Checklist

- [ ] Compiles without `!` errors
- [ ] Ran pdflatex twice for cross-references
- [ ] Optional arguments use `[]` not `{}`
- [ ] Chapter follows structure template
- [ ] Key insights in callout boxes

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tuliosoria) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
