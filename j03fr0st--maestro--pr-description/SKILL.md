---
name: pr-description
description: > Use when this capability is needed.
metadata:
  author: j03fr0st
---

# PR Description Generator

Generate comprehensive pull request descriptions by analyzing git changes. Works with both **GitHub** and **Azure DevOps**.

## Workflow

### 1. Analyze Changes

```bash
# View all changes vs base branch
git log main..HEAD --oneline
git diff main...HEAD --stat

# View staged/unstaged changes if uncommitted work exists
git diff --cached
git diff
```

### 2. Categorize Changes

Group changes into logical categories:

| Category | Examples |
|----------|----------|
| Dependencies | package.json, requirements.txt, *.csproj |
| Build Config | webpack.config.js, tsconfig.json, Dockerfile |
| Pipeline | .github/workflows/*, azure-pipelines.yml |
| Code | src/*, lib/*, app/* |
| Tests | *.spec.ts, *.test.js, tests/* |
| Documentation | README.md, docs/*, CHANGELOG.md |

### 3. Generate Description

Scale the template to match the PR size. For large PRs, use the full format. For small or trivial PRs (typo fix, dependency bump, single-file change), use just the Summary and omit empty sections.

#### Full format (multi-file, feature work)

```markdown
## Summary
2-3 sentences describing the overall purpose and impact of the changes.

## Related Work Items
- GitHub: Fixes #123, Relates to #456
- Azure DevOps: AB#12345, AB#67890

## Changes

### [Category Name]
- Specific change with details (file paths, version numbers)

### [Another Category]
- Change details

## Change Type
- [ ] New Feature
- [ ] Bug Fix
- [ ] Dependency Updates
- [ ] Build Configuration
- [ ] Pipeline Configuration
- [ ] Code Refactoring
- [ ] Test Addition
- [ ] Documentation
- [ ] Breaking Change

## Risks & Considerations
- Any potential risks or breaking changes
- Areas requiring extra review attention
- Migration steps if applicable
```

#### Lightweight format (trivial PRs)

```markdown
## Summary
One sentence describing the change.

## Change Type
- [x] Bug Fix
```

> **Work Item Linking:**
> - **GitHub**: Use `Fixes #123` or `Closes #123` to auto-close issues
> - **Azure DevOps**: Use `AB#12345` syntax to link work items in the description

## Title Format

Use conventional commit format: `<type>: <brief description>`

| Type | Use For |
|------|---------|
| `feat` | New features |
| `fix` | Bug fixes |
| `refactor` | Code restructuring |
| `chore` | Maintenance tasks |
| `docs` | Documentation only |
| `test` | Test additions/changes |
| `build` | Build system changes |
| `ci` | CI/CD changes |

## Description Guidelines

1. **Be Specific**: Include version numbers, package names, file paths
2. **Explain Why**: Focus on the purpose, not just what changed
3. **Highlight Risks**: Call out breaking changes or areas needing attention
4. **Link Work Items**:
   - GitHub: `Fixes #123` or `Closes #456`
   - Azure DevOps: `AB#12345` (auto-links to work item)

## Examples

### Example 1: Feature PR

```markdown
## Summary
Updates authentication system to use JWT tokens instead of session cookies, improving
scalability and enabling stateless API design. Also updates related dependencies and
adds comprehensive test coverage.

## Related Work Items
AB#4521, AB#4522

## Changes

### Dependencies
- Updated `jsonwebtoken` from 8.5.1 to 9.0.0
- Added `@types/jsonwebtoken` 9.0.0
- Removed `express-session` (no longer needed)

### Code
- Replaced session-based auth in `src/auth/middleware.ts`
- Added JWT token generation in `src/auth/tokens.ts`
- Updated all protected routes to use new middleware

### Tests
- Added unit tests for token generation (`src/auth/tokens.spec.ts`)
- Added integration tests for auth flow (`tests/auth.integration.ts`)

## Change Type
- [x] New Feature
- [x] Dependency Updates
- [x] Code Refactoring
- [x] Test Addition
- [x] Breaking Change

## Risks & Considerations
- **Breaking Change**: Existing sessions will be invalidated on deploy
- **Migration**: Users will need to re-authenticate after deployment
- **Security**: JWT secret must be configured in environment variables
```

### Example 2: Trivial PR

```markdown
## Summary
Fix typo in error message shown when database connection times out.

## Change Type
- [x] Bug Fix
```

## Creating the PR

### Step 1: Detect Platform

```bash
# Check for GitHub remote
git remote -v | grep -i github

# Check for Azure DevOps remote
git remote -v | grep -i "dev.azure.com\|visualstudio.com"
```

### Step 2: Write Description to Temp File

Write the PR description to `.github/.tmp/pr-description.md` using the Write tool. Using a temp file avoids shell escaping issues with complex markdown content (backticks, quotes, special characters).

```bash
# Ensure the directory exists
mkdir -p .github/.tmp
```

### Step 3: Create PR Using the File

#### GitHub

```bash
# Create PR with description from file
gh pr create --title "<type>: <description>" --body-file .github/.tmp/pr-description.md

# Or update existing PR description
gh pr edit <PR_NUMBER> --body-file .github/.tmp/pr-description.md

# Create PR with specific base branch
gh pr create --base main --title "<type>: <description>" --body-file .github/.tmp/pr-description.md
```

#### Azure DevOps

```bash
# Create PR with description from file
az repos pr create \
  --title "<type>: <description>" \
  --description "$(cat .github/.tmp/pr-description.md)" \
  --source-branch "$(git branch --show-current)" \
  --target-branch main

# Or update existing PR description
az repos pr update \
  --id <PR_ID> \
  --description "$(cat .github/.tmp/pr-description.md)"
```

### Step 4: Clean Up

```bash
rm .github/.tmp/pr-description.md
```

> The `.github/.tmp/` directory should be in `.gitignore` to prevent committing temp files.

## Platform-Specific Notes

### GitHub

- Uses `gh` CLI (GitHub CLI)
- Supports `--body-file` for reading description from file
- Work items: Reference with `Fixes #123` or `Closes #123`

### Azure DevOps

- Uses `az repos` CLI (Azure CLI with DevOps extension)
- Requires reading file content with `$(cat file)` for `--description`
- Work items: Link with `--work-items <ID>` flag
- Auth: `az login` then `az devops configure --defaults organization=<ORG_URL> project=<PROJECT>`

```bash
# ADO: Create PR with linked work items
az repos pr create \
  --title "<type>: <description>" \
  --description "$(cat .github/.tmp/pr-description.md)" \
  --source-branch "$(git branch --show-current)" \
  --target-branch main \
  --work-items 12345 67890
```

## Quick Reference Commands

```bash
# View current branch changes vs main
git diff main...HEAD

# List files changed
git diff main...HEAD --name-only

# Show commit messages for PR
git log main..HEAD --format="- %s"

# View diff stats
git diff main...HEAD --stat

# Get current branch name
git branch --show-current
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/j03fr0st) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
