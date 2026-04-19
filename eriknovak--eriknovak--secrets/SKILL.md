---
name: secrets
description: Audit codebase for secret handling issues. Scans for accidentally committed secrets, validates gitignore patterns, and ensures proper .env templates exist. Use when this capability is needed.
metadata:
  author: eriknovak
---

# Secrets Audit Skill

Audit your codebase for proper secret handling and identify potential security issues.

## Usage

Ask Claude to audit secrets:
- "Check for leaked secrets"
- "Audit secret handling"
- "Are my secrets safe?"
- "Scan for credentials"

Or use the slash command: `/secrets`

## What It Checks

### 1. Committed Secrets Detection
Scans git history and working tree for patterns that look like secrets:
- API keys (AWS, Google, Stripe, etc.)
- Tokens (JWT, OAuth, Bearer)
- Private keys (RSA, SSH, PGP)
- Passwords in config files
- Connection strings with credentials

### 2. Gitignore Validation
Verifies secret-related files are properly ignored:
- `.env` and `.env.*` files
- `credentials.json`, `secrets.yaml`
- Private key files (`*.pem`, `*.key`)
- Cloud config (`~/.aws/credentials`)

### 3. Template Verification
Ensures proper secret management patterns:
- `.env.example` exists when `.env` is used
- Template has all required keys (without values)
- Documentation for required secrets

### 4. Hardcoded Secret Detection
Scans source code for hardcoded credentials:
- String patterns matching secret formats
- Base64-encoded potential secrets
- URLs with embedded credentials

## Output

The skill provides:
- **Issues Found**: List of potential secret exposures
- **Risk Level**: Critical, High, Medium, Low
- **Recommendations**: Specific fixes for each issue
- **Best Practices**: General guidance for the project

## Example Output

```
=== Secrets Audit Report ===

CRITICAL: Found potential AWS key in src/config.js:42
  Pattern: AKIA[0-9A-Z]{16}
  Action: Rotate key immediately, remove from git history

HIGH: .env file not in .gitignore
  File: .env
  Action: Add to .gitignore, verify not committed

MEDIUM: Missing .env.example template
  Action: Create .env.example with required keys (no values)

LOW: Using environment variables correctly
  Files: src/db.js, src/api.js

=== Recommendations ===
1. Run: git filter-branch to remove secrets from history
2. Add comprehensive .gitignore patterns
3. Use secret manager for production (Vault, AWS Secrets Manager)
```

## Limitations

- Cannot detect secrets in binary files
- Pattern matching may have false positives
- Does not check external secret managers
- Git history scan limited to recent commits by default

## Related

- `block-secrets.sh` hook prevents Claude from reading secret files
- See reference.md for detailed patterns and remediation steps

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/eriknovak) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
