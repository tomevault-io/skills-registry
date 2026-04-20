---
name: community-skills
description: >- Use when this capability is needed.
metadata:
  author: context-is-everything
---

# Community Skills

**Contribute and update skills in the context-is-everything/skills community repository.**

This skill automates the complete workflow for sharing skills with the Sasha community, handling all GitHub operations transparently using the pre-configured `github-community-token` from secretstore.

## What This Skill Does

The community-skills skill provides two primary operations:

### 1. **Contribute** - Add New Skills
Share a new skill you've created with the community by creating a pull request to the context-is-everything/skills repository.

### 2. **Update** - Improve Existing Skills
Submit updates, fixes, or improvements to skills already in the community repository.

## Value Proposition

### Why Use This Skill?

**For Contributors:**
- ✅ **Automated workflow** - No manual git operations required
- ✅ **Token management** - Uses pre-configured GitHub credentials
- ✅ **PR automation** - Creates professional pull requests automatically
- ✅ **Error handling** - Validates and handles common issues
- ✅ **Best practices** - Follows established contribution patterns

**For the Community:**
- 🌟 **Shared knowledge** - Make your skills available to all Sasha users
- 🔄 **Continuous improvement** - Easy updates keep skills current
- 📚 **Growing library** - Build collective Sasha capabilities
- 🤝 **Collaboration** - Contributions benefit everyone

### What Problems Does This Solve?

**Without this skill:**
- Manual git clone, branch, commit, push workflow
- Finding and copying GitHub token
- Writing PR descriptions and formatting
- Remembering repository structure
- Handling authentication errors

**With this skill:**
- Single command contribution workflow
- Automatic token retrieval from secretstore
- Generated PR descriptions with proper formatting
- Validated skill structure
- Clear error messages with solutions

## Quick Start

### Contribute a New Skill

```bash
# Simple contribution
./scripts/contribute_skill.sh contribute /path/to/your/skill

# With custom branch name
./scripts/contribute_skill.sh contribute /path/to/your/skill --branch add-awesome-feature
```

### Update an Existing Skill

```bash
# Simple update
./scripts/contribute_skill.sh update /path/to/existing/skill

# With custom commit message
./scripts/contribute_skill.sh update /path/to/skill --message "Fix typo in documentation"
```

## How It Works

### Prerequisites

**Automatically Configured:**
- ✅ GitHub token (`github-community-token` in secretstore)
- ✅ Repository access (pre-configured service account)
- ✅ Git credentials (handled by script)

**Required:**
- Valid skill directory with SKILL.md
- Skill follows proper structure

### Workflow Steps

Both operations follow this automated workflow:

0. **Validate Token** - Verify token has push permissions (prevents wasted work)
1. **Retrieve Token** - Securely fetch `github-community-token` from secretstore
2. **Clone Repository** - Clone context-is-everything/skills to /tmp
3. **Create Branch** - Generate feature branch with timestamp (cleanup existing if needed)
4. **Copy Skill Files** - Copy your skill to the repository
5. **Commit Changes** - Stage and commit with descriptive message
6. **Push Branch** - Push to GitHub using authenticated remote
7. **Create PR** - Submit pull request with generated description
8. **Report Success** - Provide PR URL for review

**Note:** Step 0 (token validation) is recommended but not currently automated. See "Before Contributing" best practices for manual validation.

### Token Management

The skill uses `github-community-token` from secretstore:

```bash
# Token is automatically retrieved
TOKEN=$(mcp__secretstore__secretstore_get github-community-token | jq -r '.secret.value')

# Used for git operations
git clone https://${TOKEN}@github.com/context-is-everything/skills.git
git push -u origin branch-name

# Used for GitHub API
curl -H "Authorization: token ${TOKEN}" https://api.github.com/repos/...
```

**Security:**
- Token never exposed in logs
- Stored encrypted in secretstore
- Only used for authenticated operations
- Automatically rotated every 90 days

## Usage Patterns

### Pattern 1: Contributing a Brand New Skill

**Scenario:** You created a new skill and want to share it.

```bash
# Skill location
SKILL_PATH="/home/sasha/all-project-files/deployed-md-files/docs/skills/data-analysis"

# Contribute to community
./scripts/contribute_skill.sh contribute $SKILL_PATH
```

