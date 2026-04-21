---
name: build-check
description: Run Docusaurus build, type checking, and linting to validate the site before deployment Use when this capability is needed.
metadata:
  author: ngtanthanh-qc
---

# Build Check

Run a full validation of the Docusaurus site.

## Steps

1. **Clear cache** first to ensure clean build:
   ```bash
   npm run clear
   ```

2. **Run TypeScript type checking**:
   ```bash
   npm run typecheck
   ```

3. **Run production build**:
   ```bash
   npm run build
   ```

4. **Report results**: Summarize any errors, warnings, or broken links found during the build process.

## Common Issues to Watch For

- Broken links (Docusaurus throws on broken links with `onBrokenLinks: "throw"`)
- MDX compilation errors (invalid JSX, unclosed tags)
- Missing imports in MDX files
- Image references to non-existent files
- TypeScript type errors in custom components

## Output Format

Report results as:
- Total build time
- Number of pages generated
- Any warnings or errors found
- Broken links if any
- Suggested fixes for any issues

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ngtanthanh-qc) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
