---
name: git-commit
description: Create Git commit messages following Conventional Commits format with automatic type and scope detection. Use when asked to "commit changes", "create a commit", "write commit message", or when ready to commit staged code. Supports feat, fix, docs, refactor, test, build, ci, chore, and revert types with project-specific scope determination based on file paths and directory structure. Use when this capability is needed.
metadata:
  author: puku0x
---

# Git Commit Skill

This skill guides the creation of Git commit messages that follow the [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/) format.

## When to Use

Use this skill when:

- Code changes are completed and ready to be committed
- Need to create a commit message that adheres to project conventions

## Commit Message Format

The commit message must follow this format:

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

## Instructions

1. Check the staged files using `git diff --staged --name-only`.
   Ask user to stage changes if there are no staged files.
2. Determine the appropriate commit type based on the staged files.

**type** must be selected from the following options:

- `feat`: New feature or change to existing functionality
- `fix`: Bug fix
- `docs`: Documentation-only changes (e.g., changes to `*.md` files)
- `refactor`: Refactoring (code changes that neither fix a bug nor add a feature)
- `perf`: Performance improvements
- `test`: Adding or modifying tests (e.g., changes to `*.spec.*` or `*.spec.*.snap` files)
- `build`: Changes affecting the build system or external dependencies
- `ci`: Changes to CI configuration files (e.g., changes within `.github/actions` or `.github/workflows`)
- `chore`: Other changes (e.g., changes to `*.json`, `*.config.mjs`, `*.config.cjs`, `*.config.js`, `*.config.ts`, `.gitignore`, `.gitattributes`, `.prettierignore`, `.prettier`)
- `revert`: Reverting a previous commit

3. Determine the appropriate scope based on the staged files and the project structure.

**scope** should be determined based on the directory structure of the staged files. For example:

- If staged files are in `.claude/` or `.mcp.json`, use `claude`
- If staged files are in `.github/`, use `github`
- If staged files are in `.vscode/`, use `vscode`
- If staged files are in `apps/frontend/`, use `frontend`
- If staged files are in `libs/frontend/feature-xxx`, use `frontend-feature-xxx`
- If staged files are in `libs/utils/`, use `utils`
- If staged files are in the root directory, do not set a scope

4. Craft a comprehensive and descriptive commit message strictly following the Conventional Commits format.

**Important** NEVER include `\n` in the commit message. Always use actual newlines to separate the subject, body, and footer.

Example:

```bash
git commit -m "feat(utils): update xxx function

- Add xxx logic in xxx function
- Implement unit tests for xxx function

Co-Authored-By: Copilot <175728472+Copilot@users.noreply.github.com>"
```

## Notes

- **Important** Commit messages must be written in English.
- If there are staged changes, determine the commit message based only on the staged changes.
- Always include `Co-Authored-By` information. The commit message must clearly indicate that it was created with Copilot.

## References

- [Commit message](./references/commit-message.instructions.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/puku0x) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
