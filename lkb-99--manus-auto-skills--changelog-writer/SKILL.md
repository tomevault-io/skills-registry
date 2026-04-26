---
name: changelog-writer
description: "Automates changelog generation from Git history. Use this skill when users need to create, update, or manage a changelog file, generate release notes, or follow Conventional Commits and Keep a Changelog standards. Triggers: changelog, release notes, conventional commits, keep a changelog, git log, versioning, release, git tag, CHANGELOG.md, histórico de versões, notas de lançamento."
allowed-tools: [Read, Write, Edit, Bash, Browser]
license: MIT License
metadata:
    skill-author: Lucas Kefler Bergamaschi
---

# changelog-writer: Automated Changelog Generation

## Overview

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: changelog, release notes, conventional commits, keep a changelog, git log, versioning, release, git tag, CHANGELOG.md, histórico de versões, notas de lançamento
- Phrases: "gerar changelog", "criar notas de lançamento", "atualizar o CHANGELOG.md", "changelog a partir do git", "conventional commits"
- Context: Any discussion about documenting changes for a new software version, automating release notes from git history, or managing a project's version history file.

**Example user queries that trigger this skill:**
- "Preciso gerar um changelog para a nova versão do meu projeto."
- "Como posso criar release notes automaticamente a partir dos meus commits?"
- "Quero manter um CHANGELOG.md seguindo o formato Keep a Changelog."

Maintaining a clear, consistent, and up-to-date changelog is a critical practice in software development, yet it is often a manual and error-prone task. The `changelog-writer` skill is designed to eliminate this burden by automating the entire process. It leverages two widely adopted standards: **Conventional Commits** for structured commit messages and **Keep a Changelog** for formatting the output file. By parsing your Git history, the skill automatically identifies features, fixes, breaking changes, and other significant modifications, compiling them into a well-organized and human-readable `CHANGELOG.md` file. This ensures that your project's history is not only machine-readable but also easily understandable for developers, stakeholders, and end-users.

## When to Use This Skill

This skill is particularly useful in the following scenarios:

- **Preparing for a New Release:** Before tagging and publishing a new version of your software, use this skill to generate accurate release notes automatically.
- **CI/CD Pipeline Integration:** Incorporate this skill into your continuous integration or continuous deployment pipeline to automatically update the changelog every time a new version is built or deployed.
- **Standardizing Changelogs Across Projects:** When managing multiple repositories, this skill enforces a consistent changelog format, making it easier for teams to track changes across the board.
- **Migrating to an Automated Process:** If your project currently relies on manual changelog updates, this skill provides a straightforward path to a more efficient, automated workflow.
- **Generating Release Notes for Platforms:** The generated content can be used directly as the body for GitHub Releases, GitLab Releases, or other package management platforms.

## Core Capabilities

### 1. Conventional Commit Parsing

The skill is built to understand the Conventional Commits specification. It intelligently parses your `git log` to categorize commits based on their type.

- **`feat`:** Maps to the `Added` section for new features.
- **`fix`:** Maps to the `Fixed` section for bug fixes.
- **`perf`:** Maps to the `Changed` section, often highlighted as a performance improvement.
- **`refactor`:** Maps to the `Changed` section, noting code restructuring.
- **`docs`:** Changes to documentation. Usually excluded from the changelog unless specified.
- **`style`, `test`, `build`, `ci`, `chore`:** These types are considered internal and are typically excluded from the final changelog to keep it focused on user-facing changes.
- **`BREAKING CHANGE`:** Any commit with a `!` after the type/scope (e.g., `feat!:`) or a `BREAKING CHANGE:` footer will be prominently featured in a dedicated `BREAKING CHANGES` section.

### 2. Keep a Changelog Formatting

The output is structured according to the "Keep a Changelog" format, which is designed for readability. The skill organizes parsed commits into the following standard sections:

- **Added:** For new features.
- **Changed:** For changes in existing functionality.
- **Deprecated:** For soon-to-be-removed features.
- **Removed:** For now-removed features.
- **Fixed:** For any bug fixes.
- **Security:** In case of vulnerabilities.

### 3. Version Management & Git Integration

The skill integrates with your Git repository to manage versions and commit ranges.

- **Automatic Commit Range:** By default, it generates the changelog for all commits made since the most recent Git tag. This is ideal for generating updates for a new version.
- **Custom Commit Range:** You can specify a custom range, such as `v1.0.0..HEAD`, to generate a changelog for a specific set of changes.
- **Version Handling:** The skill uses the provided version number to create the new header in the changelog, including the release date.

### 4. File I/O Operations

The skill handles the `CHANGELOG.md` file intelligently.

- **Creation:** If no `CHANGELOG.md` exists, it creates a new one from scratch.
- **Prepending:** If a file already exists, the skill prepends the newly generated content for the latest version, preserving all previous changelog entries. This ensures a complete, chronological history of the project.

## Step-by-Step Workflow

1.  **Initiate the Skill:** The user invokes the `changelog-writer` skill.

