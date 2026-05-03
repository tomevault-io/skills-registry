---
name: github-automation
description: Comprehensive GitHub automation skill for PR/Issue management, repository operations, CI/CD integration, and team collaboration. Use this skill when working with GitHub repositories, creating Pull Requests, managing Issues, automating code reviews, handling releases, or performing repository maintenance tasks. This skill integrates with GitHub CLI (gh) and GitHub Actions for streamlined workflows. Use when this capability is needed.
metadata:
  author: liyang2016
---

# GitHub Automation

## Overview

This skill provides comprehensive workflows and automation capabilities for GitHub repositories, including Pull Request management, Issue tracking, code review automation, release processes, and repository maintenance. Leverage this skill to streamline development workflows, enforce best practices, and automate repetitive GitHub operations through GitHub CLI and Actions.

## Quick Reference

**Before using this skill, ensure GitHub CLI (gh) is installed and authenticated:**
```bash
gh auth status
```

**Key automation areas:**
- **PR Management**: Create, review, update, and manage Pull Requests
- **Issue Tracking**: Create, label, and prioritize Issues
- **Repository Operations**: Branch management, releases, tags, maintenance
- **CI/CD Integration**: GitHub Actions workflows and automation
- **Team Collaboration**: Assignments, reviews, notifications, project boards

## PR/Issue Management Workflows

### Creating Pull Requests

**Automated PR Creation Workflow:**

1. **Prepare the branch**
   - Ensure branch follows naming convention: `type/description` (e.g., `feature/add-auth`, `bugfix/login-error`)
   - Update base branch if needed: `git fetch && git rebase origin/main`
   - Run tests and linters locally

2. **Create the PR using gh CLI**
   ```bash
   gh pr create --title "PR title" --body "PR description" --base main --head feature/branch-name
   ```

3. **Auto-assign reviewers**
   ```bash
   gh pr edit --add-reviewer username1,username2
   ```

4. **Add labels automatically based on files changed**
   ```bash
   # Use scripts/auto_label_pr.py for intelligent labeling
   python scripts/auto_label_pr.py
   ```

5. **Link to related issues**
   - Include issue numbers in PR title: `Fix #123`
   - Or in body: `Closes #123, #456`

**PR Template Usage:**
- Use `assets/pr_template.md` as the default PR template
- Place template in `.github/PULL_REQUEST_TEMPLATE.md`
- Template includes sections for: description, changes, testing, checklist

### Reviewing Pull Requests

**Automated Code Review Workflow:**

1. **PR Health Check**
   ```bash
   # Run scripts/pr_health_check.py to validate:
   # - CI/CD status
   # - Description completeness
   # - Required labels
   # - Reviewer assignments
   python scripts/pr_health_check.py <pr-number>
   ```

2. **Automated Review Comments**
   - Check for common issues using patterns in `references/code_review_patterns.md`
   - Suggest improvements based on diff analysis

3. **Approval Management**
   ```bash
   gh pr review <pr-number> --approve --body "LGTM"
   gh pr review <pr-number> --request-changes --body "Please fix..."
   gh pr review <pr-number> --comment --body "Questions..."
   ```

### Managing Issues

**Issue Creation and Triage:**

1. **Create Issue with Template**
   ```bash
   gh issue create --title "Issue title" --body-file assets/issue_template.md
   ```

2. **Auto-label Issues**
   ```bash
   # Use scripts/auto_label_issue.py for labeling based on keywords
   python scripts/auto_label_issue.py <issue-number>
   ```

3. **Assign and Prioritize**
   ```bash
   gh issue edit <issue-number> --assignee username --add-label "priority:high"
   ```

4. **Link Related Issues**
   - Use `#` syntax to reference other issues
   - Create issue dependencies using relationships

## Repository Operations

### Branch Management

**Branch Operations Workflow:**

1. **Create Feature Branch**
   ```bash
   git checkout -b feature/branch-name
   # Push to remote
   git push -u origin feature/branch-name
   ```

2. **Set Up Branch Protection** (requires admin permissions)
   ```bash
   # Use scripts/setup_branch_protection.py
   python scripts/setup_branch_protection.py --branch main --require-pr --require-ci
   ```

3. **Synchronize Fork**
   ```bash
   # Use scripts/sync_fork.py to sync fork with upstream
   python scripts/sync_fork.py
   ```

### Release Management

**Automated Release Workflow:**

1. **Prepare Release**
   - Update version in code
   - Update CHANGELOG.md
   - Ensure all PRs are merged

