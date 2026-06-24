---
name: release
description: > Use when this capability is needed.
metadata:
  author: signageos
---

# Skill: Release vscode-sops Extension

Bumps the version, builds, publishes to VS Code Marketplace and Open VSX, then creates a GitHub Release.

## When to Apply

- **"release"**, **"publish"**, **"bump and publish"**, **"ship the extension"**
- NOT applicable for pre-release/beta builds or individual CI steps

## Prerequisites

- [ ] Working directory is the `vscode-sops` repository root
- [ ] `master` branch is clean (no uncommitted changes)
- [ ] `sops` CLI is available (needed to decrypt `.env` for OVSX token)
- [ ] `vsce` CLI available (`npx vsce` from node_modules)
- [ ] `npx ovsx` available
- [ ] Node.js available via nvm (use `nvm use 22` or latest LTS)
- [ ] Node.js dependencies installed (`npm install` — use `--registry https://registry.npmjs.org` if the private CodeArtifact registry token is expired)
- [ ] `gh` CLI for creating GitHub Release (install locally if needed; authenticate with `GH_TOKEN` from `~/dev/signageos/projects/.env.secrets`)
- [ ] GitHub token available in `~/dev/signageos/projects/.env.secrets` as `GITHUB_TOKEN`

---

## Workflow

### Step 1: Determine Next Version

1. Read current version from `package.json` (field `"version"`).
2. Check recent bump commits to understand the versioning pattern:
   ```bash
   git --no-pager log --oneline --grep="^Bump" -10
   ```
   Historical pattern: `0.9.1` → `0.9.2` → `0.9.3` — **patch bump** is the default.
3. Compute next patch version: `X.Y.Z` → `X.Y.(Z+1)`.
4. Confirm with the user before proceeding:
   - Show current version and proposed next version.
   - Ask whether to use patch / minor / major if context suggests otherwise.

---

### Step 2: Update CHANGELOG.md

1. Open `CHANGELOG.md`.
2. Rename the `## [Unreleased]` section header to `## [X.Y.Z]` (the new version).
3. Insert a fresh empty `## [Unreleased]` section above it.
4. If `[Unreleased]` is empty, **stop and ask the user** what to put in the release notes before continuing.

Example before:
```markdown
## [Unreleased]
### Fixed
- Support for files parsed as `null`

## [0.9.3]
```

Example after:
```markdown
## [Unreleased]

## [0.9.4]
### Fixed
- Support for files parsed as `null`

## [0.9.3]
```

---

### Step 3: Bump Version in package.json and package-lock.json

Update the `"version"` field in both files from the old version to the new version:

```bash
npm version X.Y.Z --no-git-tag-version
```

This updates `package.json` and `package-lock.json` atomically without creating a git tag yet.

---

### Step 4: Build — `vscode:prepublish`

```bash
npm run vscode:prepublish
```

**On failure — STOP immediately.** Show:
- The full compiler/linter output.
- Likely cause (TypeScript error, import error, lint rule).
- Suggestion to fix (e.g., "Run `npm run lint` to see all lint issues", or show the TS error with file/line).
- Ask the user to fix the issue and then re-run this skill (or confirm to retry after they fix it).

Do **not** proceed to publishing if the build fails.

---

### Step 5: Publish to VS Code Marketplace — `vsce publish`

```bash
npx vsce publish
```

#### Token expiration handling

If the command fails with an error like `Failed to publish: 401 Unauthorized`, `The Personal Access Token used has expired`, or similar auth error:

1. **Stop and display this guidance:**

```
⚠️  VS Code Marketplace token has expired or is invalid.

To regenerate:
1. Go to: https://dev.azure.com/signageos/_usersSettings/tokens
   (Make sure you are signed in under a user that has access to the signageos organization)
2. Create a new token with:
   - Organization: All accessible organizations (or "signageos")
   - Scopes: Click "Show all scopes", then enable Marketplace → Manage (not just Read)
   - Expiration: up to 1 year
3. Copy the token.
4. Run: npx vsce login signageos
   (Enter the new token when prompted — it is stored in ~/.vsce)

After completing the above, respond with "continue" or "done" to resume.
```

2. Wait for the user to confirm they have re-authenticated.
3. Retry `npx vsce publish`.

#### Other publish failures

- Show full output.
- Suggest fix based on error message (missing README, invalid manifest, etc.).
- Stop and wait for confirmation before retrying.

---

