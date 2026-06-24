---
name: ci-cd-generator
description: Scaffold/audit GitHub Actions CI/CD — Go/Rust/TS. Generation: workflows + coverage gates (60–80%), security scans (CodeQL/Semgrep, SCA, SBOM, gitleaks), N+1/race/leak/load tests. Audit: severity-ranked findings + diff fixes. Triggers: 'criar CI', 'gerar pipeline', 'auditar pipeline', '/ci-cd-generator'. Use when this capability is needed.
metadata:
  author: Bruno-Cunha-Souza
---

# CI/CD Generator

Lifecycle skill that scaffolds a complete GitHub Actions pipeline for a Go, Rust, or TypeScript project. The workflow encodes opinionated defaults documented in the project owner's notes — coverage gates, N+1 query detection, race condition property-based testing, memory leak detection, and load testing — plus standard security gates (SAST, SCA, container scan, SBOM, secret scan).

## When to Use

The skill has two operating modes — **generation** (the default, walks Phase 0 → 6 below) and **audit** (read-only review of an existing pipeline, walks Phase A0 → A5 in [references/AUDIT.md](references/AUDIT.md)).

### Generation mode

- A new repository (Go, Rust, or TypeScript) has no `.github/workflows/` directory and the user wants CI/CD wired up
- An existing repository has an ad-hoc workflow that the user wants replaced with a complete, opinionated baseline
- The user explicitly asks for "criar CI", "gerar pipeline", "scaffold workflow", or invokes `/valarmindskills:ci-cd-generator`
- A polyglot monorepo needs per-language pipelines (run the skill once per project root)

### Audit mode

- The repository already has workflows authored before this skill existed and the user wants them reviewed
- A security incident exposed a CI weakness (compromised secret, malicious action) and the user wants the full surface checked
- Pre-release gate: confirm the workflows match the security level the team thinks they are running at
- The user explicitly asks for "auditar pipeline", "audit CI pipeline", "review existing workflow"

This skill is **language-aware and lifecycle-driven**: it detects → asks the minimum needed → applies heuristics → emits YAML or report → validates. For multi-stack API security review (design + active testing + Go and Next.js stack-specific lifecycles) run from a CI pipeline, complement with `@code-security-review` (Go branch — `references/golang/`; Next branch — `references/nextjs/`). For Next.js performance audits, see `@code-review` `references/NEXTJS.md`.

## Do not use when

- The user wants to fix or refactor a single line in an existing workflow — open the YAML directly or use `@github-pr-review`. For a full pipeline audit, switch to **audit mode** instead.
- The CI platform is GitLab CI, CircleCI, Buildkite, or Jenkins — this skill is GitHub Actions only
- The project language is not Go, Rust, or TypeScript (Node.js / Bun / Deno)
- The user wants to deploy infrastructure (Terraform, Pulumi, Kubernetes manifests) — pipeline ≠ infra

## Prerequisites

| Tool | Purpose | Install |
| --- | --- | --- |
| `gh` (GitHub CLI) | Branch protection, secrets management, dispatch | `brew install gh` then `gh auth login` |
| `actionlint` | Static lint of generated YAML | `brew install actionlint` |
| `yamllint` | Style and structural lint | `brew install yamllint` |
| Language toolchain on host | Local sanity check before commit | `go`, `cargo`, `bun`/`pnpm`/`npm` per project |

Required access:

- [ ] Read access to the repository root (for detection)
- [ ] Write access to `.github/` and `.github/workflows/`
- [ ] Permission to commit on a branch (skill never commits without explicit user approval)
- [ ] If branch protection is part of the request: admin role on the repository

## Phase 0 — Project Detection

Detect language, package manager, and runtime before generating anything. Run the steps in order; stop at the first conclusive match per axis.

```bash
# Step 1 — language
test -f go.mod        && echo "language: go"
test -f Cargo.toml    && echo "language: rust"
test -f package.json  && echo "language: typescript"
test -f tsconfig.json && echo "  ts-config: present"

# Step 2 — TypeScript runtime (only if language=typescript)
test -f bun.lockb         && echo "runtime: bun, pm: bun"
test -f pnpm-lock.yaml    && echo "runtime: node, pm: pnpm"
test -f yarn.lock         && echo "runtime: node, pm: yarn"
test -f package-lock.json && echo "runtime: node, pm: npm"

# Step 3 — Rust workspace shape
grep -q '\[workspace\]' Cargo.toml 2>/dev/null && echo "rust: workspace"

# Step 4 — Go module shape
grep -E '^go [0-9]+\.[0-9]+' go.mod | head -1   # toolchain version
grep -q '^// +build' . -r 2>/dev/null && echo "go: legacy build tags present"
```