2. **Create Release Tag**
   ```bash
   git tag -a v1.0.0 -m "Release v1.0.0"
   git push origin v1.0.0
   ```

3. **Generate Release Notes**
   ```bash
   # Use scripts/generate_changelog.py
   python scripts/generate_changelog.py --since v0.9.0
   ```

4. **Create GitHub Release**
   ```bash
   gh release create v1.0.0 --notes-file assets/release_notes.md
   ```

5. **Publish Release**
   ```bash
   gh release edit v1.0.0 --draft=false
   ```

**Release Template:**
- Use `assets/release_notes_template.md` for consistent release notes
- Include: version, highlights, breaking changes, features, bug fixes, upgrades

### Repository Maintenance

**Maintenance Tasks:**

1. **Clean Up Old Branches**
   ```bash
   # Use scripts/cleanup_branches.py
   python scripts/cleanup_branches.py --days-older-than 90 --merged-only
   ```

2. **Update Repository Settings**
   ```bash
   # Use scripts/repo_maintenance.py
   python scripts/repo_maintenance.py --enable-auto-merge --set-default-branch main
   ```

3. **Archive Stale Issues**
   ```bash
   # Close issues older than 1 year with no activity
   gh issue list --state open --json number,title,updatedAt | \
     jq '.[] | select(.updatedAt | fromdateiso8601 < now - 365*24*3600) | .number' | \
     xargs -I {} gh issue close {} --comment "Auto-closed due to inactivity"
   ```

## CI/CD Integration

### GitHub Actions Workflows

**Workflow Creation:**

1. **Create Workflow File**
   - Place in `.github/workflows/`
   - Use YAML syntax
   - Reference `references/workflows.md` for examples

2. **Common Workflow Patterns:**

**CI Pipeline:**
```yaml
name: CI
on: [push, pull_request]
jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
      - run: npm ci
      - run: npm test
```

**Automated PR Review:**
```yaml
name: PR Review
on: pull_request
jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run linter
        run: npm run lint
      - name: Comment on PR
        uses: actions/github-script@v7
        with:
          script: |
            github.rest.issues.createComment({
              issue_number: context.issue.number,
              owner: context.repo.owner,
              repo: context.repo.repo,
              body: 'Lint checks passed!'
            })
```

3. **Workflow Management**
   ```bash
   # List workflows
   gh workflow list

   # Run workflow manually
   gh workflow run workflow-name.yml

   # View workflow runs
   gh run list --workflow=workflow-name.yml

   # View run logs
   gh run view <run-id> --log
   ```

### Continuous Deployment

**CD Pipeline Setup:**

1. **Create Deployment Workflow**
   - Trigger on merge to main
   - Run tests, build, and deploy
   - Reference `references/workflows.md` for CD examples

2. **Environment Management**
   ```bash
   # Create environments
   gh api /repos/{owner}/{repo}/environments --raw-field name staging

   # Add protection rules
   gh api -X PUT /repos/{owner}/{repo}/environments/staging \
     --raw-field deployment_branch_policy '{ "protected_branches": true, "custom_branch_policies": false }'
   ```

3. **Deploy on PR Merge**
   ```yaml
   name: Deploy
   on:
     push:
       branches: [main]
   jobs:
     deploy:
       runs-on: ubuntu-latest
       steps:
         - uses: actions/checkout@v4
         - name: Deploy to production
           run: ./deploy.sh
   ```

## Team Collaboration

### Project Boards

**Project Management:**

1. **Create Project Board**
   ```bash
   gh project create --title "Sprint Board" --owner "@org"
   ```

2. **Add Items to Project**
   ```bash
   # Add PR to project
   gh project item add <project-id> --url "<pr-url>"

   # Add issue to project
   gh project item add <project-id} --url "<issue-url>"
   ```

3. **Automate Project Updates**
   ```bash
   # Use scripts/project_board_automation.py
   python scripts/project_board_automation.py --move-completed
   ```

### Team Management

**Team Operations:**

1. **Create Team**
   ```bash
   gh api /orgs/{org}/teams --raw-field name "frontend-team" --raw-field permission "pull"
   ```

2. **Add Members to Team**
   ```bash
   gh api -X PUT /orgs/{org}/teams/frontend-team/memberships/{username}
   ```

3. **Assign Team to Review**
   ```bash
   gh pr edit <pr-number> --add-reviewer "frontend-team"
   ```

