---
name: find-issue
description: Find and recommend issues suitable for contribution based on user's skills and experience. Use when user wants to find something to work on. Use when this capability is needed.
metadata:
  author: jay05410
---

# Issue Finder Skill

Find issues suitable for contribution in an open source project.

## When to Use

- User says they want to contribute but don't know what to work on
- User asks for beginner-friendly issues
- User mentions their experience level or skills
- User asks for "good first issue" recommendations

## Process

1. **Understand user context**
   - Experience level (beginner, intermediate, expert)
   - Technical skills (languages, frameworks)
   - Contribution type preference (code, docs, tests)

2. **Fetch open issues via GitHub MCP**
   - Filter by labels: good-first-issue, help-wanted, beginner
   - Check for unassigned issues
   - Verify issues are still relevant (not stale)

3. **Categorize by difficulty**
   - Easy: good-first-issue, documentation, typos
   - Medium: bug fixes, small features
   - Hard: architecture changes, core features

4. **Rank recommendations**
   - Match to user skills
   - Prioritize active issues (recent comments)
   - Prefer well-documented issues

## Output Format

Respond in user's language:

```
# Recommended Issues for [Project]

## Your Profile
- Experience: [detected or stated]
- Skills: [detected or stated]

## 🟢 Beginner-Friendly

### #[number] [title]
- **Type**: Bug/Feature/Docs
- **Labels**: `label1`, `label2`
- **Why recommended**: [specific reason]
- **Link**: [URL]

## 🟡 Intermediate
[Similar format]

## 🔴 Challenging
[Similar format]

## My Recommendation
Based on your profile, I suggest **#[number]** because [reason].

## Next Steps
1. Comment on the issue to claim it
2. Ask clarifying questions if needed
3. Start with `/oss-claudecode:analyze` if you need more context
```

## Arguments

`$ARGUMENTS` can include:
- Repository: `kotest/kotest`
- Difficulty: `--easy`, `--medium`, `--hard`
- Type: `--bug`, `--feature`, `--docs`
- Skills: `--skill=typescript`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jay05410) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
