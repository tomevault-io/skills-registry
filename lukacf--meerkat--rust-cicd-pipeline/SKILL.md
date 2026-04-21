---
name: rust-cicd-pipeline
description: | Use when this capability is needed.
metadata:
  author: lukacf
---

# Rust CI/CD Pipeline Setup

This skill provides a complete, production-ready CI/CD pipeline for Rust projects featuring:

- **Progressive Testing**: Fast tests on commit, full tests on push
- **Makefile as Single Source of Truth**: Identical commands locally and in any CI system
- **No GitHub Actions Dependency**: Works with Jenkins, GitLab CI, or any CI runner
- **Version Consistency**: Automatic verification between tag and Cargo.toml

## Architecture Overview

```
┌─────────────────┐     ┌─────────────────┐     ┌─────────────────┐
│   Pre-commit    │     │    Any CI       │     │    Release      │
│     Hooks       │     │    System       │     │    Process      │
├─────────────────┤     ├─────────────────┤     ├─────────────────┤
│ On commit:      │     │ On push/PR:     │     │ On v* tag:      │
│ - cargo fmt     │     │ - make lint     │     │ - Verify version│
│ - cargo clippy  │     │ - make test     │     │ - Extract notes │
│ - cargo test    │     │ - make test-all │     │ - Build release │
│ - gitleaks      │     │ - make audit    │     │ - Tag artifacts │
│                 │     │                 │     │                 │
├─────────────────┤     └─────────────────┘     └─────────────────┘
│ On push:        │              │
│ - make test-all │              ▼
│ - make doc      │     ┌─────────────────┐
└─────────────────┘     │    Makefile     │
         │              │ (Single Source) │
         └──────────────┤ - test          │
                        │ - test-all      │
                        │ - lint          │
                        │ - fmt           │
                        │ - audit         │
                        │ - ci            │
                        └─────────────────┘
```

## Quick Setup

To set up a complete pipeline for a project:

### 1. Prerequisites

Ensure the project has:
- `Cargo.toml` with proper package metadata
- A `tests/` directory for integration tests (unit tests typically in `src/`)
- Rust toolchain installed via rustup

### 2. Install Required Tools

```bash
# Install cargo tools for CI
rustup component add clippy rustfmt
cargo install cargo-deny cargo-audit cargo-tarpaulin
```

### 3. Create Required Files

Create these files from the reference templates:

| File | Purpose |
|------|---------|
| `Makefile` | Test command definitions |
| `.pre-commit-config.yaml` | Git hooks configuration |
| `deny.toml` | Dependency auditing rules |
| `CHANGELOG.md` | Release notes |

### 4. Install Hooks

```bash
pip install pre-commit
make install-hooks
```

## Detailed Configuration

### Makefile Setup

The Makefile defines all test and lint commands. Customize for your project structure:

```makefile
CRATE_NAME := your_crate_name
```

Key targets:
- `make test`: Fast unit tests for pre-commit (~seconds)
- `make test-all`: Full test suite with all features
- `make lint`: Clippy linting with strict settings
- `make fmt`: Check and fix formatting
- `make audit`: Security audit with cargo-deny
- `make ci`: Full CI suite (lint + all tests + audit)
- `make install-hooks`: Install git hooks
- `make coverage`: Generate test coverage report

See `references/makefile-template.md` for the complete template.

### Pre-commit Configuration

The `.pre-commit-config.yaml` runs:

**On every commit** (fast, <30s):
- rustfmt formatting check
- Clippy linting (fast mode)
- Gitleaks secret detection
- Fast unit tests via `make test`

**On push only** (thorough):
- Full test suite via `make test-all`
- Documentation build via `make doc`

Customize hooks based on your project needs.

See `references/pre-commit-config.yaml` for the template.

### CI Integration

Since GitHub Actions is not available, use the Makefile targets with your CI system:

**Jenkins Pipeline Example:**
```groovy
pipeline {
    agent any
    stages {
        stage('CI') {
            steps {
                sh 'make ci'
            }
        }
    }
}
```

**GitLab CI Example:**
```yaml
ci:
  script:
    - make ci
```

**Any CI System:**
```bash
# Single command runs all checks
make ci
```

### Cargo Deny Configuration

The `deny.toml` file configures dependency auditing:

- **Licenses**: Whitelist allowed licenses
- **Bans**: Block problematic dependencies
- **Advisories**: Check for security vulnerabilities
- **Sources**: Restrict dependency sources

