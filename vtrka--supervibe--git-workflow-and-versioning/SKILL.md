---
name: git-workflow-and-versioning
description: Use WHEN making, grouping, committing, versioning, or releasing changes to keep git history atomic, reversible, and user-work safe.
metadata:
  author: vTRKA
---

# Git Workflow And Versioning

## Overview

Git Workflow And Versioning keeps changes easy to review, revert, release, and audit. It complements `using-git-worktrees`, `finishing-a-development-branch`, `pre-pr-check`, and release governance with explicit atomic-commit, branch hygiene, version surface, changelog, tag, and user-work protection rules.

## When to Use

Use before committing, splitting, staging, rebasing, tagging, version bumping, changelog writing, or release branch finishing. Use when multiple files changed and the reviewer needs a coherent story.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: start from source of truth, preserve context evidence, apply scope safety, use real producers with runtime receipts for durable delegated outputs, verify before completion claims, and keep confidence below gate when evidence is partial.


## Swarm-First Durable Work Gate

Follow `<resolved-supervibe-plugin-root>/docs/references/swarm-first-skill-contract.md`. Named durable worker, reviewer, producer, validator, verifier, or specialist work requires real specialist dispatch through the host/runtime path with selected skill ids, resolved `SKILL.md` paths, runtime receipts or receipt-ready host invocation evidence, and scoped verification evidence. Inline or controller-authored work is diagnostic or trivial-only; it cannot satisfy durable proof, reviewer proof, producer proof, validator proof, verification proof, task closure, or graph task closure.
## Step 0 - Read source of truth

1. Run `git status --short --branch` and separate intended edits from user or parallel-worker changes.
2. Read active plan, work item, PRD, release note, or user request that defines the allowed write set.
3. Search project memory for branch, version, changelog, release, or rollback decisions when release impact exists.
4. Use CodeGraph when commit grouping depends on public APIs, generated files, or shared runtime behavior.
5. Read package/plugin/version surfaces before proposing a version bump.

## Anti-patterns

- Ignoring the boundary: Do not mutate git history, stage files, commit, tag, rebase, push, or publish without user/workflow authority.
- Accepting the rationalization: "I will just commit everything" fails when unrelated user work or generated drift is mixed with the requested change.
- Proceeding despite this red flag: Commit includes files with no relationship to the stated behavior.
- Letting the known failure mode happen: Clean history is achieved by reverting user changes.

## When not to use

- Do not mutate git history, stage files, commit, tag, rebase, push, or publish without user/workflow authority.
- Do not use to hide unrelated user changes inside a commit.
- Do not bump versions before implementation and verification gates are green.

## Decision tree

```text
Is git mutation authorized?
  NO  -> inspect and propose only.
  YES -> continue with status, scope, and verification checks.

Are changes separable by behavior?
  YES -> split into atomic commits by user-observable or contract outcome.
  NO  -> one commit may be acceptable if it has one reason to revert.

Does the change affect release/package/plugin surfaces?
  YES -> require version, changelog, docs, registry, and rollback checks.
  NO  -> commit grouping and PR summary are enough.
```

## Practice matrix

| Work type | Grouping practice | Required proof | Reject when |
| --- | --- | --- | --- |
| Feature or fix | One reversible behavior per commit | targeted check for that behavior | unrelated files are mixed in |
| Version or release prep | Sync package, plugin, registry, lockfile, docs, and changelog | release gate or scoped release validator | surfaces disagree |
| History operation | Capture status, upstream, conflict risk, and rollback | pre/post `git status --short --branch` | authority is missing |

## Procedure

1. Inspect git status and current branch before any staging or history decision.
2. Map changed files to user outcome, task, or release surface.
3. Split commits so each commit has one reason to revert and one verification story.
4. Keep generated files with the source or command that produced them; do not stage unexplained generated drift.
5. Write commit messages in imperative mood with scope and behavior, not tool narration.
6. For versioned artifacts, check all version surfaces: package manifests, plugin metadata, registry, changelog, docs, lockfiles, and generated install metadata.
7. For release tags, verify tag name, target commit, changelog entry, package version, and rollback path agree.
8. Before rebase/merge/cherry-pick, record current status and conflict risk; never overwrite unrelated work.
9. Run the targeted verification for each commit group or the release gate when versioning changes.
10. Re-run `git status --short --branch` after operations and report remaining unrelated changes.
11. Workflow cycle: read status and scope -> choose commit/version grouping -> run verification -> if a command fails, unrelated work appears, or release surfaces disagree, stop or repair the plan and rerun the same scoped command before staging or claiming readiness.

## Examples

