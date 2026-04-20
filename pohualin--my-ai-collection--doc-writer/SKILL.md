---
name: doc-writer
description: Use when working with a skill for generating, editing, and maintaining documentation for Java, Python, and React projects. Supports automated README creation, API reference generation, and code annotation.
metadata:
  author: pohualin
---

## Usage

- Use this skill to create or update documentation files (README, API docs, code comments) for Java, Python, and React codebases.
- Supports extracting docstrings, JSDoc, and JavaDoc comments.
- Can generate summaries, usage examples, and structured guides.

## Instructions

1. Select the target language (java, python, or react).
2. Provide the codebase or file to document.
3. Specify the type of documentation needed (README, API reference, or code comments).
4. The skill will generate or update the documentation accordingly.

## Bundled Assets

- templates/
  - readme-template.md
  - api-reference-template.md
  - code-comment-template.md

## Example

To generate a README for a Python project:
- Provide the project root and select 'python' as the language.
- The skill will extract module docstrings and generate a structured README.

## Limitations

- Asset files should be under 5MB each.
- Supports only Java, Python, and React codebases.

---

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pohualin) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
