---
name: swift-5-to-6-migration
description: Step-by-step workflow to migrate a Swift 5.x codebase (macOS/iOS) to Swift 6 language mode using Swift 6.2-era toolchains, focusing on strict concurrency, incremental adoption, and deterministic verification. Use for Xcode/SwiftPM upgrades, enabling strict concurrency checking, flipping Swift 6 mode per module, and fixing Sendable/actor-isolation diagnostics. Use when this capability is needed.
metadata:
  author: zhutao100
---

# Swift 5.x → Swift 6+ migration runbook (for agents)

## Scope

This skill is designed to help an agent:
- upgrade a repo from Swift 5.x to Swift 6+ compiler/toolchain
- incrementally adopt **Swift 6 language mode** per target/module
- eliminate or manage strict-concurrency diagnostics (warnings first, then errors)
- keep the work **verifiable**: each change comes with deterministic build/test evidence

**In scope**
- Xcode projects (macOS/iOS) and SwiftPM packages
- mixed repos (Xcode app + internal SwiftPM packages)
- strict concurrency / Sendable / actor isolation / default actor isolation

**Out of scope**
- major architectural rewrites not required to satisfy concurrency correctness
- large dependency upgrades unrelated to Swift 6 readiness

If the user asks for a “quick compile fix”, still prefer the incremental path; use opt-outs only when justified and tracked.

---

## Inputs the agent must collect up front

1. **Build entry points**
   - Xcode: workspace/project + scheme(s)
   - SwiftPM: package root(s)
2. **Target inventory**
   - app/executables vs frameworks vs packages
3. **CI constraints**
   - pinned Xcode/toolchain? ability to pin?
4. **Definition of done**
   - “compiles in Swift 6 mode”, “warnings budget”, “all tests green”, etc.

Run:
- `scripts/swift_toolchain_probe.sh`
- `scripts/xcode_show_build_settings.sh …` (if Xcode)
- `scripts/spm_probe.py …` (if SwiftPM)

---

## Outputs

The agent should produce:
1. A **migration plan** (module ordering, gating policy, risks) using `assets/templates/migration_plan.md`
2. A sequence of **small PR-sized change sets**, each with:
   - settings change + code changes + tests
   - warning budget tracking (if needed)
3. A **post-migration cleanup queue** (remove opt-outs, refactor for performance)

---

# Step-by-step workflow

## 0) Preflight: toolchain + baseline

### 0.1 Pin the toolchain
- Pin Xcode/Swift toolchain in CI first; local dev follows CI.
- Record:
  - Xcode version
  - Swift version (`swift --version`)

### 0.2 Baseline build + tests
- Produce a baseline build/test run on main:
  - `xcodebuild test …` or `swift test`
- Capture:
  - current warnings count
  - current crash/perf regressions (if available)

### 0.3 Choose a target order
Default ordering:
1. top-level app/executable target(s)
2. UI-facing frameworks
3. internal frameworks/libraries
4. shared SwiftPM packages (if any)

Rationale: start “from the outside” so changes don’t break downstream dependents.

---

## 1) Phase 1 — Upgrade to Swift 6 compiler while staying in Swift 5 language mode

**Goal:** build under Swift 6 toolchain, but keep targets in Swift 5 mode.

### 1.1 Xcode target configuration (Swift 5 mode)
For each target:
- Swift Language Version: **5**
- Strict Concurrency Checking: **Complete** (warnings)

If you use xcconfig files, you can apply `assets/xcconfig/swift5_warnings_first.xcconfig` as a starting point.

Verify effective values using:

```bash
./scripts/xcode_show_build_settings.sh -workspace <App>.xcworkspace -scheme <Scheme>
```

### 1.2 SwiftPM target configuration (warnings-first)
- Run `scripts/spm_probe.py` and confirm whether `swift-tools-version` implicitly moved you into Swift 6 language mode.
- If you’re not ready for Swift 6 mode in a package, opt targets out (keep them in Swift 5 language mode) until they are migrated.

---

## 2) Phase 2 — “Warnings-first” strict concurrency remediation (per module)

