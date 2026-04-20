---
name: feature-doc-review
description: Review a feature/component documentation to check that it adheres to repo standards. Use when this capability is needed.
metadata:
  author: johnludlow
---

# Feature Review

## Prerquisites

- markdownlint is installed and can be called via `npx markdownlint-cli`

## When to use this skill

Use this skill when the user wants to check whether documentation fits required criteria.

## Process inputs

- An existing document
- Optionally, a focus to review, such as a particular section or aspect

## Process outputs

- A report on the document's adherence to guidelines
- Suggested edits to make a document more compliant

## How to review the feature document

- Read the provided document
- Check for stylistic consistency, grammar, plain English and legibility
- Check for relevance to the subject matter
- Check for correctness compared to current implementation / goals
- Check for adherence to the checklist
- Check for adherence to the template
- Use `npx markdownlint-cli **/*.md` to check for and report markdown errors
- Use [check-doc-links.ps1](../../../scripts/check-doc-links.ps1) to check for bad links

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnludlow) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
