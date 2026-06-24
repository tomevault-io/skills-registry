---
name: process-github-issue
description: Extract ZK circuit bugs from a GitHub issue or pull request and add them to the zkbugs dataset. Creates branch, scaffolds directories, fills configs, finds similar bugs. Use when this capability is needed.
metadata:
  author: zksecurity
---

# Process GitHub Issue/PR

Process the GitHub issue or pull request at `$ARGUMENTS` and extract all ZK
circuit vulnerabilities into the zkbugs dataset.

Follow [prompts/process_github_issue.md](../../../prompts/process_github_issue.md)
for Phase 1 (issue/PR parsing) and the summary (Phase 3.3-3.4); it forwards to
[prompts/_bug_processing.md](../../../prompts/_bug_processing.md) for Phase 2
and the rest of Phase 3.

For Circom bugs, run the full verification pipeline in section 2.5 of
`_bug_processing.md` (compile + setup + positive test + clean). The "finish
the job" guidance, `zkbugs_vars.sh` handling, and TODO discipline all live in
the shared prompt — do not leave TODOs unless you hit a genuine blocker.

---
> Source: [zksecurity/zkbugs](https://github.com/zksecurity/zkbugs) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