2.  **Gather Inputs:** The skill prompts for the necessary information to begin:
    *   **Repository Path:** The local file path to the Git repository (e.g., `/home/ubuntu/my-project`).
    *   **Version Number:** The new version number for the release (e.g., `v2.1.0`). This should follow Semantic Versioning.
    *   **Commit Range (Optional):** A specific commit range (e.g., `v2.0.0..HEAD`). If omitted, the skill automatically uses the range from the latest tag to `HEAD`.

3.  **Execute Git Log:** The skill runs a `git log` command within the specified repository. The command is formatted to extract commit messages that adhere to the Conventional Commits standard.
    *   Example command: `git log --pretty=format:"%H___%s___%b" v1.5.2..HEAD`

4.  **Parse Commits:** The raw output from `git log` is processed. Each commit message is parsed to identify its type, scope, subject, and any breaking changes.

5.  **Categorize Changes:** The parsed commits are categorized into the "Keep a Changelog" sections (`Added`, `Fixed`, `Changed`, etc.). Commits that do not map to a specific user-facing category (like `chore` or `style`) are discarded.

6.  **Generate Markdown Content:** The categorized commits are formatted into a Markdown string. This includes the new version header with the release date and lists of changes under their respective headings.

7.  **Update CHANGELOG.md:**
    *   The skill reads the existing `CHANGELOG.md` file (if any).
    *   It prepends the newly generated Markdown content to the top of the file.
    *   The updated content is written back to `CHANGELOG.md`, effectively creating a new release section while preserving the old ones.

8.  **Report Completion:** The skill confirms that the `CHANGELOG.md` file has been successfully updated and provides the path to the file.

## Best Practices

- **Maintain Consistent Commit Hygiene:** For this skill to work effectively, your team must consistently follow the Conventional Commits specification. Use commit hooks or CI checks to enforce this.
- **Use Semantic Versioning:** Align your version numbers with the changes being made. Increment the MAJOR version for breaking changes, the MINOR version for new features, and the PATCH version for fixes.
- **Tag Releases in Git:** Always create a Git tag for each release (e.g., `git tag -a v1.0.0 -m "Initial release"`). This skill relies on tags to determine the range of commits for a new version.
- **Review Before Committing:** Although the process is automated, it's good practice to quickly review the generated changelog content before committing it to your repository.
- **Customize for Your Workflow:** Consider using a configuration file (e.g., `.changelogrc`) in your project to define custom commit-to-section mappings or other preferences if the skill supports it.

## Examples

### Example 1: Generating a Patch Release Changelog

Suppose your latest tag is `v1.2.0` and you have made a few commits since then to fix bugs.

**Git Log:**

```
* 2f4e6d1 (HEAD -> main) fix: resolve issue with user authentication
* a1b3c87 fix(api): correct the response status code for failed requests
* 9d8e7f6 docs: update README with new instructions
```

**Invocation:**

The user provides the repository path and the new version `v1.2.1`.

**Generated `CHANGELOG.md` Content:**

```markdown
## [1.2.1] - 2026-02-02

### Fixed

- Resolve issue with user authentication (2f4e6d1)
- **api:** Correct the response status code for failed requests (a1b3c87)
```

This content would be prepended to the existing `CHANGELOG.md`.

### Example 2: Generating a Minor Release with a Breaking Change

Suppose your latest tag is `v2.0.0` and you've added a new feature and made a refactor with a breaking change.

**Git Log:**

```
* 5a6b7c8 (HEAD -> main) feat!: add new user profile module
*
* BREAKING CHANGE: The user data structure has been completely redesigned. Old API endpoints for users are now deprecated.
* 3d4e5f6 refactor(auth): improve token validation logic
* 1a2b3c4 test: add unit tests for new profile module
```

**Invocation:**

The user provides the repository path and the new version `v3.0.0` (due to the breaking change).

**Generated `CHANGELOG.md` Content:**

```markdown
## [3.0.0] - 2026-02-02

### BREAKING CHANGES

- The user data structure has been completely redesigned. Old API endpoints for users are now deprecated. (5a6b7c8)

### Added

- Add new user profile module (5a6b7c8)

### Changed

- **auth:** Improve token validation logic (3d4e5f6)
```

## Templates

### Basic `CHANGELOG.md` Template

This is the basic structure the skill will create and maintain.

```markdown
# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.0.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [1.0.0] - 2025-10-26

### Added

- Initial release of the project.
```

### Conventional Commit Message Templates

Use these templates for your commit messages to ensure compatibility with the skill.

**Commit with `feat`:**
```
feat(newsletter): add subscription form

Adds a new form to the footer for users to subscribe to the weekly newsletter.
```

**Commit with `fix` and `scope`:**
```
fix(api): prevent crash on invalid user ID

Ensures the endpoint returns a 404 error instead of crashing the server when an invalid user ID is provided.
```

**Commit with `BREAKING CHANGE`:**
```
refactor!(auth): switch from JWT to session-based authentication

BREAKING CHANGE: Authentication tokens are no longer supported. Clients must update to use cookie-based sessions.
```

## References

- [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)
- [Conventional Commits Specification](https://www.conventionalcommits.org/en/v1.0.0/)
- [Semantic Versioning](https://semver.org/spec/v2.0.0.html)
- [Git Log Documentation](https://git-scm.com/docs/git-log)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