**Goal:** eliminate or manage warnings in Swift 5 language mode so the eventual Swift 6 flip is controlled.

### 2.1 Create an issue/PR slice
For each module:
- Create a tracking issue from `assets/templates/migration_issue.md`
- Target one category at a time:
  - global/shared mutable state
  - Sendable conformance
  - actor isolation and protocol conformance mismatches
  - dependency boundary issues (`@preconcurrency`)

### 2.2 Apply “minimal truthful change”
Rules:
- Prefer **expressing the truth** (correct isolation) over suppressions.
- Avoid mixing refactors with migration enabling steps.
- Any opt-out must include:
  - a comment describing synchronization/contract
  - a tracking issue ID

### 2.3 Use fix patterns as a catalog
When you hit a diagnostic, map it to:
- `references/fix_patterns.md`

For example:
- “global variable is not concurrency-safe …”
- “sending … risks causing data races”
- “actor-isolated method cannot satisfy nonisolated requirement”

### 2.4 Verify after each slice
Minimum gates:
- Build
- Unit tests
- Target smoke test (UI if relevant)

---

## 3) Phase 3 — Flip Swift 6 language mode per module

Only do this when the module is “clean enough” under strict concurrency checking.

### 3.1 Switch to Swift 6 language mode
Xcode:
- Swift Language Version: **6**
- Strict Concurrency Checking: **Complete** (now errors)

SwiftPM:
- tools-version 6.0+ typically implies Swift 6 language mode unless opted out per target

If using xcconfig:
- `assets/xcconfig/swift6_language_mode.xcconfig`

### 3.2 Re-run the full gate
- unit tests
- integration/UI tests where stable
- run-time smoke tests (watch for actor isolation runtime assertions at module boundaries)

---

## 4) Phase 4 — Optional: Default Actor Isolation & Approachable Concurrency (Swift 6.2 era)

This is a **policy choice**, not required for correctness.

### 4.1 Default Actor Isolation (UI targets)
If the repo is UI-heavy and drowning in `@MainActor` annotations, consider setting:
- Default Actor Isolation: **MainActor** (app target and UI modules)

Then re-run performance checks:
- over-isolation can serialize work and regress responsiveness

### 4.2 Approachable Concurrency
If you enable the “Approachable Concurrency” build setting:
- validate behavior changes, particularly for `nonisolated` async functions
- run concurrency-sensitive tests in CI

---

## 5) Phase 5 — Cleanup: remove opt-outs, stabilize APIs

After everything compiles in Swift 6 mode:
- Remove `nonisolated(unsafe)` and `@unchecked Sendable` where possible
- Convert “blanket `@MainActor`” into intentional architecture boundaries
- Revisit `@preconcurrency` usage as dependencies adopt Swift 6

Use the PR checklist:
- `assets/templates/pr_checklist.md`

---

# Operational guidance for agents

## A) Evidence capture (required)
Every PR should include:
- toolchain versions (`swift --version`, `xcodebuild -version`)
- command(s) run for verification and their outcome
- warning deltas (before/after) if relevant

## B) Do not guess build setting keys
- Always verify using `xcodebuild -showBuildSettings`
- The script `scripts/xcode_show_build_settings.sh` is the canonical check

## C) Keep SKILL.md small; offload details
- Background / validation: `references/validation_notes.md`
- Deep fix patterns: `references/fix_patterns.md`
- Build settings recipes: `references/build_system_recipes.md`

---

# Quickstart: common tasks

## 1) “Plan the migration”
1. Run toolchain probe scripts
2. Inventory targets
3. Create a plan doc from `assets/templates/migration_plan.md`

## 2) “Turn on warnings-first strict concurrency”
- Apply Swift 5 mode + strict concurrency complete (warnings)
- Build & run tests
- Start fixing in slices using fix patterns

## 3) “Flip one module to Swift 6 mode”
- Ensure warnings-first is clean
- Set Swift language version to 6 for that target
- Fix errors; rerun full gates

---
> Source: [zhutao100/swift-5-to-6-migration-skill](https://github.com/zhutao100/swift-5-to-6-migration-skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
