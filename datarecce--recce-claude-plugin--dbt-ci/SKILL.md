---
name: dbt-ci
description: > Use when this capability is needed.
metadata:
  author: datarecce
---

# dbt CI

## GitHub Actions Steps

### Full Build (CD / Production Baseline)

Use for production baseline workflows where you build everything:

**If uv:**
```yaml
- name: Run dbt
  run: |
    uv run dbt deps
    uv run dbt build --target {TARGET}
    uv run dbt docs generate --target {TARGET}
```

**If pip:**
```yaml
- name: Run dbt
  run: |
    source .venv/bin/activate
    dbt deps
    dbt build --target {TARGET}
    dbt docs generate --target {TARGET}
```

### Incremental Build (CI / Pull Requests)

Use `--state` for state-based selection when comparing against a baseline:

**If uv:**
```yaml
- name: Run dbt (modified models only)
  run: |
    uv run dbt deps
    uv run dbt build --target {TARGET} --select state:modified+ --state {STATE_PATH}
    uv run dbt docs generate --target {TARGET} --select state:modified+ --state {STATE_PATH}
```

**If pip:**
```yaml
- name: Run dbt (modified models only)
  run: |
    source .venv/bin/activate
    dbt deps
    dbt build --target {TARGET} --select state:modified+ --state {STATE_PATH}
    dbt docs generate --target {TARGET} --select state:modified+ --state {STATE_PATH}
```

## Variables

| Variable | Description | Example |
|----------|-------------|---------|
| `{TARGET}` | dbt target/profile name | `prod`, `ci`, `dev` |
| `{STATE_PATH}` | Path to baseline manifest for comparison | `target-base` |

## State-Based Selection

- `state:modified` - Only models with code changes
- `state:modified+` - Modified models + downstream dependencies
- `+state:modified+` - Upstream + modified + downstream

Requires `--state <path>` pointing to a directory with baseline `manifest.json`.

## Best Practices

- Use `state:modified+` in CI to only rebuild what changed
- Always run `dbt docs generate` to create artifacts for comparison tools
- Match `--target` to your profiles.yml environment (prod, ci, dev)
- Ensure baseline manifest exists before using `--state` flag

## Related Skills

- Use `python-uv-ci` skill for Python/uv environment setup steps
- Use `python-pip-ci` skill for Python/pip environment setup steps
- Use `recce-cloud-ci` skill for Recce Cloud upload/download steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/datarecce) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
