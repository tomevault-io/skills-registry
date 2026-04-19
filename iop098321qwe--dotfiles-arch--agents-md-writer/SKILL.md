---
name: agents-md-writer
description: >- Use when this capability is needed.
metadata:
  author: iop098321qwe
---

# AGENTS.md authoring and maintenance

## Purpose and scope

- Create and maintain an accurate, thorough, and current AGENTS.md.
- Use the references bundled with this skill. They are not part of the
  target repo and must still be followed.
- Use no assumptions. Every statement must be verified against the
  codebase, configuration, or explicit user input.
- If verification is not possible, mark the item as
  "Verification needed" and explain what is missing.
- Always keep AGENTS.md aligned with the codebase. If there is a
  mismatch, update AGENTS.md to match the code.

## Hard requirements

- Follow the fixed section order in
  `references/agents-md-template.md`. Do not reorder sections.
- Keep lines to 80 characters, with exceptions only for:
  URLs, code blocks, tables, hashes, or unbreakable commands.
  Document each exception inline.
- Include an explicit statement that best practices and industry
  standards must be followed where applicable.
- Include a "Refining Existing AGENTS.md" section with steps for
  improving clarity and AI consumption.
- Include a "Maintenance" section that documents the self-audit loop.
- Always scan AGENTS.md for update notes or logs and remove them.
- Never append or preserve update notes or logs in AGENTS.md.
- Require a "Tracked Files Overview" section in every AGENTS.md.
  - Place it immediately after "Repository Overview" in the fixed order.
  - List every tracked file in directory order with a concise purpose.
  - Keep it updated whenever AGENTS.md changes or tracked files change.
  - If a file purpose cannot be verified, mark "Verification needed".
  - Keep entries short and atomic, with documented line exceptions.
- Always use this skill whenever any AGENTS.md file is referenced,
  regardless of repository or directory.
- Always verify whether any AGENTS.md exists anywhere in the repo, and use
  this skill when one is found.
- Treat any command that runs in a repo with an AGENTS.md file as a
  trigger to use this skill, even if AGENTS.md is not referenced.
- Never make direct edits to `CHANGELOG.md`. If a changelog update is
  required, stop and ask for the release process or automation that should
  generate it.
- After any non-AGENTS.md file change in such a repo, re-run the
  self-audit loop and update AGENTS.md if it is out of sync.
- Always use this skill's references for requirements and format guidance,
  even when the target repo does not contain those reference files.

## Required references

- Read `references/agents-md-template.md` for the exact section order
  and required headings. This reference is mandatory, even when the
  target repo does not contain it.
- Read `references/agents-md-checklist.md` to ensure completeness and
  accuracy against the repo. This reference is mandatory, even when the
  target repo does not contain it.
- Read `references/agents-md-self-audit.md` and apply the audit loop
  after any code change. This reference is mandatory, even when the
  target repo does not contain it.
- Read `references/conventional-commits.md` when drafting commit
  messages. This reference is mandatory, even when the target repo does
  not contain it.

## No-assumptions rule

- Do not infer or guess commands, versions, paths, or workflows.
- Do not copy common patterns unless they are confirmed in the repo.
- If a section requires information you cannot verify, add a
  "Verification needed" note with the exact source that must be
  checked.
- Prefer fewer, correct statements over more, speculative content.

## Source of truth collection

1. Locate existing AGENTS.md and read it first.
2. Identify the repository root and confirm it.
3. Inventory files that define workflows:
   - Build and package scripts.
   - Test and lint configurations.
   - CI/CD definitions.
   - Runtime or version managers.
   - Service definitions and infrastructure configs.
4. Confirm the authoritative commands from real configs or scripts.
5. Extract concrete versions from config files, not assumptions.
6. Note any missing or ambiguous information.

## Writing workflow (step-by-step)

1. Start from the template.
   - Use `references/agents-md-template.md` as the skeleton.
   - Keep the heading order unchanged.
2. Fill each section using verified repo facts.
   - Provide exact file paths and command invocations.
   - Keep bullets short and action-oriented.
3. Document constraints and exceptions.
   - For any 80-character exception, explain why it is necessary.
4. Add the best practices statement.
   - Use explicit language that best practices and industry standards
     must be followed where applicable.
5. Add the refining section.
   - Include the steps for improving and optimizing AGENTS.md for AI.
6. Add the maintenance section.
   - Include the self-audit loop without change-log guidance.
   - Require a scan to remove update notes or logs.
7. Validate the document against the checklist.
   - Use `references/agents-md-checklist.md`.

## Section-by-section guidance

### Purpose

- State the document objective and who should use it.
- Include the explicit best-practices-and-standards statement.

### Scope

- Define what the document covers and excludes.
- Avoid vague language. Use concrete boundaries.

### Formatting Rules

- Include the 80-character line limit and exceptions.
- Call out any exception in the same section where it appears.

### Quick Start

- Provide a minimal, verified path to run or test the project.
- Do not include optional steps unless required to succeed.

### Environment

- List required versions from actual config files.
- List required environment variables and their purposes.

### Repository Overview

- Summarize top-level directories with one-line roles.
- Keep descriptions precise and verified.

### Tracked Files Overview

- List every tracked file in directory order with a one-line purpose.
- Include files in the same order as they appear in the repo tree.
- Mark unknown purposes as "Verification needed" with the source needed.
- Keep entries short and atomic; document line-length exceptions.

### Architecture

- Describe major components and data flow.
- Link to code paths or modules when possible.

### Commands

- List commands with exact invocation and purpose.
- Include build, run, and common tasks.

### Testing

- Provide test commands and test structure.
- Note coverage or required test gates if present.

### Linting and Formatting

- List lint and format commands and config locations.

### CI and Release

- Summarize CI checks and release steps.
- List required status checks if present.

### Conventions

- Document naming, logging, error handling, and review rules.

### Security and Compliance

- Document secret handling and security practices.
- List compliance constraints if present.

### Dependencies and Services

- List external services, databases, queues, and storage.

### Troubleshooting

- Provide common failure modes and verified fixes.

### Refining Existing AGENTS.md

- Include a step-by-step refinement process:
  - Verify every statement against the repo.
  - Remove stale or duplicated content.
  - Normalize section order and formatting.
  - Replace vague guidance with commands and paths.
  - Add "Verification needed" notes where data is missing.
  - Optimize for AI with short, atomic bullets.

### Maintenance

- Define the self-audit loop and when to run it.
- Do not record AGENTS.md changes inside AGENTS.md.
- Remove any update notes or logs from AGENTS.md.

## Mandatory self-audit loop

- Run the audit after any code change or config change.
- Compare AGENTS.md to the repo and update mismatches.
- Re-check commands, versions, directory names, and workflows.
- Do not record AGENTS.md changes inside AGENTS.md.

## Conventional Commits requirement

- Use Conventional Commits when drafting commit messages.
- Follow `references/conventional-commits.md` for format and examples.

## Verification checklist

- All required sections exist and follow the fixed order.
- Each statement is verified or marked as "Verification needed".
- Commands are accurate, minimal, and reference correct paths.
- Line length is <= 80 characters, exceptions documented.
- Best practices and industry standards are explicitly required.
- Refinement and maintenance guidance is present and actionable.
- AGENTS.md matches the current codebase state.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/iop098321qwe) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