Persist as `$LANG ∈ {go, rust, typescript}`, plus per-language sub-fields (`$RUNTIME`, `$PM`, `$WORKSPACE`). The next phases branch on these values.

| `$LANG` | Reference to load | Default file emitted |
| --- | --- | --- |
| `go` | [references/GO.md](references/GO.md) | `.github/workflows/ci.yml` |
| `rust` | [references/RUST.md](references/RUST.md) | `.github/workflows/ci.yml` |
| `typescript` | [references/TYPESCRIPT.md](references/TYPESCRIPT.md) | `.github/workflows/ci.yml` |
| `polyglot` (multiple matches) | Run Phase 0 per subdirectory | One workflow per language detected |

If no match is found, abort with a one-line notice. The skill does not invent a language.

## Phase 1 — Collect Preferences

Ask the user **only** for inputs that cannot be inferred. Use one consolidated `AskUserQuestion` block, never one question per phase.

| Input | Required | Default | Inferable from |
| --- | --- | --- | --- |
| Coverage threshold | No | `60` (per user heuristic) | None — must default |
| Security level | No | `standard` (SAST + SCA + secret scan) | None — must default |
| Container scan | No | `true` if `Dockerfile` exists, else `false` | `test -f Dockerfile` |
| SBOM emission | No | `true` if `security level = strict` | None |
| Release workflow (tag-driven) | No | `true` if `goreleaser.yml` exists or user has `github-release-note` history | `test -f .goreleaser.yml` |
| Dependabot config | No | `true` (always) | None |
| Load testing job | No | `false` (heavy; opt-in) | Never default on |
| Branch protection rules | No | Suggest in report; do not apply automatically | None |

Three security levels:

- **`minimal`** — lint + test + build. No security gates. Use only for prototypes.
- **`standard`** (default) — adds CodeQL/Semgrep, dependency audit, secret scan
- **`strict`** — adds container scan (trivy), SBOM (syft), license check, signing

The full preferences-elicitation script lives in [references/USER_HEURISTICS.md](references/USER_HEURISTICS.md). It also documents the rationale for each default, citing the project owner's vault notes.

## Phase 2 — Apply User Heuristics

The pipeline emits five opinionated checks regardless of language. Each is sourced from the project owner's notes and is mandatory unless the user opts out explicitly.

| # | Heuristic | Default behavior | Override flag |
| --- | --- | --- | --- |
| 1 | **Coverage gate** ≥ 60% (warn ≥ 80%) | Pipeline fails below threshold | `--coverage=N` or "ignore coverage" |
| 2 | **N+1 detection** | Integration tests assert max query count per request | `--no-n1` |
| 3 | **Race condition PBT** | Concurrent property-based test job; `-race` flag in Go | `--no-race` |
| 4 | **Memory leak detection** | Jest/Vitest `--detectOpenHandles --detectLeaks` (TS); `-race` (Go); miri optional (Rust) | `--no-leak-detect` |
| 5 | **Load testing** | Nightly `workflow_dispatch` job using k6 or artillery (opt-in) | Default off |

For each heuristic, [references/USER_HEURISTICS.md](references/USER_HEURISTICS.md) contains:

- The exact rationale from the source notes
- The detection technique per language
- A copy-pasteable workflow snippet
- The expected failure mode if the heuristic catches something

## Phase 3 — Wire Security Gates

Branch on the security level chosen in Phase 1. Each gate is a separate job that runs in parallel with the test job whenever possible.

| Gate | `minimal` | `standard` | `strict` |
| --- | :---: | :---: | :---: |
| `actionlint` self-check | ✓ | ✓ | ✓ |
| Lint + format | ✓ | ✓ | ✓ |
| Unit + integration tests | ✓ | ✓ | ✓ |
| Coverage gate | ✓ | ✓ | ✓ |
| **CodeQL / Semgrep (SAST)** | — | ✓ | ✓ |
| **Dependency audit (SCA)** | — | ✓ | ✓ |
| **Secret scan (gitleaks)** | — | ✓ | ✓ |
| **Container scan (trivy)** | — | conditional | ✓ |
| **SBOM (syft / cyclonedx)** | — | — | ✓ |
| License check | — | — | ✓ |
| Provenance / signing (cosign + SLSA) | — | — | optional |

