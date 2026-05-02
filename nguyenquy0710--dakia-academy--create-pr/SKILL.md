---
name: create-pr
description: Creates GitHub pull requests with properly formatted titles that pass the check-pr-title CI validation. Use when creating PRs, submitting changes for review, or when the user says /pr or asks to create a pull request.
metadata:
  author: nguyenquy0710
---

# Create Pull Request

Creates GitHub PRs with titles following DAKIA Academy's Conventional Commits specification for proper changelog generation and CI validation.

## PR Title Format

```
<type>(<scope>): <summary>
```

### Types (required)

| Type       | Description                                      | Changelog | Example Use Case |
|------------|--------------------------------------------------|-----------|------------------|
| `feat`     | New feature for users                            | Yes       | Add video player, new API endpoint |
| `fix`      | Bug fix for users                                | Yes       | Fix login issue, resolve crash |
| `perf`     | Performance improvement                          | Yes       | Optimize database queries |
| `test`     | Adding/correcting tests                          | No        | Add unit tests, E2E tests |
| `docs`     | Documentation only                               | No        | Update README, add comments |
| `refactor` | Code refactoring (no functional change)          | No        | Simplify logic, extract function |
| `style`    | Code style/formatting changes                    | No        | Fix indentation, format code |
| `build`    | Build system or dependency changes               | No        | Update package.json, npm scripts |
| `ci`       | CI/CD configuration                              | No        | Update GitHub Actions |
| `chore`    | Routine tasks, maintenance                       | No        | Update gitignore, cleanup |

### Scopes (optional but recommended)

**Feature-based scopes:**
- `auth` - Authentication and authorization
- `courses` - Course management and content
- `admin` - Admin panel functionality
- `api` - API routes and endpoints
- `ui` - User interface components
- `db` - Database models and operations
- `markdown` - Markdown processing and rendering

**Area-based scopes:**
- `client` - Web Client (public platform)
- `admin-panel` - Admin Panel
- `shared` - Shared components/utilities

### Summary Rules

- Use imperative present tense: "Add" not "Added"
- Capitalize first letter
- No period at the end
- Keep it concise (50 chars or less recommended)
- Add `[skip ci]` to skip CI checks (use sparingly)

## Steps

1. **Check current state**:
   ```bash
   git status
   git diff --stat
   git log origin/main..HEAD --oneline
   ```

2. **Analyze changes** to determine:
   - Type: What kind of change is this? (feat, fix, refactor, etc.)
   - Scope: Which area/feature is affected? (auth, courses, api, etc.)
   - Summary: What does the change do? (imperative, capitalized)

3. **Push branch if needed**:
   ```bash
   git push -u origin HEAD
   ```

4. **Create PR** using gh CLI with the template from `.github/pull_request_template.md`:
   ```bash
   gh pr create --draft --title "<type>(<scope>): <summary>" --body "$(cat <<'EOF'
   ## Summary

   <Describe what the PR does and why. Include how to test the changes.>

   ## Screenshots/Videos (if applicable)

   <Add screenshots or videos for UI changes>

   ## Related Issues

   <!-- Use "closes #<issue-number>", "fixes #<issue-number>", or "resolves #<issue-number>" to auto-close issues -->
   <!-- Example: Closes #123 -->

   ## Type of Change

   - [ ] Bug fix (non-breaking change which fixes an issue)
   - [ ] New feature (non-breaking change which adds functionality)
   - [ ] Breaking change (fix or feature that would cause existing functionality to not work as expected)
   - [ ] Documentation update
   - [ ] Performance improvement
   - [ ] Code refactoring

   ## Checklist

   - [ ] My code follows the project's coding conventions (see `.github/copilot-instructions.md`)
   - [ ] I have performed a self-review of my code
   - [ ] I have commented my code, particularly in hard-to-understand areas
   - [ ] I have made corresponding changes to the documentation
   - [ ] My changes generate no new warnings or errors
   - [ ] I have added tests that prove my fix is effective or that my feature works
   - [ ] New and existing unit tests pass locally with my changes
   - [ ] Any dependent changes have been merged and published

   ## Testing Performed

   <Describe the testing you performed to verify your changes>

   - [ ] Unit tests
   - [ ] E2E tests
   - [ ] Manual testing

   ## Additional Notes

   <Any additional information that reviewers should know>
   EOF
   )"
   ```

## PR Body Guidelines

Based on `.github/pull_request_template.md`:

### Summary Section
- **What**: Clearly describe what the PR does
- **Why**: Explain the motivation behind the change
- **How**: Briefly explain the approach taken
- Include screenshots/videos for UI/UX changes

### Related Issues Section
- Link to GitHub issues using keywords to auto-close:
  - `closes #123` / `fixes #123` / `resolves #123`
- Reference related PRs if applicable

### Type of Change
Check all that apply:
- Bug fix (non-breaking)
- New feature (non-breaking)
- Breaking change
- Documentation
- Performance
- Refactoring

### Checklist
All items should be checked before requesting review:
- Code follows project conventions
- Self-review completed
- Comments added for complex logic
- Documentation updated
- No new warnings/errors
- Tests added and passing
- Dependencies merged

### Testing Section
Describe testing performed:
- Unit tests added/updated
- E2E tests added/updated
- Manual testing steps

## Examples

### Feature - Course functionality
```
feat(courses): add video player with subtitle support
```

### Bug fix - Authentication
```
fix(auth): resolve session timeout on page refresh
```

### UI improvement
```
style(ui): improve course card responsive design
```

### API change
```
feat(api): add endpoint for user progress tracking
```

### Database update
```
refactor(db): simplify user enrollment schema
```

### Performance optimization
```
perf(api): optimize course query with aggregation pipeline
```

### Breaking change (add exclamation mark before colon)
```
feat(api)!: change user profile response structure
```

### Documentation
```
docs(readme): update installation instructions for Windows
```

### Skip CI (use sparingly)
```
docs(readme): fix typo in example [skip ci]
```

### No scope (affects multiple areas)
```
chore: update npm dependencies to latest versions
```

### Admin panel feature
```
feat(admin): add user analytics dashboard
```

### Markdown processing
```
fix(markdown): handle code blocks in course content
```

## Validation

The PR title must follow Conventional Commits specification:

```regex
^(feat|fix|perf|test|docs|refactor|style|build|ci|chore)(\([a-z-]+\))?!?: [A-Z].+[^.]$
```

Key validation rules:
- **Type**: Must be one of the allowed types
- **Scope**: Optional, lowercase with hyphens, in parentheses
- **Breaking change**: Exclamation mark goes before the colon
- **Summary**: Must start with capital letter, no period at end
- **Length**: Keep title under 72 characters total

## Tips for Good PRs

### DO ✅
- Keep PRs focused on a single change
- Write clear, descriptive titles
- Include context in the description
- Add screenshots for UI changes
- Write/update tests
- Update documentation
- Request review from relevant team members

### DON'T ❌
- Mix unrelated changes in one PR
- Use vague titles like "Fix bug" or "Update code"
- Skip the PR template
- Leave commented-out code
- Commit sensitive data or credentials
- Ignore failing CI checks

## Related Documentation

- [AGENTS.md](../../AGENTS.md) - Full development guidelines
- [.github/copilot-instructions.md](../../.github/copilot-instructions.md) - Coding standards
- [.github/WORKFLOWS.md](../../.github/WORKFLOWS.md) - CI/CD documentation
- [Conventional Commits](https://www.conventionalcommits.org/) - Commit message convention

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nguyenquy0710) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