**What happens:**
1. Validates skill structure (SKILL.md exists, proper format)
2. Creates branch: `contribute-data-analysis-20260131-120000`
3. Copies all skill files to repository
4. Commits: "Add data-analysis skill"
5. Pushes and creates PR with:
   - Title: "Add data-analysis skill"
   - Description with file count, line count, testing checklist
   - Link to view changes

**Output:**
```
✓ Skill directory validated
✓ GitHub token retrieved
✓ Repository ready
✓ Branch created: contribute-data-analysis-20260131-120000
✓ Skill files copied
✓ Changes committed
✓ Branch pushed
✓ Pull request created

✨ Success! Pull request created

   View PR: https://github.com/context-is-everything/skills/pull/123
```

### Pattern 2: Updating an Existing Skill

**Scenario:** You improved documentation in the pdf-editor skill.

```bash
# Updated skill location
SKILL_PATH="/home/sasha/all-project-files/deployed-md-files/docs/skills/pdf-editor"

# Submit update
./scripts/contribute_skill.sh update $SKILL_PATH --message "Improve documentation and add examples"
```

**What happens:**
1. Validates skill exists and is modified
2. Creates branch: `update-pdf-editor-20260131-120000`
3. Removes old version
4. Copies updated files
5. Commits with custom message
6. Pushes and creates PR

### Pattern 3: Custom Branch for Related Changes

**Scenario:** Making related fixes across multiple updates.

```bash
# Use consistent branch name
BRANCH="fix-typos-batch-1"

./scripts/contribute_skill.sh update /path/to/skill1 --branch $BRANCH
./scripts/contribute_skill.sh update /path/to/skill2 --branch $BRANCH
./scripts/contribute_skill.sh update /path/to/skill3 --branch $BRANCH

# All updates go to same PR
```

## Script Reference

### contribute_skill.sh

**Location:** `scripts/contribute_skill.sh`

**Synopsis:**
```bash
contribute_skill.sh <operation> <skill-path> [options]
```

**Operations:**
- `contribute` - Add a new skill
- `update` - Update an existing skill

**Arguments:**
- `skill-path` - Absolute path to skill directory

**Options:**
- `--branch NAME` - Custom branch name (default: auto-generated with timestamp)
- `--message MSG` - Custom commit message (default: auto-generated)

**Environment Variables:**
- `GITHUB_TOKEN` - Fallback if secretstore unavailable (not recommended)

**Exit Codes:**
- `0` - Success
- `1` - Error (validation failed, token missing, git error, etc.)

### Examples

```bash
# Basic contribution
./scripts/contribute_skill.sh contribute /home/sasha/all-project-files/deployed-md-files/docs/skills/my-skill

# Update with custom branch
./scripts/contribute_skill.sh update /path/to/skill --branch fix-bug-123

# Contribute with custom message
./scripts/contribute_skill.sh contribute /path/to/skill --message "Add comprehensive data visualization skill"

# Update multiple related files
BRANCH="feature-improvements"
./scripts/contribute_skill.sh update /path/to/skill1 --branch $BRANCH
./scripts/contribute_skill.sh update /path/to/skill2 --branch $BRANCH
```

## Manual Workflow (Alternative)

If you prefer to run the workflow manually or need more control:

### Step-by-Step Manual Process

1. **Retrieve Token**
```bash
TOKEN=$(mcp__secretstore__secretstore_get github-community-token | jq -r '.secret.value')
```

2. **Clone Repository**
```bash
cd /tmp
rm -rf skills
git clone "https://${TOKEN}@github.com/context-is-everything/skills.git"
cd skills
```

3. **Configure Git**
```bash
git config user.email "sasha@context-is-everything.ai"
git config user.name "Sasha AI"
```

4. **Create Branch**
```bash
SKILL_NAME="my-skill"
BRANCH="contribute-${SKILL_NAME}-$(date +%Y%m%d-%H%M%S)"
git checkout -b "$BRANCH"
```

5. **Copy Skill**
```bash
SKILL_PATH="/home/sasha/all-project-files/deployed-md-files/docs/skills/$SKILL_NAME"
cp -r "$SKILL_PATH" "./$SKILL_NAME"
```

6. **Commit Changes**
```bash
git add .
git commit -m "Add $SKILL_NAME skill"
```