See `references/deny-config.toml` for the template.

### Changelog Format

Maintain a `CHANGELOG.md` with version sections:

```markdown
# Changelog

## [1.2.0] - 2024-12-10
### Added
- New feature X

### Fixed
- Bug in Y
```

See `references/changelog-format.md` for detailed formatting guidelines.

### Cargo.toml Configuration

Ensure your `Cargo.toml` includes:

```toml
[package]
name = "your-crate"
version = "1.0.0"  # Single source of truth

[features]
default = []
integration-tests = []

[lints.rust]
unsafe_code = "forbid"

[lints.clippy]
all = "warn"
pedantic = "warn"
```

See `references/cargo-example.toml` for complete configuration.

## Release Process

To create a new release:

1. **Update changelog** with all changes since last release
2. **Bump version** in `Cargo.toml`
3. **Commit**: `git commit -m "chore: bump version to X.Y.Z"`
4. **Tag**: `git tag -a vX.Y.Z -m "Release vX.Y.Z - description"`
5. **Push**: `git push origin main && git push origin vX.Y.Z`
6. **Build release**: `make release` (creates optimized binaries)

See `references/release-process.md` for detailed instructions.

## Troubleshooting

### Pre-commit hooks not running
```bash
pre-commit install
pre-commit install --hook-type pre-push
```

### Clippy warnings in CI but not locally
Ensure you have the same Rust version. Pin in `rust-toolchain.toml`:
```toml
[toolchain]
channel = "1.75.0"
components = ["rustfmt", "clippy"]
```

### Cargo deny fails with unknown advisory
Update the advisory database:
```bash
cargo deny fetch
```

### Skip hooks temporarily
```bash
git commit --no-verify -m "message"
git push --no-verify
```

### Test features not found
Ensure feature flags are consistent between Cargo.toml and test commands.

## Test Categories and Policy

The pipeline uses test categories with clear boundaries:

| Category | Speed | I/O | Dependencies | When Run |
|----------|-------|-----|--------------|----------|
| **Unit** | <100ms each | None | All mocked | Every commit |
| **Integration** | <1s each | Mocked | Real components, mocked external | Every push |
| **E2E** | Minutes | Real | Full system | Manual / Release |

### Unit Tests (`src/` with `#[cfg(test)]`)

Pure logic tests with no I/O. All dependencies mocked.

- **Purpose**: Verify individual functions/modules in isolation
- **Speed**: Must complete in <100ms per test, total suite <10s
- **Mocking**: Mock everything external (DB, APIs, file system)
- **Run**: `make test` (pre-commit)

```rust
#[cfg(test)]
mod tests {
    use super::*;
    use mockall::predicate::*;

    #[test]
    fn test_calculate_total() {
        // Pure logic test
        assert_eq!(calculate_total(&[10, 20, 30]), 60);
    }
}
```

### Integration Tests (`tests/`)

Test component interactions with mocked external services.

- **Purpose**: Verify components work together correctly
- **Speed**: <1s per test, real component wiring but mocked I/O
- **Run**: `make test-all` (pre-push, CI)

```rust
// tests/integration_test.rs
#[test]
fn test_user_workflow() {
    let mock_api = MockApiClient::new();
    let result = create_and_fetch_user(&mock_api, "test@example.com");
    assert_eq!(result.email, "test@example.com");
}
```

### Directory Structure

```
your-crate/
├── Cargo.toml
├── Makefile
├── .pre-commit-config.yaml
├── deny.toml
├── CHANGELOG.md
├── src/
│   ├── lib.rs           # With #[cfg(test)] mod tests
│   └── main.rs
├── tests/               # Integration tests
│   ├── integration.rs
│   └── common/
│       └── mod.rs       # Shared test utilities
└── benches/             # Benchmarks (optional)
    └── benchmark.rs
```

## Resources

This skill includes reference templates in the `references/` directory:

### references/

- **pre-commit-config.yaml**: Pre-commit hooks configuration template
- **makefile-template.md**: Makefile with all standard targets
- **deny-config.toml**: Cargo deny configuration
- **changelog-format.md**: Changelog formatting guide
- **cargo-example.toml**: Complete Cargo.toml configuration
- **release-process.md**: Step-by-step release instructions
- **rust-toolchain-example.toml**: Rust version pinning

To use: Read the relevant template, customize placeholders (marked with `{{PLACEHOLDER}}`), and save to your project.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lukacf) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
