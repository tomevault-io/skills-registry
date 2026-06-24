---
name: rust-codebase-scanning
description: Mandatory pre-generation scan that reads actual project style from Cargo.toml, existing server fns, components, repo modules, error enums, and migrations. Run before the first code generation of every session. Use when this capability is needed.
metadata:
  author: adelabdelgawad
---

## When to Run

- **Mandatory**: before the first code generation in any session.
- **Recommended**: after any structural change (adding a crate to workspace, renaming
  modules, adding/removing features in Cargo.toml).
- **On demand**: `/rusty:analyze scanning`

The scan result is cached for the session at:
```
$CLAUDE_PLUGIN_DATA/scan-<session_id>.json
```

If the cache exists and is less than 2 hours old, the scan may be skipped. Otherwise,
re-run. The `pre-edit-gate` hook checks for the cache or a prior `Skill: rust-codebase-scanning`
invocation in the transcript; if neither is found, it blocks `.rs` file edits with a
remediation message.

For non-interactive environments (CI, hooks, scripts), the `scripts/scan.py` utility
performs the same scan and writes the same JSON output without requiring a Claude session.

---

## Phase 1 — STRUCTURE (~5 seconds)

Detect the project layout and confirm cargo-leptos is wired.

```bash
# 1. Single-crate vs workspace
grep -q '^\[workspace\]' Cargo.toml && echo "workspace" || echo "single-crate"

# 2. Workspace members (if workspace)
grep -A20 '^\[workspace\]' Cargo.toml | grep -oE '"[^"]*"' | tr -d '"'

# 3. Cargo.toml parseable?
python3 -c "import sys; sys.version_info >= (3,11) and __import__('tomllib').load(open('Cargo.toml','rb'))" \
  2>/dev/null || echo "WARN: Cargo.toml parse issue"

# 4. cargo-leptos config block
grep -q '^\[package\.metadata\.leptos\]' Cargo.toml && echo "found" || echo "absent"
grep -A20 '^\[package\.metadata\.leptos\]' Cargo.toml | grep -E \
  'output-name|site-addr|style-file|bin-features|lib-features|server-fn-prefix'

# 5. Features defined
grep -A20 '^\[features\]' Cargo.toml | grep -E '^(ssr|hydrate|default)\s*='
```

---

## Phase 2 — PATTERN EXTRACTION (~10 seconds)

Read exactly ONE representative sample of each pattern type. Do not read every file;
read the first match only. The goal is style detection, not exhaustive analysis.

### One server function
```bash
grep -rln '#\[server\]' src/ | head -1 | xargs grep -A15 '#\[server\]' | head -20
```
Extract: function name style, arg naming, return type, error type, encoding if specified.

### One component
```bash
grep -rln '#\[component\]' src/ | head -1 | xargs grep -A20 '#\[component\]' | head -25
```
Extract: prop struct shape (if any), signal creation pattern (`RwSignal::new` vs `signal()`),
`Resource` usage pattern.

### One repository module
```bash
ls src/db/*.rs 2>/dev/null | head -1
```
If found, read the first 60 lines. Extract: function signature style (`&PgPool` direct or
`impl Executor`), `query!` vs `query_as!` vs runtime `query`, transaction style.

### One error enum
```bash
grep -rln 'enum AppError\|enum DbError\|enum ServerError' src/ | head -1
```
If found, read it. Extract: variants, derives, `impl FromServerFnError`, `impl IntoResponse`.

### One migration
```bash
ls migrations/*.sql 2>/dev/null | tail -1
```
If found, read the first 30 lines. Extract: naming convention, destructive marker usage,
whether up+down or up-only.

---

## Phase 3 — STYLE PROFILE

Produce this table. Every row must have a value (use "not detected" if absent).

| Property | Detected Value |
|---|---|
| crate-layout | `single-crate` or `workspace (members: [...])`  |
| leptos-version | e.g., `0.8.2` |
| axum-version | e.g., `0.8.1` |
| sqlx-version | e.g., `0.9.0` |
| sqlx-offline-cache | `present` or `absent` |
| sqlx-toml | `present` or `absent` |
| features-ssr | e.g., `["leptos/ssr", "axum", "sqlx/postgres", ...]` |
| features-hydrate | e.g., `["leptos/hydrate"]` |
| AppError-enum | e.g., `src/error.rs` — variants: NotFound, Forbidden, Internal |
| AppError-derives | e.g., `thiserror::Error, Serialize, Deserialize, Clone` |
| AppError-from-server-fn | `impl present` or `absent` |
| server-fn-signature-style | e.g., `async fn name(arg: Type) -> Result<T, AppError>` |
| server-fn-encoding | e.g., `PostUrl` (default) or `GetJson` |
| component-vs-island-ratio | e.g., `12 components, 2 islands` |
| signal-pattern | e.g., `RwSignal::new` or `let (r, w) = signal(T)` |
| repo-generic-over-executor | `yes` or `no (direct &PgPool)` |
| transaction-style | e.g., `pool.begin().await?` or `not found` |
| migration-naming | e.g., `<14-digit-timestamp>_<snake>` or custom |
| auth-crate | e.g., `tower-sessions + axum-login` or `absent` |
| jobs-crate | e.g., `apalis` or `absent` |
| css-strategy | `tailwind` or `scss` or `none` |
| msrv | e.g., `1.94` or `not set` |
| cargo-leptos-output-name | e.g., `my_app` |
| cargo-leptos-site-addr | e.g., `127.0.0.1:3000` |

