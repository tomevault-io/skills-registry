---
name: document-agent
description: Universal documentation retrieval and evidence workflow for large local corpora. Use when Codex needs to inventory document trees, route files to parsers, call the doc-agent CLI, retrieve grounded content from normalized or parser-native artifacts, and answer user questions about PDFs, Office files, engineering packs, or mixed-format documentation sets. Use when this capability is needed.
metadata:
  author: bogerman1
---

# Document Agent

Use the `docharbor` MCP server when it is configured. Fall back to the `doc-agent` CLI only when MCP is unavailable. Prefer the file-tree workflow over ad hoc file hunting.

If the user gives a concrete local file or small folder path, prefer:

- `doc-agent ask-path --source-path "<path>" --question "<question>" --format json --no-write`

If the user gives a concrete local file or small folder path and asks for translation, prefer:

- `doc-agent translate-path --source-path "<path>" --target-lang <lang> --format json`

Use `--translation-mode lite` only when the user explicitly asks for a DeepL/DeepLX-backed rough translation. Lite mode keeps DocHarbor parsing/rendering and LLM audit/review, but DeepL/DeepLX itself is text-only and does not replace PDF/Office/CAD layout handling.

When reading the `ask-path` result, prioritize:

- top-level `notes`
- `route_summary`
- `answer.data.answer_text`
- `answer.data.coverage_warning`

For top-level project roots or very large folders, do not assume `ask-path` should synchronously finish the entire corpus. Expect staged parsing and partial answers while PDF work continues.

## Core Workflow

1. Initialize a project if the corpus does not already have one.
2. Run inventory to create `inventory.json` and `routing.json`.
3. For PDF-heavy corpora, build a MinerU v4 queue before parsing.
4. Use `status` to understand parser coverage and backlog before doing large runs.
5. Normalize parser outputs before building retrieval trees.
6. Build the tree index from normalized Markdown or synthetic Markdown.
7. Use manifests first, then normalized outputs, then parser-native outputs.
8. Cite file path plus page or section evidence whenever possible.

## Commands

- `doc-agent init-project --project <slug> --source-root "<path>"`
- `doc-agent inventory --project <slug> --source-root "<path>"`
- `doc-agent queue --project <slug> --priority core --priority high`
- `doc-agent mineru-submit --project <slug>`
- `doc-agent mineru-poll --project <slug> --state-file "<batch json>" --wait --download`
- `doc-agent doctor --project <slug> --format json`
- `doc-agent status --project <slug>`
- `doc-agent status --project <slug> --format json --no-write`
- `doc-agent refresh --project <slug>`
- `doc-agent test-questions --project <slug> --question-set "<path>.json"`
- `doc-agent parse-native --project <slug>`
- `doc-agent parse-office --project <slug>`
- `doc-agent normalize-mineru --project <slug>`
- `doc-agent build-index --project <slug>`
- `doc-agent answer --project <slug> --question "<question>"`
- `doc-agent answer --project <slug> --question "<question>" --format json --no-write`
- `doc-agent migrate-project --project <slug> --format json`
- `doc-agent migrate-project --project <slug> --execute --rebuild`

## Important Rules

- Prefer MCP tools over shell commands when the client supports MCP.
- Prefer `ask-path` over raw file scraping for one-off local path questions.
- Do not treat a large project root as a one-off path question; use staged corpus handling and partial indexed answers.
- Treat MinerU v4 as the production PDF parser.
- Do not rely on lightweight Markdown-only parse paths for engineering PDFs.
- Preserve parser-native outputs before normalization.
- Run `doctor` before blaming corpus content for local environment failures.
- Use `status --format json --no-write` and `answer --format json --no-write` when another agent or tool needs a stable machine-readable contract.
- Watch for duplicate `doc_id` warnings in status reports when working with older inventories, then use `migrate-project` to move them onto the path-aware schema.
- Answer from manifests and normalized artifacts before opening raw files.

Read [references/workflow.md](references/workflow.md) when you need the detailed reasoning behind parser routing, PageIndex usage, and the universal project structure.

---
> Source: [bogerman1/docharbor](https://github.com/bogerman1/docharbor) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-29 -->
