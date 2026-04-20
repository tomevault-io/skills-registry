---
name: secret-guard
description: > Use when this capability is needed.
metadata:
  author: controlnet
---

# Secret Guard

Prevent secret leakage via git. Cross-platform, Python stdlib only (no pip install).

Before running any bundled script, resolve `<skill-dir>` as the directory containing this `SKILL.md`. Do **not** assume the current working directory is the skill folder.

For `staged`, `tracked`, and `gitignore` modes, run the command with the current working directory set to the target git repository you want to audit.

## Pre-Commit Check (DEFAULT)

Run before ANY commit that touches config, env, auth, or infra files:

```bash
python "<skill-dir>/scripts/scan_secrets.py" staged
```

Exit 0 = clean, exit 1 = findings. If findings exist, **do NOT commit** — remove or move secrets to env vars first.

## Full Repo Audit

Scan all tracked files:

```bash
python "<skill-dir>/scripts/scan_secrets.py" tracked
```

## Gitignore Coverage Audit

Verify .gitignore covers common sensitive file patterns:

```bash
python "<skill-dir>/scripts/scan_secrets.py" gitignore
```

Reports which patterns (.env, *.pem, *.key, credentials.json, etc.) are NOT covered.

## When User Mentions Secrets/Credentials

If the user discusses API keys, tokens, passwords, or sensitive config:

1. Resolve `<skill-dir>` from this `SKILL.md`, then run `python "<skill-dir>/scripts/scan_secrets.py" staged`
2. Run `python "<skill-dir>/scripts/scan_secrets.py" gitignore` to verify .gitignore coverage
3. If findings: list them clearly with remediation steps
4. If clean: confirm no secrets detected

## Remediation Workflow

When secrets are found:

1. **Unstage the file**: `git reset HEAD <file>`
2. **Move secret to env var**: replace hardcoded value with `os.environ["KEY"]` / `process.env.KEY` etc.
3. **Add to .gitignore** if the file is inherently sensitive (.env, *.pem, credentials.json)
4. **If already committed**: warn the user that the secret is in git history and suggest `git filter-repo` or rotating the credential
5. Re-scan: `python "<skill-dir>/scripts/scan_secrets.py" staged`

## What Gets Detected

- **Sensitive files**: .env, *.pem, *.key, *.p12, credentials.json, id_rsa, kubeconfig, etc.
- **Content patterns**: AWS keys (AKIA...), GitHub tokens (ghp_/github_pat_), GCP API keys, Stripe keys, Slack tokens, private key blocks (-----BEGIN ... PRIVATE KEY-----), JWT tokens, database connection strings, password/secret variable assignments, and 30+ provider-specific patterns
- **Gitignore gaps**: checks whether common sensitive file types are covered by .gitignore rules

For the full pattern catalog, see [references/patterns.md](references/patterns.md).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/controlnet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