---

## Priority Rule

**The detected style profile overrides all skill-level defaults.**

If the project uses `GetJson` encoding for server fns, generate `GetJson` — not `PostUrl`.
If the project omits `updated_at` on entities, don't add it.
If the project uses `Arc<AppState>` with `FromRef` for sub-states, follow that.
If the project uses `#[cfg_attr(feature = "ssr", derive(sqlx::FromRow))]`, match it.

Only deviate from the detected style when:
1. The detected style violates a hard discipline rule (hydration, security, error handling).
2. The user explicitly requests a different style.

When deviating from detected style, always explain: "This project uses X, but Y is required
because of [rule]. Generating Y."

---

## Output Format

After completing all three phases, present:

```
=== Rust Codebase Scan Complete ===

Style Profile:
  crate-layout              : single-crate
  leptos-version            : 0.8.2
  axum-version              : 0.8.1
  sqlx-version              : 0.9.0
  sqlx-offline-cache        : present
  features-ssr              : leptos/ssr, axum, sqlx/postgres
  features-hydrate          : leptos/hydrate
  AppError-enum             : src/error.rs (NotFound, Forbidden, Internal)
  server-fn-signature-style : async fn name(arg: Type) -> Result<T, AppError>
  component-vs-island-ratio : 12 components, 0 islands
  repo-generic-over-executor: yes
  auth-crate                : absent
  jobs-crate                : absent

Warnings:
  - .sqlx/ cache absent. Run `cargo sqlx prepare --workspace` after next edit.

Scan cache written to: $CLAUDE_PLUGIN_DATA/scan-<session_id>.json

Ready for generation. Detected style will be used as defaults.
```

If any Phase 1 check fails (Cargo.toml unparseable, no `[features]` block, missing `ssr`
feature), surface it as a blocking warning before proceeding.

---

## Cache Format

The JSON written to `$CLAUDE_PLUGIN_DATA/scan-<session_id>.json`:

```json
{
  "session_id": "<session_id>",
  "scanned_at": "2026-05-25T12:00:00Z",
  "project_root": "/path/to/project",
  "style_profile": {
    "crate_layout": "single-crate",
    "leptos_version": "0.8.2",
    "axum_version": "0.8.1",
    "sqlx_version": "0.9.0",
    "sqlx_offline_cache": true,
    "sqlx_toml": false,
    "features_ssr": ["leptos/ssr", "axum", "sqlx/postgres"],
    "features_hydrate": ["leptos/hydrate"],
    "app_error_path": "src/error.rs",
    "app_error_variants": ["NotFound", "Forbidden", "Internal"],
    "server_fn_encoding": "PostUrl",
    "component_count": 12,
    "island_count": 0,
    "signal_pattern": "RwSignal::new",
    "repo_generic_executor": true,
    "auth_crate": null,
    "jobs_crate": null,
    "css_strategy": "none",
    "msrv": "1.94",
    "cargo_leptos_output_name": "my_app",
    "cargo_leptos_site_addr": "127.0.0.1:3000",
    "migration_naming": "timestamp_snake",
    "latest_migration_ts": "20260525110000"
  },
  "warnings": []
}
```

---

## Non-Interactive Use

For hooks, CI, or scripts, use the bundled utility:

```bash
python3 plugins/rusty/skills/rust-codebase-scanning/scripts/scan.py \
  --root /path/to/project \
  --output /path/to/scan.json \
  --session-id <id>
```

See `scripts/scan.py` for CLI reference. Requires Python 3.11+ (uses `tomllib` from stdlib).
On Python < 3.11, install `tomli` (`pip install tomli`) — the script will fall back to it.

---
> Source: [adelabdelgawad/rust-fullstack-agents](https://github.com/adelabdelgawad/rust-fullstack-agents) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
