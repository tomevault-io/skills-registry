---
name: ci-cd-setup
description: Use when creating or modifying GitHub Actions workflows, CI pipelines, or automated publishing for any ICL repo. Covers testing, formatting, linting, cross-platform builds, and package publishing.
metadata:
  author: icl-system
---

# CI/CD Setup

## When to Use

- Creating GitHub Actions workflows
- Setting up automated testing
- Configuring automated package publishing (crates.io, PyPI, npm)
- Setting up docs deployment (GitHub Pages)
- Working on ROADMAP.md Phase 8

## Context

ICL has 3 repos that need CI. The runtime repo has the most complex pipeline (build core + all bindings + publish to 3 registries). CI must enforce determinism — it's the last line of defense.

Reference files:
- `ICL-Docs/PLAN.md` — technology stack (GitHub Actions)
- `ICL-Runtime/ROADMAP.md` — Phase 8 items
- `CONTRIBUTING.md` — what CI must check

## Procedure

1. Identify which repo needs CI (Spec, Runtime, or Docs)
2. Create `.github/workflows/` directory
3. Write workflow YAML following the templates below
4. Ensure the workflow tests determinism (100-iteration tests)
5. Set up caching for Rust, Python, and Node.js dependencies
6. Test the workflow on a branch before merging to main
7. Add branch protection rules after CI is green

## Workflow Templates

### ICL-Runtime: CI

```yaml
name: CI
on: [push, pull_request]
jobs:
  build-and-test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: dtolnay/rust-toolchain@stable
      - uses: Swatinem/rust-cache@v2
      - run: cargo fmt --check
      - run: cargo clippy -- -D warnings
      - run: cargo build --workspace
      - run: cargo test --workspace
```

### ICL-Runtime: Publish (on tag)

```yaml
name: Publish
on:
  push:
    tags: ['v*']
jobs:
  publish-crate:
    # cargo publish icl-core, icl-cli
  publish-python:
    # maturin publish
  publish-npm:
    # wasm-pack publish
```

### ICL-Docs: Deploy

```yaml
name: Deploy Docs
on:
  push:
    branches: [main]
jobs:
  deploy:
    # mdbook build && deploy to GitHub Pages
```

## Rules

- **CI must run determinism tests** — not just `cargo test`, specifically 100-iteration tests
- **CI must check formatting** — `cargo fmt --check`
- **CI must run clippy** — `cargo clippy -- -D warnings`
- **Publishing requires a tag** — never auto-publish from main
- **Cross-platform testing** — Linux, macOS, Windows (at minimum Linux)
- **Cache dependencies** — CI should be fast (< 5 minutes for quick checks)

## Anti-Patterns

- CI that doesn't test determinism
- Auto-publishing on every push to main
- No caching (slow builds discourage frequent commits)
- Workflows that only run on Linux (miss platform-specific bugs)
- Secrets in workflow files (use GitHub Encrypted Secrets)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/icl-system) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
