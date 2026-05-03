---
name: git-standup
description: Summarize recent work and commit history using git-standup. This skill should be used when users ask to review what they've done recently, create standup reports, summarize commit history, or recall recent development work across one or multiple repositories. Use when this capability is needed.
metadata:
  author: ycluis
---

# git-standup

Use git-standup to generate summaries of recent commit activity across Git repositories.

## Purpose

git-standup is a command-line tool that recalls development work by displaying commits from recent working days. It scans single or multiple Git repositories and filters commits by author, date range, and other criteria to create standup reports and work summaries.

## When to Use This Skill

Use this skill when users request:
- Daily standup summaries ("What did I do yesterday?")
- Weekly work reviews ("Show my commits from last week")
- Sprint retrospectives ("What have I worked on in the last 2 weeks?")
- Team activity summaries ("What has the team committed recently?")
- Commit history reviews for specific date ranges
- Multi-repository work summaries

## Installation Check and Guidance

Before using git-standup, verify it's installed:

```bash
which git-standup
```

If not installed, guide the user to install via their preferred method:
- **npm**: `npm install -g git-standup`
- **Homebrew** (macOS/Linux): `brew install git-standup`
- **Manual installation**: Visit https://github.com/kamranahmedse/git-standup

## Workflow

### 1. Understand the User's Request

Determine what summary is needed:
- **Time range**: Yesterday, last week, last sprint, specific dates?
- **Scope**: Single repository or workspace with multiple repos?
- **Author**: Just the user, specific teammate, or entire team?
- **Detail level**: Just commit messages or include diff stats?

### 2. Select Appropriate Options

Refer to `references/command_options.md` for complete option details. Common patterns:

**Personal daily standup**:
```bash
git standup -d 1
```

**Team weekly summary**:
```bash
git standup -a all -d 7
```

**Detailed work review with stats**:
```bash
git standup -d 5 -c
```

**Specific date range**:
```bash
git standup -A 2026-01-15 -B 2026-01-20
```

**Multi-repository workspace**:
```bash
# Run from parent directory
git standup -m 3 -d 7
```

### 3. Execute git-standup

Run the appropriate command in the user's working directory or repository root.

For multi-repository summaries, navigate to the parent directory containing all repositories before executing.

### 4. Analyze and Summarize the Output

git-standup provides raw commit data. Enhance value by:

**Categorizing work**:
- Group commits by project/repository
- Identify themes (features, bug fixes, refactoring, documentation)
- Highlight major accomplishments

**Formatting for readability**:
- Create clear sections by repository or category
- Use bullet points for individual commits
- Emphasize significant changes

**Providing context**:
- Note branch information when relevant
- Highlight patterns (e.g., "focused primarily on frontend work")
- Identify potential blockers or areas needing attention

**Example transformation**:

*Raw output*:
```
repo1: 2026-01-21 - Fix login bug (John)
repo1: 2026-01-21 - Update README (John)
repo2: 2026-01-20 - Add user profile feature (John)
```

*Enhanced summary*:
```markdown
## Work Summary: Last 2 Days

### repo1 - Authentication & Documentation
- Fixed critical login bug affecting session persistence
- Updated README with new authentication flow documentation

### repo2 - User Features
- Implemented new user profile feature with avatar upload
```

### 5. Handle Edge Cases

**No commits found**:
- Verify the correct directory and time range
- Check if user needs to fetch latest commits: `git standup -f`
- Adjust author filter if needed

**Too many results**:
- Narrow time range with `-d` option
- Filter by specific branch with `-b`
- Focus on specific author with `-a`

**Multiple repositories**:
- Ensure running from appropriate parent directory
- Adjust recursion depth with `-m` if needed
- Consider generating report file with `-r` for large outputs

## Advanced Usage

### Sprint Review Preparation

Generate comprehensive report with statistics:
```bash
git standup -d 14 -c -r
```

Then analyze the generated report file for trends and metrics.

### Team Activity Monitoring

Review all team members' contributions:
```bash
git standup -a all -d 7 -c
```

Summarize by team member and highlight collaboration patterns.

### Cross-Repository Analysis

For workspaces with many nested repositories:
```bash
git standup -m 4 -d 5 -c
```

Group output by repository and identify cross-cutting work.

## Best Practices

- **Verify current directory**: Ensure running from the correct repository or workspace root
- **Check installation first**: Confirm git-standup is available before attempting usage
- **Use appropriate time ranges**: Match `-d` value to the user's request (1 for daily, 7 for weekly, etc.)
- **Add context to summaries**: Don't just repeat commit messages; categorize and highlight key work
- **Leverage diff stats**: Use `-c` flag when users want detail on scope of changes
- **Consider multi-repo workspaces**: Use `-m` to control repository scanning depth
- **Fetch when needed**: Add `-f` flag if user might have recent unpulled commits

## Troubleshooting

**Command not found**:
- Confirm git-standup installation
- Check PATH includes installation directory

**No output or unexpected results**:
- Verify current directory contains Git repositories
- Check author name matches Git config: `git config user.name`
- Confirm date range captures expected commits
- Try broader time range or `-a all` to debug

**Performance issues with large workspaces**:
- Limit recursion depth with `-m`
- Focus on specific repository instead of parent directory
- Use specific date ranges rather than broad `-d` values

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ycluis) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