### Notifications and Status Updates

**Automated Notifications:**

1. **Slack Integration**
   ```yaml
   - name: Notify Slack
     uses: 8398a7/action-slack@v3
     with:
       status: ${{ job.status }}
       text: 'PR #${{ github.event.number }} is ready for review'
       webhook_url: ${{ secrets.SLACK_WEBHOOK }}
   ```

2. **Status Checks**
   ```bash
   # Create combined status
   gh api /repos/{owner}/{repo}/statuses/{sha} \
     --raw-field state "success" \
     --raw-field context "ci/travis" \
     --raw-field description "All checks passed"
   ```

## Best Practices

### PR/Issue Hygiene

1. **Use descriptive titles**: "Add user authentication" not "Update code"
2. **Keep PRs small**: Reviewable in <30 minutes
3. **Link issues**: Always reference related issues
4. **Use templates**: Ensure all PRs have complete descriptions
5. **Auto-label**: Use scripts to categorize automatically

### Repository Security

1. **Enable branch protection**: Require PRs and CI checks
2. **Use CODEOWNERS**: Define required reviewers by file path
3. **Secret scanning**: Enable in repository settings
4. **Dependabot**: Enable for dependency updates
5. **Regular audits**: Use scripts to audit permissions and access

### CI/CD Best Practices

1. **Fast feedback**: Run quick checks first, slow checks later
2. **Cache dependencies**: Speed up builds with caching
3. **Use matrix builds**: Test across multiple versions
4. **Deploy safely**: Use blue-green or canary deployments
5. **Monitor failures**: Set up alerts for failed runs

## Resources

### scripts/

- **auto_label_pr.py**: Automatically label PRs based on changed files
- **auto_label_issue.py**: Label issues based on keywords and content
- **pr_health_check.py**: Validate PR completeness before merge
- **setup_branch_protection.py**: Configure branch protection rules
- **sync_fork.py**: Synchronize fork with upstream repository
- **generate_changelog.py**: Generate changelog from merged PRs
- **cleanup_branches.py**: Remove old merged branches
- **repo_maintenance.py**: Update repository settings and configurations
- **project_board_automation.py**: Automate project board operations

### references/

- **gh_commands.md**: Comprehensive GitHub CLI command reference
- **code_review_patterns.md**: Common code review patterns and checklists
- **workflows.md**: GitHub Actions workflow examples and patterns
- **best_practices.md**: GitHub best practices and guidelines

### assets/

- **pr_template.md**: Pull Request template with standard sections
- **issue_template.md**: Issue template with bug report and feature request formats
- **release_notes_template.md**: Release notes template for consistent formatting
- **codeowners_file**: CODEOWNERS file example for team-based reviews

## Troubleshooting

**Common Issues:**

1. **gh CLI not authenticated**
   ```bash
   gh auth login
   ```

2. **Branch protection errors**
   - Ensure you have admin permissions
   - Check repository settings for conflicting rules

3. **Workflow failures**
   - Check logs: `gh run view <run-id> --log`
   - Validate YAML syntax
   - Ensure secrets are configured

4. **Permission denied errors**
   - Verify your access level
   - Check if you're in the required teams
   - Ensure OAuth tokens have correct scopes

## Examples

**Example 1: Complete PR Workflow**
```bash
# Create feature branch
git checkout -b feature/add-oauth

# Make changes and commit
git commit -am "Add OAuth support"

# Push and create PR
git push -u origin feature/add-oauth
gh pr create --title "Add OAuth authentication" \
  --body-file assets/pr_template.md \
  --base main \
  --add-reviewer dev-team \
  --add-label "feature,authentication"
```

**Example 2: Automated Release Process**
```bash
# Generate changelog
python scripts/generate_changelog.py --since v1.0.0 > assets/release_notes.md

# Create and push tag
git tag -a v1.1.0 -m "Release v1.1.0"
git push origin v1.1.0

# Create GitHub release
gh release create v1.1.0 --notes-file assets/release_notes.md --draft=false
```

**Example 3: Repository Maintenance**
```bash
# Clean up old branches
python scripts/cleanup_branches.py --days-older-than 90 --merged-only

# Update branch protection
python scripts/setup_branch_protection.py --branch main --require-pr --require-ci

# Archive stale issues
gh issue list --state open --search "updated:<365 days ago" --json number | \
  jq -r '.[].number' | xargs -I {} gh issue close {} --comment "Auto-closed"
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liyang2016) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