7. **Push Branch**
```bash
git remote set-url origin "https://${TOKEN}@github.com/context-is-everything/skills.git"
git push -u origin "$BRANCH"
```

8. **Create PR**
```bash
PR_URL=$(curl -s -X POST \
  -H "Authorization: token ${TOKEN}" \
  -H "Accept: application/vnd.github.v3+json" \
  https://api.github.com/repos/context-is-everything/skills/pulls \
  -d "{
    \"title\": \"Add $SKILL_NAME skill\",
    \"body\": \"Adding new skill to community repository\",
    \"head\": \"$BRANCH\",
    \"base\": \"main\"
  }" | jq -r '.html_url')

echo "Pull request created: $PR_URL"
```

**See [references/github-workflow.md](references/github-workflow.md) for complete manual workflow patterns.**

## Troubleshooting

### Token Not Found

**Error:**
```
✗ GitHub token not found in secretstore or environment
✗ Set GITHUB_TOKEN environment variable or configure secretstore
```

**Solutions:**
1. Verify token exists in secretstore:
   ```bash
   mcp__secretstore__secretstore_list | grep github-community-token
   ```

2. If missing, token needs to be configured (contact administrator)

3. Temporary workaround (not recommended):
   ```bash
   export GITHUB_TOKEN="ghp_your_token_here"
   ./scripts/contribute_skill.sh contribute /path/to/skill
   ```

### Skill Validation Failed

**Error:**
```
✗ SKILL.md not found in: /path/to/skill
```

**Solutions:**
1. Verify path is correct:
   ```bash
   ls -la /path/to/skill
   ```

2. Check SKILL.md exists:
   ```bash
   ls -la /path/to/skill/SKILL.md
   ```

3. Ensure you're providing the skill directory, not a file:
   ```bash
   # Wrong: ./contribute_skill.sh contribute /path/to/skill/SKILL.md
   # Right: ./contribute_skill.sh contribute /path/to/skill
   ```

### No Changes to Commit

**Warning:**
```
⚠ No changes to commit
```

**This occurs when:**
- Updating a skill that hasn't changed
- Skill already exists in repository with identical content

**Solutions:**
1. Verify skill was actually modified:
   ```bash
   cd /tmp/skills
   git status
   git diff
   ```

2. If no real changes, no PR is needed

### Permission Denied

**Error:**
```
remote: Permission to context-is-everything/skills.git denied
fatal: unable to access 'https://github.com/...': The requested URL returned error: 403
```

**Solutions:**
1. Verify token has push access:
   ```bash
   TOKEN=$(mcp__secretstore__secretstore_get github-community-token | jq -r '.secret.value')
   curl -H "Authorization: token ${TOKEN}" \
     https://api.github.com/repos/context-is-everything/skills | jq '.permissions'
   ```

2. Expected output should show `"push": true`

3. If permissions incorrect, token may need rotation (contact administrator)

### Token Permission Denied After Committing

**Scenario:** The script successfully cloned, branched, and committed, but failed when pushing to GitHub.

**Error:**
```
✓ Changes committed
remote: Permission to context-is-everything/skills.git denied to [username]
fatal: unable to access 'https://github.com/...': The requested URL returned error: 403
```

**This happens when:**
- Token validation wasn't performed before starting
- Token was rotated or revoked mid-process
- Token belongs to user without push permissions

**Recovery Steps:**

1. **Update the token in secretstore:**
   ```bash
   # Add the new token with push permissions
   mcp__secretstore__secretstore_set github-community-token "ghp_NewTokenHere..."
   ```

2. **Manually push the existing committed work:**
   ```bash
   cd /tmp/skills
   git checkout [your-branch-name]

   # Update remote with new token
   TOKEN=$(mcp__secretstore__secretstore_get github-community-token | jq -r '.secret.value')
   git remote set-url origin "https://${TOKEN}@github.com/context-is-everything/skills.git"

   # Push the branch
   git push -u origin [your-branch-name]
   ```

3. **Create the PR manually:**
   ```bash
   TOKEN=$(mcp__secretstore__secretstore_get github-community-token | jq -r '.secret.value')
   curl -s -X POST \
     -H "Authorization: token ${TOKEN}" \
     -H "Accept: application/vnd.github.v3+json" \
     https://api.github.com/repos/context-is-everything/skills/pulls \
     -d '{
       "title": "Add [skill-name] skill",
       "body": "Brief description of the skill",
       "head": "[your-branch-name]",
       "base": "main"
     }' | jq -r '.html_url'
   ```