- Worked example: a skill change plus generated registry update becomes one commit only if the registry file was produced by the named command and the commit message names the skill behavior being changed.
- Worked example: a package release bump checks package.json, lockfile, plugin metadata, changelog, release path, and rollback before proposing tag or publish steps.
- Anti-example: staging `git add .` in a dirty worktree that includes unrelated agent edits, generated reports, and user notes; return `BLOCKED` or advisory grouping instead.

## Common rationalizations

- "I will just commit everything" fails when unrelated user work or generated drift is mixed with the requested change.
- "Version bump is simple" fails when package, plugin, registry, changelog, and docs surfaces can drift.
- "Rebase is safe" fails without status evidence and a rollback plan for conflicts.

## Red flags

- Commit includes files with no relationship to the stated behavior.
- Changelog claims behavior that no test or validator verified.
- Version surfaces disagree across package, plugin, registry, README, or lockfile.
- Tag or release note exists before checks pass.
- Untracked files are staged without source or purpose.

## Checklist

- Status checked before and after.
- Unrelated work preserved and excluded.
- Commit groups are atomic and reversible.
- Version/changelog/tag surfaces are synced when relevant.
- Verification command output is attached to the git or release claim.

## Failure modes

- Clean history is achieved by reverting user changes.
- Commit boundaries follow file type instead of behavior.
- Version is bumped to unblock release process before quality gates pass.
- Rollback path depends on artifacts not created or published.

## Output contract

Return a parseable `gitVersioningPlan`:

```json
{
  "status": "PASS | PARTIAL | BLOCKED | ADVISORY",
  "branch": {"current": "name", "target": "name or unknown"},
  "statusBefore": "short status summary",
  "intendedChanges": [{"group": "behavior or release surface", "files": []}],
  "excludedChanges": [{"file": "path", "reason": "unrelated or user-owned"}],
  "commitPlan": [{"message": "imperative subject", "files": [], "verification": "command"}],
  "versionPlan": {"surfaces": [], "changelog": "path or none", "tag": "name or none"},
  "verificationCommands": ["exact command"],
  "rollback": "revert, branch restore, tag delete, or package rollback path",
  "nextAction": "stage | split | verify | handoff | stop"
}
```

## Guard rails

- DO NOT stage, commit, tag, push, publish, rebase, merge, or rewrite history without explicit user or workflow authority.
- DO NOT include unrelated user-owned files in a commit to make the tree look clean.
- ALWAYS capture status before and after git operations and record the verification command tied to each proposed commit group.
- Never run destructive git commands unless explicitly requested.
- Never stage, commit, tag, push, publish, rebase, or merge without authority.
- Never use `git reset --hard` or `git checkout --` to clean unrelated changes.
- Keep release claims separate from advisory git hygiene suggestions.

## Verification

- `git status --short --branch`
- Targeted tests or validators for changed files.
- `npm run check` before release handoff when release scope is in play.
- `npm run validate:skill-bundle-coverage` when skill bundle ownership changes.
- After any staging, rebase, merge, tag, or version repair, rerun `git status --short --branch` and the same scoped verification command before claiming the plan is ready.
- If any verification command fails, record exit code, mark the git plan `BLOCKED` or `PARTIAL`, repair only the affected commit group, and rerun the same command before moving to the next action.

## Supporting references

- Native `git status`, `git diff`, `git log`, `git tag`, and `git revert` are the deterministic helpers for this skill; do not emulate them manually.
- `<resolved-supervibe-plugin-root>/scripts/validate-release-path.mjs` and `<resolved-supervibe-plugin-root>/scripts/run-release-check.mjs` - release-surface helper scripts when versioning affects release.
- `<resolved-supervibe-plugin-root>/tests/release-provenance.test.mjs` and `<resolved-supervibe-plugin-root>/tests/release-path-validator.test.mjs` - regression eval coverage for release/version proof.
- `<resolved-supervibe-plugin-root>/skills/shipping-and-launch/templates/release-readiness.md` - release practice pack for versioned handoff.
- Worked examples and anti-example above are the local examples pack.

## Related

- `supervibe:using-git-worktrees`
- `supervibe:finishing-a-development-branch`
- `supervibe:pre-pr-check`
- `supervibe:shipping-and-launch`
- `supervibe:verification`
## Supporting references

### Resource tree hardening

- `references/practice-pack.md` - Read when git-workflow-and-versioning needs deeper load rules, local evidence anchors, gotchas, or a final checklist.
- `scripts/self-check.mjs` - Run with `--check` before claiming the git-workflow-and-versioning resource tree is complete; add `--json` when machine-readable evidence is needed.
- `evals/regression.json` - Use when tuning git-workflow-and-versioning trigger boundaries or checking should-trigger and should-not-trigger prompts.
- `examples/workflow.md` - Load when a concrete git-workflow-and-versioning workflow example or anti-example would clarify the next action.
- `templates/output-contract.md` - Use when emitting agent-output so status, evidence, blockers, confidence, and nextAction stay consistent.

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
