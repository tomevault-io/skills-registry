---
name: render-book
description: Render the book into HTML (gitbook) or PDF formats. Use when the user wants to "render the book", "build the book", "generate the book", or create HTML/PDF output. Use when this capability is needed.
metadata:
  author: dparedesi
---

# Rendering Book

Render the "Data Science with R" book into HTML or PDF formats using R's `bookdown` package.

**Why?** Automates the complex R commands required to build the book, ensuring consistent output for both web (HTML) and print (PDF) formats.

## Quick Start

1. **Check Dependencies**: Ensure R and `bookdown` are installed.
2.  **Pre-flight**: Check `_output.yml` for deprecated flags.
3. **Choose Format**: HTML (default) or PDF (slow, heavy build).
4. **Execute**: Run the appropriate command.
5.  **Verify**: Check logs and output files.

## Prerequisites

- **R Environment**: Must have R installed.
- **Packages**: `bookdown` package must be installed.
    ```r
    if (!require("bookdown")) install.packages("bookdown")
    ```

## Workflow Steps

### 0. Pre-flight Check (Optional)

Check `_output.yml` for deprecated Pandoc arguments.

```bash
grep "highlight-style" _output.yml
```

> [!TIP]
> If found, consider replacing with `--syntax-highlighting` to avoid warnings.

### 1. Render HTML (Gitbook)

This is the default and most common format.

```bash
Rscript -e "bookdown::render_book('index.Rmd', 'bookdown::gitbook')"
```

> [!TIP]
> The output will be generated in `docs/index.html`.

### 2. Render PDF

Use this for generating the print version.

> [!NOTE]
> PDF generation involves heavy LaTeX compilation and may download large assets. Expect this to take significantly longer than HTML.

```bash
Rscript -e "bookdown::render_book('index.Rmd', 'bookdown::pdf_book')"
```

> [!CAUTION]
> PDF generation requires a LaTeX installation (like TinyTeX). If it fails, try installing TinyTeX in R: `tinytex::install_tinytex()`.

### 3. Render All Formats

Generates all formats defined in `_output.yml`.

```bash
Rscript -e "bookdown::render_book('index.Rmd', 'all')"
```

### 4. Quality Check (Log Analysis)

After rendering, check for hidden warnings that don't fail the build:

```bash
grep -E "Warning|undefined|multiply-defined" Data-Science-with-R.log
```

## Verification

Verify the output exists and was recently modified (within last 5 minutes):

```bash
# Verify PDF
find docs/ -name "*.pdf" -mmin -5

# Verify HTML
find docs/ -name "index.html" -mmin -5
```

## Troubleshooting

| Problem | Cause | Solution |
|---------|-------|----------|
| `command not found: Rscript` | R is not in PATH | Install R or add it to your PATH. |
| `LaTeX failed to compile` | Missing LaTeX packages | Run `tinytex::install_tinytex()` in R. |
| `there is no package called 'bookdown'` | Missing R package | Run `install.packages("bookdown")` in R. |
| Slow PDF build/hangs | Downloading assets or large LaTeX compile | Wait; check network or compilation logs. |
| `Deprecated: ... highlight-style` | Old Pandoc args in `_output.yml` | Update `_output.yml` (see Pre-flight check). |

## Common Mistakes

1. **Running from wrong directory**: Always run from the book root (where `index.Rmd` lives).
   ```bash
   # Wrong - will fail
   cd docs && Rscript -e "bookdown::render_book(...)"

   # Correct
   Rscript -e "bookdown::render_book('index.Rmd', 'bookdown::gitbook')"
   ```

2. **Editing `docs/` directly**: Never edit files in `docs/`. They get overwritten on each render. Edit the `.Rmd` source files instead.

3. **Forgetting to save `.Rmd` files**: Ensure all changes are saved before rendering. Unsaved changes won't appear in output.

4. **Running PDF before HTML**: If you've never built the book, build HTML first to catch errors faster. PDF builds are slow and LaTeX errors are harder to debug.

## Sample Output

**Successful HTML build:**
```
Output created: docs/index.html
```

**Successful PDF build:**
```
Output created: docs/Data-Science-with-R.pdf
```

**Failed build (missing package):**
```
Error in library(ggplot2) : there is no package called 'ggplot2'
Calls: ... -> source -> withVisible -> eval -> eval -> library
Execution halted
```
> [!TIP]
> Package errors show which package is missing. Install it with `install.packages("package_name")`.

## Quality Rules

- **Do not use `_build.sh`**: The legacy script is deprecated.
- **Check `_output.yml`**: Ensure the configuration file exists before rendering.
- **Verify output before committing**: Always check that `docs/index.html` or `docs/*.pdf` was updated.
- **Run pre-flight check for new setups**: Check for deprecated Pandoc flags before first build.
- **Clean build for major changes**: If output looks wrong after structural changes, delete `docs/` and rebuild:
  ```bash
  rm -rf docs/* && Rscript -e "bookdown::render_book('index.Rmd', 'bookdown::gitbook')"
  ```

## Testing

### Evaluation Scenarios

| Scenario | Expected Behavior | Failure Indicator |
|----------|-------------------|-------------------|
| Fresh HTML build | Creates `docs/index.html` with all chapters | Missing chapters or broken links |
| Fresh PDF build | Creates `docs/Data-Science-with-R.pdf` | LaTeX errors or missing PDF |
| Missing R package | Clear error naming the missing package | Cryptic error without package name |
| Invalid `.Rmd` syntax | Error with file name and line number | Build hangs or no error context |

### Validation Commands

```bash
# Verify HTML build success
test -f docs/index.html && echo "HTML OK" || echo "HTML MISSING"

# Verify PDF build success
test -f docs/Data-Science-with-R.pdf && echo "PDF OK" || echo "PDF MISSING"

# Check for build warnings
grep -c "Warning" docs/*.log 2>/dev/null || echo "No warnings"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dparedesi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