### Step 6: Publish to Open VSX Registry — `ovsx:publish`

The script (`tools/ovsx-publish.bash`) decrypts `.env` with `sops`, sources it, and runs `npx ovsx publish -p $OVSX_TOKEN`.

**Note:** If `npm run ovsx:publish` fails because `npx ovsx` tries to install from the private CodeArtifact registry and gets a 401, run the publish manually with the public registry:

```bash
sops -d .env > .decrypted~.env
source .decrypted~.env
npx --registry https://registry.npmjs.org ovsx publish -p $OVSX_TOKEN
rm -f .decrypted~.env
```

#### Token expiration handling

If the command fails with `Unauthorized`, `invalid_token`, `401`, or similar:

1. **Stop and display this guidance:**

```
⚠️  Open VSX token has expired or is invalid.

To regenerate:
1. Go to: https://open-vsx.org → Log in → User Settings → Access Tokens
2. Generate a new token and copy it.
3. Update the OVSX_TOKEN value in the encrypted `.env` file:
   - Decrypt: sops -d .env > .decrypted~.env
   - Edit .decrypted~.env and set OVSX_TOKEN=<new-token>
   - Re-encrypt: sops -e .decrypted~.env > .env
   - Remove plaintext: rm .decrypted~.env
   - Commit the updated .env: git add .env && git commit -m "chore: rotate OVSX token"

After completing the above, respond with "continue" or "done" to resume.
```

2. Wait for the user to confirm they have updated the token.
3. Retry `npm run ovsx:publish`.

---

### Step 7: Commit, Tag, and Push

```bash
git add package.json package-lock.json CHANGELOG.md
git commit -m "Bump X.Y.Z"
git tag -a "vX.Y.Z" -m "Release vX.Y.Z"
git push github master
git push github "vX.Y.Z"
```

**Note:** The primary remote for GitHub is `github` (not `origin`, which points to GitLab). Check with `git remote -v` if unsure.

---

### Step 8: Create GitHub Release

1. Extract the new version's changelog section from `CHANGELOG.md` (everything between `## [X.Y.Z]` and the next `## [` heading).
2. Load the GitHub token:
   ```bash
   export GH_TOKEN=$(grep GITHUB_TOKEN ~/dev/signageos/projects/.env.secrets | cut -d= -f2)
   ```
3. Create the release via `gh` CLI:

```bash
gh release create "vX.Y.Z" \
  --repo signageos/vscode-sops \
  --title "vX.Y.Z" \
  --notes "<changelog content>"
```

**Note:** If `gh` CLI is not installed, download it to `/tmp`:
```bash
curl -sL https://github.com/cli/cli/releases/latest/download/gh_*_linux_amd64.tar.gz -o /tmp/gh.tar.gz
tar xzf /tmp/gh.tar.gz -C /tmp
export PATH="/tmp/gh_*_linux_amd64/bin:$PATH"
```

**Release body format** — mirror existing releases (e.g. v0.9.3):
- Use the changelog section as-is (Markdown headings like `### Fixed`, `### Added`, etc.).
- Keep it concise and readable.

Example:
```markdown
### Fixed
- Support for files parsed as `null`
```

---

## Error Handling Reference

| Error | Action |
|-------|--------|
| TypeScript compile error | Stop, show error + file/line, suggest fix, wait |
| ESLint / lint error | Stop, show rule + file, suggest `npm run lint`, wait |
| `vsce publish` 401 / token expired | Show Azure DevOps PAT regeneration steps, wait |
| `ovsx publish` 401 / token expired | Show Open VSX token rotation steps (sops), wait |
| `git push` rejected | Stop, show reason (e.g., non-fast-forward), suggest `git pull --rebase`, wait |
| `gh release create` fails | Stop, show error, suggest verifying `gh auth status`, wait |
| Any other unexpected error | Stop, show full output, state likely cause and suggestion, wait for user |

---

## Output Summary

After successful completion, report:

```
✅ Released vscode-sops vX.Y.Z

- CHANGELOG.md updated
- package.json / package-lock.json bumped
- Built with vscode:prepublish
- Published to VS Code Marketplace (signageos.signageos-vscode-sops)
- Published to Open VSX Registry
- Git tag: vX.Y.Z pushed
- GitHub Release: https://github.com/signageos/vscode-sops/releases/tag/vX.Y.Z
```

---
> Source: [signageos/vscode-sops](https://github.com/signageos/vscode-sops) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-20 -->
