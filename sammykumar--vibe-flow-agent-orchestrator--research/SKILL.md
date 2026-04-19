---
name: research
description: Performs repository research, evidence-driven analysis, and produces PDD deliverables (1-PROGRESS.md, 2-RESEARCH.md, 3-SPEC.md) inside the active plan directory. Use when a deep investigation, codebase mapping, or specification is required. Use when this capability is needed.
metadata:
  author: sammykumar
---

# Research Skill

This skill implements the Research Methodologist behavior. It performs
repository research, evidence-driven analysis, and scaffolds PDD deliverables
into the active plan directory.

## Scope & Constraints

- Writes are strictly limited to the active plan directory:
  `.github/plans/{status}/{domain}/{scope-path}/{task-name}/`.
- **Do NOT** modify `src/` or production source files.
- Use Playwright/Chrome DevTools only for UI inspection tasks and only against
  local/dev servers when explicitly requested.

## When to use

- The request contains keywords like: "research", "spec", "investigate", or
  explicitly asks for a technical specification.

## High-level Steps

1. Initialize or verify the plan folder exists (use the `todo` tool to create
   a plan if needed).
2. Run repository searches and read files to map the codebase and surface
   relevant artifacts.
3. Capture external evidence and references (Agentskills spec, related docs)
   using `web/fetch_webpage`.
4. Instantiate `2-RESEARCH.md` from `assets/research-template.md` and fill it with task-specific analysis and evidence while preserving the template structure.
5. Instantiate `3-SPEC.md` from `assets/spec-template.md` and fill it with task-specific specification content while preserving the template structure.
6. Update `1-PROGRESS.md` and set status to `research_complete` when finished.

## Inference

The skill should be invoked when the user's request contains the above
keywords or asks for investigative/spec work. Use discretion and confirm intent
if ambiguous.

## Templates & Assets

This skill includes the following assets and templates (located under the
skill directory):

- `assets/progress-log-template.md` — starter `1-PROGRESS.md`
- `assets/research-template.md` — skeleton for `2-RESEARCH.md`
- `assets/spec-template.md` — skeleton for `3-SPEC.md`
- `references/REFERENCE.md` — tool usage and scope details

These `assets/` files are the templates the skill reads at runtime. The files in `docs/templates/` are reference copies for documentation and do not drive agent behavior directly.

## Example usage

```text
# Create a plan for 'add-auth-integration' and run research
@research: "Investigate authentication flow in the repository. The plan
  directory is '/absolute/path/to/.github/plans/in-progress/app/auth/add-auth-integration'.
  Create `2-RESEARCH.md` and `3-SPEC.md` skeletons and update progress."
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/sammykumar) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
