---
name: changelog-fragment
description: > Use when this capability is needed.
metadata:
  author: konveyor
---

# Generate Changelog Fragment

Create a changelog fragment file documenting the current changes.

## Instructions

1. **Determine what changed** by reviewing recent git diffs or the work done in this session
2. **Choose the kind**:
   - `feature` — new functionality
   - `bugfix` — bug fix
   - `enhancement` — improvement to existing functionality
   - `deprecation` — deprecated functionality
   - `breaking` — breaking change
3. **Write a concise description** — one clear sentence explaining what changed and why
4. **Determine which extension(s) are affected** by looking at which files changed:
   - Changes in `vscode/core/`, `webview-ui/`, `shared/`, `agentic/` → `core`
   - Changes in `vscode/java/` → `java`
   - Changes in `vscode/javascript/` → `javascript`
   - Changes in `vscode/go/` → `go`
   - Changes in `vscode/csharp/` → `csharp`
   - Changes in `vscode/konveyor/` → `konveyor`
   - If the change only affects `core`, omit the `extensions` field (it defaults to core)
   - If the change affects non-core extensions, or multiple extensions, include `extensions`
5. **Name the file** `changes/unreleased/<short-description>.yaml`
   - Use a descriptive kebab-case name like `fix-socket-path-limit.yaml` or `add-dark-mode.yaml`
6. **Write the fragment** using this format:

```yaml
kind: <kind>
description: >
  <description>.
```

Or with explicit extension targeting:

```yaml
kind: <kind>
description: >
  <description>.
extensions:
  - java
```

## Rules

- **Ignore test-only changes**: If the changes are exclusively in the `tests/` folder (e.g. adding or updating E2E tests), do not create a changelog fragment — these are not user-facing changes
- Description must be a single sentence, ending with a period
- Use active voice: "Fixed X" not "X was fixed", "Added Y" not "Y has been added"
- Be specific: "Fixed crash when analyzer encounters empty rulesets" not "Fixed a bug"
- Do not include PR numbers in the description (they are derived from the filename)
- Valid extensions: `core`, `java`, `javascript`, `go`, `csharp`, `konveyor`
- Omit `extensions` for core-only changes (it defaults to core)

## Examples

For a core bug fix:

**File**: `changes/unreleased/fix-sso-auth.yaml`

```yaml
kind: bugfix
description: >
  Fixed authentication flow when using SSO providers with custom certificates.
```

For a Java extension feature:

**File**: `changes/unreleased/add-java-provider.yaml`

```yaml
kind: feature
description: >
  Added support for custom Java external provider configuration.
extensions:
  - java
```

For a change affecting multiple extensions:

**File**: `changes/unreleased/shared-api-update.yaml`

```yaml
kind: enhancement
description: >
  Updated provider registration API for improved language extension compatibility.
extensions:
  - core
  - java
  - go
```

## Validation

After creating the fragment, validate it:

```bash
node scripts/changelog.js validate
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/konveyor) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
