---
name: doc-gen
description: Automated documentation generation and auditing. Use when extracting comments, generating READMEs, or finding undocumented code. Use when this capability is needed.
metadata:
  author: anuragsinghbhandari
---

# 📚 Doc Gen

Doc Gen helps keep your codebase documented and easy to understand.

## Tasks

### 1. Extract Documentation
- **Extract Docs**: Run `scripts/extract_docs.sh <file>` to pull JSDoc or Python docstrings from a source file.

### 2. Documentation Audit
- **Find Undocumented**: Run `scripts/check_undocumented.sh <file>` to identify functions or classes that are missing documentation.

## Workflow: Updating Documentation
1. Run `check_undocumented.sh` on a file to find gaps.
2. Use the LLM to write appropriate documentation for the missing parts.
3. Use `extract_docs.sh` to gather all existing documentation for a summary or README update.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/anuragsinghbhandari) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
