---
name: semgrep-sast-scan
description: > Use when this capability is needed.
metadata:
  author: vchirrav
---

# Semgrep SAST Scan Skill

Run SAST scans on source code repositories using Semgrep Code, connected to the organization's
Semgrep AppSec Platform cloud instance.

## Prerequisites

Before running a scan, verify the following environment variables are configured. If any are
missing, inform the user and point them to the **Environment Setup** section below.

| Variable | Required | Purpose |
|---|---|---|
| `SEMGREP_APP_TOKEN` | **Yes** | Auth token from Semgrep AppSec Platform (Settings > Tokens) |
| `SEMGREP_APP_URL` | No | Defaults to `https://semgrep.dev`. Set only for single-tenant deployments. |
| `SEMGREP_REPO_NAME` | No | Override auto-detected repo name for findings linking |
| `SEMGREP_TIMEOUT` | No | Per-file scan timeout in seconds (default: 5) |

### Quick check

Run the preflight script to validate the environment before scanning:

```bash
bash /path/to/semgrep-sast-scan/scripts/preflight.sh
```

If the preflight fails, do NOT proceed with the scan — resolve the issues first.

## How to Run a SAST Scan

### Step 1: Preflight

Always run the preflight check first:

```bash
bash <skill-dir>/scripts/preflight.sh
```

This validates that `semgrep` is installed, `SEMGREP_APP_TOKEN` is set, the current directory
is a Git repository, and the token authenticates successfully against the Semgrep platform.

### Step 2: Execute the scan

Run the scan script from the **root of the target project**:

```bash
# Full SAST scan (recommended for main/default branches and initial scans)
bash <skill-dir>/scripts/run-sast-scan.sh

# Diff-aware scan (for pull requests — only scans changed files)
bash <skill-dir>/scripts/run-sast-scan.sh --diff-aware

# Full scan with cross-file analysis enabled
bash <skill-dir>/scripts/run-sast-scan.sh --cross-file

# Scan with JSON output saved to a file
bash <skill-dir>/scripts/run-sast-scan.sh --output json

# Scan with SARIF output (for GitHub Advanced Security Dashboard integration)
bash <skill-dir>/scripts/run-sast-scan.sh --output sarif
```

### Step 3: Review results

After the scan completes:

1. **Terminal output** shows a summary of findings by severity.
2. **Semgrep AppSec Platform** at https://semgrep.dev/orgs/vchirrav_personal_org has the full
   findings dashboard with code hyperlinks, triage tools, and AI-assisted remediation.
3. If `--output json` or `--output sarif` was used, the report file is saved in the current
   directory as `semgrep-sast-results.<format>`.

## Scan Modes Explained

**Full scan** (`semgrep ci --code`): Scans the entire codebase. Use for default/main branches
and scheduled nightly scans. Reports all findings.

**Diff-aware scan** (`semgrep ci --code` with `SEMGREP_BASELINE_REF`): Scans only files changed
since the baseline ref. Use for PR/MR scans to report only newly introduced findings.

**Cross-file analysis**: Enables interprocedural analysis across files and functions. Produces
fewer false positives and more true positives but takes longer. Enable in the Semgrep AppSec
Platform under Settings > General > Code, or pass `--cross-file` to the scan script.

## Environment Setup for Project Teams

Project teams need to do the following one-time setup to use this skill:

### 1. Generate a SEMGREP_APP_TOKEN

1. Sign in to https://semgrep.dev/orgs/vchirrav_personal_org
2. Go to **Settings > Tokens**
3. Click **Create new token**
4. Copy the token value

### 2. Configure the token in your environment

**For Claude Code / local development:**

Add to your shell profile (`~/.bashrc`, `~/.zshrc`, or equivalent):

```bash
export SEMGREP_APP_TOKEN="your-token-here"
```

Or create a `.env` file in your project root (add `.env` to `.gitignore`!):

```bash
SEMGREP_APP_TOKEN=your-token-here
```

**For CI/CD (GitHub Actions):**

1. Go to your repo Settings > Secrets and variables > Actions
2. Add a new repository secret: `SEMGREP_APP_TOKEN` with the token value
3. Reference it in your workflow:

```yaml
env:
  SEMGREP_APP_TOKEN: ${{ secrets.SEMGREP_APP_TOKEN }}
```

**For CI/CD (GitLab CI):**

1. Go to Settings > CI/CD > Variables
2. Add `SEMGREP_APP_TOKEN` as a masked variable

**For CI/CD (Other providers):**

Set `SEMGREP_APP_TOKEN` as a secret/environment variable in your CI provider's configuration.
See https://semgrep.dev/docs/semgrep-ci/sample-ci-configs for provider-specific examples.

### 3. Install Semgrep CLI

```bash
# macOS
brew install semgrep

# pip (any OS)
pip install semgrep

# Docker (no local install needed)
docker run --rm -v "${PWD}:/src" semgrep/semgrep semgrep ci --code
```

### 4. Optional environment variables

```bash
# Override the repo display name in Semgrep AppSec Platform
export SEMGREP_REPO_DISPLAY_NAME="my-service-name"

# Increase per-file timeout for large files (default 5s)
export SEMGREP_TIMEOUT=30

# For diff-aware scans, set the baseline branch
export SEMGREP_BASELINE_REF="origin/main"
```

## Troubleshooting

| Symptom | Fix |
|---|---|
| `Error: Not logged in` | Ensure `SEMGREP_APP_TOKEN` is exported in your shell |
| `No findings reported to platform` | Check that `SEMGREP_APP_TOKEN` is valid and not expired |
| Scan times out on large files | Increase `SEMGREP_TIMEOUT` (e.g., `export SEMGREP_TIMEOUT=60`) |
| Findings don't show code links | Set `SEMGREP_REPO_URL` and `SEMGREP_REPO_NAME` explicitly |
| Different findings in CI vs local | Ensure same Semgrep version; CI uses diff-aware by default |

## Reference

- Semgrep AppSec Platform: https://semgrep.dev/orgs/vchirrav_personal_org
- Semgrep Docs: https://semgrep.dev/docs
- CI Environment Variables: https://semgrep.dev/docs/semgrep-ci/ci-environment-variables
- Sample CI Configs: https://semgrep.dev/docs/semgrep-ci/sample-ci-configs

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/vchirrav) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
