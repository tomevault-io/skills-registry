---
name: git-workflow
description: >- Use when this capability is needed.
metadata:
  author: smittysmee
---

## Instructions
When working with Git repositories:

1. **Read credentials first** using `kybernos/secrets_read`:
   - Read the token from your available secrets (check the system prompt for secret names and keys)
   - You will use this token for both git CLI auth and GitHub API calls

2. **Clone the repository** using `shell_exec`:
   ```
   git clone https://github.com/org/repo.git /workspace/repo
   ```
   For private repos (or to enable push), embed the token in the URL:
   ```
   git clone https://<TOKEN>@github.com/org/repo.git /workspace/repo
   ```
   Replace `<TOKEN>` with the value you read from secrets.

3. **Configure git for push** (if you need to push later):
   ```
   cd /workspace/repo
   git config user.email "agent@kybernos.io"
   git config user.name "Kybernos Agent"
   git remote set-url origin https://<TOKEN>@github.com/org/repo.git
   ```

4. **Create a feature branch** before making changes:
   ```
   cd /workspace/repo && git checkout -b feature/description
   ```
   Use descriptive branch names: `feature/add-auth`, `fix/login-bug`, etc.

5. **Make changes** using file_write and file_edit tools, then stage and commit:
   ```
   git add -A
   git commit -m "descriptive commit message"
   ```
   Write clear, concise commit messages describing *why*, not just *what*.

6. **Push to remote**:
   ```
   git push -u origin feature/description
   ```

7. **Create a pull request** via GitHub API using `http_request`:
   ```
   POST https://api.github.com/repos/{owner}/{repo}/pulls
   Authorization: Bearer <TOKEN>
   {
     "title": "Short descriptive title",
     "body": "## Summary\n- Change 1\n- Change 2",
     "head": "feature/description",
     "base": "main"
   }
   ```
   Use the same token from step 1 in the Authorization header.

8. **Check CI status** after creating the PR:
   ```
   GET https://api.github.com/repos/{owner}/{repo}/commits/{sha}/check-runs
   Authorization: Bearer <TOKEN>
   ```

## Error Recovery
- **Authentication failures**: Re-read the secret using `kybernos/secrets_read`.
  Check that you are using the correct secret name and key (listed in your system
  prompt under Available Secrets). If the key is wrong, the error will list
  available keys.
- **Clone fails**: Ensure the token is embedded in the clone URL for private repos.
  If the repo is public, try cloning without the token first.
- **Merge conflicts**: Run `git status` to identify conflicting files. Resolve
  conflicts in each file using file_read + file_edit, then `git add` and
  `git commit`. Never force-push without explicit user approval.
- **Push rejected (non-fast-forward)**: Pull latest changes first:
  `git pull --rebase origin main`, then push again.
- **Rate limiting**: GitHub API returns 403 with `X-RateLimit-Remaining: 0`.
  Wait until `X-RateLimit-Reset` timestamp before retrying.

## Example: Full PR Workflow

Here is the complete sequence for "Clone repo X, add a README, and create a PR":

1. Read credentials:
   - Tool: `kybernos/secrets_read` with `name: "github-token"`, `key: "token"`
   - Result: `ghp_abc123...`

2. Clone with token:
   - Tool: `shell_exec` with `command: "git clone https://ghp_abc123@github.com/org/repo.git /workspace/repo"`

3. Configure git and create branch:
   - Tool: `shell_exec` with `command: "cd /workspace/repo && git config user.email 'agent@kybernos.io' && git config user.name 'Kybernos Agent' && git checkout -b docs/add-readme"`

4. Create the file:
   - Tool: `file_write` with `path: "/workspace/repo/README.md"`, `content: "# Repo\n\nDescription here."`

5. Stage, commit, push:
   - Tool: `shell_exec` with `command: "cd /workspace/repo && git add README.md && git commit -m 'docs: add README' && git push -u origin docs/add-readme"`

6. Create the PR:
   - Tool: `http_request` with `method: "POST"`, `url: "https://api.github.com/repos/org/repo/pulls"`, `headers: {"Authorization": "Bearer ghp_abc123"}`, `body: {"title": "docs: add README", "body": "Adds a project README.", "head": "docs/add-readme", "base": "main"}`

## Guardrails
- NEVER commit secrets, credentials, or API keys to version control
- NEVER force-push to main/master branches
- Always create a feature branch — never commit directly to main
- Maximum 10 commits per task
- Always check `git status` before committing to avoid staging unwanted files
- Use `.gitignore` to exclude build artifacts, dependencies, and secrets

---
> Source: [smittysmee/kybernos](https://github.com/smittysmee/kybernos) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