Per-language tool selection (CodeQL languages, audit commands, container base scan strategy) is captured in [references/SECURITY_GATES.md](references/SECURITY_GATES.md).

Anti-patterns the generator must refuse to emit:

- `pull_request_target` with checkout of the PR head (RCE vector)
- `permissions:` defaulting to `write-all` at the workflow level
- Third-party actions without a pinned commit SHA when `security level = strict`
- Secrets passed via `env:` at the workflow level (must be job- or step-scoped)
- `actions/checkout@v4` followed by running untrusted scripts before any allowlist check

## Phase 4 — Generate Workflow YAML

Emit the YAML in this skeleton, populated from the language-specific reference:

```yaml
name: ci
on:
  push:
    branches: [main]
  pull_request:
    branches: [main]

permissions:
  contents: read

concurrency:
  group: ci-${{ github.ref }}
  cancel-in-progress: true

jobs:
  meta:
    name: actionlint
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: rhysd/actionlint@v1   # pinned via SHA in `strict`

  lint:
    needs: meta
    # populated from <language>.md

  test:
    needs: meta
    # populated from <language>.md
    # includes: race flag, coverage gate, N+1 assertion, leak detection

  security:
    needs: meta
    # populated from SECURITY_GATES.md per security-level

  build:
    needs: [lint, test, security]
    # populated from <language>.md
```

Per-language fully populated workflows (with matrix, cache, environment variables, and the canonical job graph) live in:

- [references/GO.md](references/GO.md) — `actions/setup-go@v5`, `go test -race -cover -covermode=atomic`, `staticcheck`, `golangci-lint`, `govulncheck`, optional `goreleaser`
- [references/RUST.md](references/RUST.md) — `actions-rust-lang/setup-rust-toolchain@v1`, `Swatinem/rust-cache@v2`, `cargo fmt --check`, `cargo clippy -D warnings`, `cargo nextest`, `cargo-llvm-cov`, `cargo-audit`, `cargo-deny`
- [references/TYPESCRIPT.md](references/TYPESCRIPT.md) — `setup-bun` / `setup-node` + `pnpm/action-setup`, `tsc --noEmit`, `eslint`, `vitest`/`jest` with `--detectOpenHandles --detectLeaks`, N+1 query test template, `size-limit`

Always pin third-party actions:

- `standard` level: `@vN` major-version pin
- `strict` level: full commit SHA pin (`@<40-char-sha>`) with a comment naming the version

## Phase 5 — Validation & Dry-Run

Before reporting success, run the local checks:

```bash
# 1. Lint the YAML
actionlint .github/workflows/*.yml
yamllint -d relaxed .github/workflows/*.yml

# 2. Confirm secrets referenced in the YAML
grep -hoE '\$\{\{ secrets\.[A-Z_]+ \}\}' .github/workflows/*.yml | sort -u

# 3. Verify all third-party actions are pinned
grep -hE 'uses: ' .github/workflows/*.yml | grep -v 'actions/' | grep -v '@[a-f0-9]\{40\}\|@v[0-9]'

# 4. List required workflows for branch protection
grep -E '^  [a-z_-]+:$' .github/workflows/ci.yml | sed 's/[: ]//g'
```

Each finding is added to the post-generation report. If `actionlint` reports errors, abort and emit the diff for the user instead of writing the file.

The full validation matrix and the optional `gh workflow run --ref <branch> ci.yml` smoke test are in [references/CHECKLIST.md](references/CHECKLIST.md).

## Phase 6 — Deliver

1. Write the workflow file(s) to `.github/workflows/`. Default filename: `ci.yml`. Optional: `release.yml`, `nightly-load.yml`.
2. Write `.github/dependabot.yml` if Phase 1 enabled it.
3. Emit the generation report (see Output format).
4. Surface the suggested branch protection rules — do not apply them automatically. Provide the `gh` command the user can run.
5. Hand off to `@github-commit` to commit the new files; do not commit unless the user explicitly approves.

A worked end-to-end run of Phase 0 → Phase 6 for a Go API project is in [EXAMPLE.md](EXAMPLE.md).

## Audit mode (alternate entry)

When the user asks to **audit** an existing pipeline rather than generate one, branch at the very top of Phase 0 into the audit procedure documented in [references/AUDIT.md](references/AUDIT.md). The audit walks Phase A0 → A5 (inventory → heuristic compliance → security gate compliance → anti-pattern sweep → action freshness → caching/concurrency hygiene) and emits a severity-ranked findings table with fix proposals.