**Prevention:**
- Validate token permissions BEFORE starting (see Best Practices section below)

## Best Practices

### Before Contributing

1. **Validate Token Permissions** - Verify token has push access BEFORE starting (prevents wasted work):
   ```bash
   # Test token permissions (should return "push": true)
   TOKEN=$(mcp__secretstore__secretstore_get github-community-token | jq -r '.secret.value')
   curl -s -H "Authorization: token ${TOKEN}" \
     https://api.github.com/repos/context-is-everything/skills | jq '.permissions'
   ```

2. **Test Your Skill** - Ensure skill works as expected

3. **Validate Structure** - Run skill validator if available

4. **Review Documentation** - Check SKILL.md is clear and complete

5. **Check Uniqueness** - Ensure skill doesn't duplicate existing skills

### Contribution Guidelines

**Good contributions:**
- ✅ Clear, focused purpose
- ✅ Comprehensive documentation
- ✅ Tested on real use cases
- ✅ Follows skill structure standards
- ✅ Includes examples and references

**Avoid:**
- ❌ Duplicate functionality of existing skills
- ❌ Missing or incomplete documentation
- ❌ Untested code or scripts
- ❌ Non-standard skill structure
- ❌ Overly specific or single-use skills

## Related Documentation

- **GitHub Workflow Patterns** - See [references/github-workflow.md](references/github-workflow.md)
- **Token Management** - See `/home/sasha/all-project-files/deployed-md-files/docs/setup/github-token-distribution-design.md`
- **Skill Creation** - Use the `skill-creator` skill

---

## Lessons Learned

These improvements were identified from real-world usage (February 2026 - stealth-search skill contribution):

### Issue 1: Late Permission Discovery
**Problem:** Token validation happens at PUSH stage (step 6), after clone, branch, commit work is complete. If token lacks permissions, you've wasted time and left a partial state.

**Solution:** Validate token permissions BEFORE starting any work (see "Before Contributing" best practices above).

### Issue 2: Poor Error Recovery
**Problem:** When push fails due to permission issues, the script doesn't provide recovery guidance. User must manually push with updated token.

**Solution:** Added comprehensive "Token Permission Denied After Committing" troubleshooting section with exact recovery steps.

### Issue 3: Branch Exists from Failed Attempt
**Problem:** After fixing token and retrying, script fails because branch already exists from previous attempt. No cleanup guidance provided.

**Solution:** Updated workflow step 3 to mention cleanup. Future enhancement: automate branch cleanup detection and retry.

### Recommended Future Enhancements

1. **Automated Pre-Flight Check:**
   ```bash
   # Add to script beginning
   validate_token_permissions() {
     TOKEN=$1
     curl -s -H "Authorization: token ${TOKEN}" \
       https://api.github.com/repos/context-is-everything/skills | \
       jq -e '.permissions.push == true' > /dev/null || {
       echo "✗ Token does not have push permissions"
       exit 1
     }
   }
   ```

2. **Smart Branch Retry:**
   ```bash
   # Clean up failed attempts automatically
   if git show-ref --verify --quiet refs/heads/$BRANCH; then
     echo "⚠ Branch $BRANCH exists from previous attempt"
     git branch -D $BRANCH
     echo "✓ Cleaned up, retrying..."
   fi
   ```

3. **Token Test Command:**
   ```bash
   # Add ./scripts/test_token.sh
   # Quick one-command validation before contributing
   ```

---

## Summary

The community-skills skill makes it effortless to share your work with the Sasha community:

- ✅ **Automated workflow** - From local skill to PR in one command
- ✅ **Secure authentication** - Uses pre-configured token from secretstore
- ✅ **Professional PRs** - Auto-generated descriptions and formatting
- ✅ **Error handling** - Clear messages and solutions
- ✅ **Best practices** - Follows established patterns

**Start contributing:**
```bash
./scripts/contribute_skill.sh contribute /path/to/your/amazing/skill
```

**Keep improving:**
```bash
./scripts/contribute_skill.sh update /path/to/your/improved/skill
```

Building the Sasha community, one skill at a time! 🚀

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/context-is-everything) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
