---
name: create-pr
description: Create a pull request with a clean, AI-native summary of all commits. Use when the user wants to create a PR from the current branch with a well-structured description. Use when this capability is needed.
metadata:
  author: ameyalambat128
---

# Create PR

Create a pull request with a clean, AI-native summary of all commits.

## Instructions

1. **Gather context** - Run these commands in parallel:
   - `git log origin/main..HEAD --oneline` to see commits to include
   - `git diff origin/main..HEAD --stat` to see files changed
   - `git branch --show-current` to get current branch name
   - `git log origin/main..HEAD --pretty=format:"%s%n%b"` to get full commit messages

2. **Analyze the changes**:
   - Group commits by feature/area (don't just list them)
   - Identify the primary purpose of the PR
   - Note any breaking changes or dependencies

3. **Generate PR title**:
   - Use conventional commit format: `<type>(<scope>): <description>`
   - Keep it under 72 characters
   - Make it descriptive but concise

4. **Generate PR body** using this structure:

```markdown
## Summary
<2-4 bullet points explaining WHAT changed and WHY>

## Changes
<Group related changes together, not 1:1 with commits>
- **Area/Feature**: Description of changes
- **Area/Feature**: Description of changes

## Testing
- [ ] <Specific test scenarios>
- [ ] <Edge cases to verify>

## Notes
<Optional: Breaking changes, migration steps, deployment notes>
```

5. **Push and create PR**:
   - Push current branch if needed: `git push -u origin <branch>`
   - Create PR: `gh pr create --title "<title>" --body "<body>"`

6. **Return the PR URL** to the user

## Guidelines

- **Summarize, don't enumerate**: Group related commits into cohesive change descriptions
- **Focus on impact**: What does this PR enable or fix for users/developers?
- **Be specific in testing**: Include actual test scenarios, not generic "test the feature"
- **Skip noise**: Don't mention minor refactors, typo fixes, or formatting unless significant
- **Use present tense**: "Add feature" not "Added feature"
- **Link context**: Reference issues with #123 if mentioned in commits

## Example Output

```
PR Title: feat(sounds): add nature sounds to therapeutic sound library

## Summary
- Add 4 new nature sounds (rainfall, forest, ocean, fireplace) to the Sound Library
- Update existing audio files to 1-hour duration for extended therapy sessions
- Enhance the UI with appropriate icons for each sound type

## Changes
- **Audio Files**: Replace 4 existing MP3s with higher quality 1-hour versions, add new fire.mp3
- **SoundLibrary Component**: Add nature sound entries with CloudRain, TreePine, Waves, Flame icons

## Testing
- [ ] Verify all 5 audio files play and loop correctly
- [ ] Check nature sounds appear in UI with correct icons and descriptions
- [ ] Test volume controls and progress bar functionality

## Notes
Audio files are 82MB each. Consider Git LFS for future audio additions.
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ameyalambat128) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