The audit is **read-only by default**. The user can opt in per finding ID for the skill to apply unified-diff fixes; each fix is tagged **SAFE** / **REVIEW** / **BREAKING** and re-validated with `actionlint` before report-ok. The skill never commits.

Pick the entry point by user intent:

| Intent signal | Entry |
| --- | --- |
| "criar CI", "gerar pipeline", "scaffold workflow", `/ci-cd-generator` on a repo with no `.github/workflows/` | Phase 0 (generation) |
| "auditar pipeline", "audit CI", "review existing workflow", `/ci-cd-generator` on a repo with workflows present | Phase A0 (audit) |
| Ambiguous and `.github/workflows/` exists | Ask once: "audit existing workflows or generate a new baseline alongside?" — do not assume |

## Constraints

- **Never** commit the generated YAML without explicit user approval — emit the diff first
- **Never** apply branch protection rules without an explicit `gh api` call confirmed by the user
- **Never** emit `pull_request_target` with PR-head checkout
- **Never** default `permissions:` to `write-all` at workflow level — start at `contents: read` and elevate per job
- **Never** pass secrets via the workflow-level `env:`; scope them to the job or step
- **Never** use third-party actions without a version pin (major tag minimum, SHA in `strict`)
- **Never** generate a pipeline for a language outside `{go, rust, typescript}` — abort with a one-line notice
- **Never** silently downgrade the security level when a tool is missing — surface the gap to the user
- **Must** load the matching language reference before emitting any YAML
- **Must** run `actionlint` on the generated file before reporting success
- **Must** include a header comment listing required secrets, the trigger graph, and the source skill
- **Must** keep the workflow under 500 lines per file; split into `ci.yml` + `release.yml` + `nightly-load.yml` when needed
- **Must not** invent secrets or repository variables — every `${{ secrets.X }}` reference must be listed in the report

## Output format

Generation report (printed verbatim after every successful run):

```text
ci-cd-generator: <action>
  language:      <go | rust | typescript>
  runtime/pm:    <node+pnpm | bun | rust+nextest | go+toolchain x.y>
  security:      <minimal | standard | strict>
  workflows:     <files written, paths under .github/>
  jobs:          <ordered list of job ids in ci.yml>
  required-secrets: <SECRET_A, SECRET_B, … or "none">

Suggested branch protection (run after committing):
  gh api -X PUT repos/<owner>/<repo>/branches/main/protection \
    -F required_status_checks.strict=true \
    -F 'required_status_checks.contexts[]=lint' \
    -F 'required_status_checks.contexts[]=test' \
    -F 'required_status_checks.contexts[]=security' \
    -F enforce_admins=true \
    -F required_pull_request_reviews.required_approving_review_count=1

Next: review the diff, then `/valarmindskills:github-commit`.
```

## Related Skills

- `@github-commit` — commit the generated workflow files following Conventional Commits
- `@github-release-note` — generate release notes when the release workflow tags a version
- `@github-pr-review` — review the diff that introduces the workflow
- `@clean-code` — apply principles to the YAML (consistent naming, no duplication across jobs)
- `@code-security-review` — multi-stack security review job covering generic flows + Go (Gin/Fiber, `references/golang/`) + Next.js 16 App Router (`references/nextjs/`); active testing via `references/TESTING_PHASES.md`, run nightly via the load testing slot; 100-vuln class catalog via `references/WEB_VULNERABILITIES.md` when the pipeline must spell out which classes each gate addresses
- `@code-debugger` — diagnosing a failing generated workflow before reporting it as broken

## References

- [GO](references/GO.md) — Go pipeline template, tooling matrix, canonical workflow
- [RUST](references/RUST.md) — Rust pipeline template, tooling matrix, canonical workflow
- [TYPESCRIPT](references/TYPESCRIPT.md) — TypeScript pipeline template (Node + Bun), N+1 and leak templates
- [USER_HEURISTICS](references/USER_HEURISTICS.md) — coverage gate, N+1, race PBT, leak detection, load testing — rationale and snippets
- [SECURITY_GATES](references/SECURITY_GATES.md) — SAST, SCA, container, SBOM, secret scan, license — per-language tool matrix
- [CHECKLIST](references/CHECKLIST.md) — post-generation validation, branch protection, smoke test
- [AUDIT](references/AUDIT.md) — audit mode procedure, detection rules, severity matrix, report format, opt-in fix application
- [EXAMPLE](EXAMPLE.md) — end-to-end worked example for a Go API project

---
> Source: [Bruno-Cunha-Souza/ValarMindSkills](https://github.com/Bruno-Cunha-Souza/ValarMindSkills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
