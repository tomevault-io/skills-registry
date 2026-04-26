---
name: release-notes
description: Create professional release notes for software products. Use this skill when users want to generate, write, or format release notes, changelogs, or update announcements. Triggers: release notes, changelog, what's new, software update, version update, notas de versão, changelog, nova versão. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Skill: Professional Release Notes Generation

## Overview
This skill is designed to automate the creation of comprehensive and professional release notes for software products. It streamlines the process of gathering information about new features, bug fixes, and improvements, and then formats this information into a clear, concise, and well-structured document. This is particularly useful for development teams that need to communicate changes to users, stakeholders, or internal teams in a consistent and timely manner.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: release notes, changelog, what's new, software update, version update, notas de versão, changelog, nova versão, atualizações de software
- Phrases: "create release notes", "gerar notas de versão", "what's new in this version", "escrever um changelog"
- Context: Any discussion about documenting changes in a new software version.

**Example user queries that trigger this skill:**
- "Preciso criar as notas de versão para a versão 2.5 do nosso app."
- "How to write a good changelog?"
- "Generate release notes from my git commits."

## When to Use This Skill
This skill is most effective in the following scenarios:

- **New Version Release:** When a new version of a software product is ready for deployment.
- **Regular Updates:** For products that have a regular update cycle (e.g., weekly, bi-weekly, or monthly).
- **Internal Communication:** To inform internal teams (e.g., support, marketing, sales) about upcoming changes.
- **Public Announcements:** To communicate updates to end-users and the public.
- **Beta/Alpha Releases:** To provide detailed information to testers and early adopters.

## Core Capabilities

### 1. Information Gathering
This skill can gather information from various sources to build the release notes. It can read from commit logs, project management tools (like Jira or Trello), and internal documentation.

- **Git Log Parsing:** Extracts commit messages and organizes them by type (feature, fix, chore, etc.).
- **Issue Tracker Integration:** Fetches data from issue trackers to get details about completed tasks.
- **Changelog Analysis:** Reads existing CHANGELOG.md files to identify recent changes.

### 2. Content Structuring
It organizes the gathered information into a structured format. The default structure includes sections for New Features, Bug Fixes, Improvements, and Known Issues.

- **Categorization:** Automatically categorizes changes based on predefined keywords (e.g., 'feat:', 'fix:', 'docs:').
- **Formatting:** Applies consistent formatting to the release notes, including headings, lists, and code blocks.
- **Custom Templates:** Allows the use of custom templates to match the branding and style of the product.

### 3. Output Generation
Generates the final release notes in various formats, including Markdown, HTML, and plain text.

- **Markdown:** The default output format, suitable for most platforms like GitHub, GitLab, and developer portals.
- **HTML:** For embedding in websites or sending as an email.
- **Plain Text:** A simple format for easy distribution.

## Step-by-Step Workflow

1.  **Initialize the Skill:** Start by invoking the skill and specifying the version number for the release.

2.  **Configure Data Sources:** Provide the necessary information for the skill to gather data. This may include:
    -   Git repository path.
    -   API keys for project management tools.
    -   Paths to relevant documentation.

3.  **Gather Information:** The skill will execute the necessary commands to collect the data. This may involve:
    -   Running `git log` with specific formatting.
    -   Making API calls to Jira, Trello, etc.
    -   Reading from specified files.

4.  **Review and Edit:** The skill will present the gathered information in a draft format. You can review, edit, and add any missing details.

5.  **Generate Release Notes:** Once the content is finalized, the skill will generate the release notes in the desired format.

6.  **Distribute:** The final output can be saved to a file, copied to the clipboard, or sent to a specified destination.

## Best Practices

-   **Consistent Commit Messages:** Encourage the development team to follow a consistent format for commit messages (e.g., Conventional Commits). This greatly improves the accuracy of the information gathering process.
-   **Detailed Issue Tracking:** Ensure that issues and tasks in the project management tool are well-documented with clear titles and descriptions.
-   **Use a Template:** Create a release notes template that aligns with your product's branding and communication style. This ensures consistency across all releases.
-   **Review Before Publishing:** Always review the generated release notes for accuracy, clarity, and tone before publishing them.
-   **Audience-Specific Content:** Tailor the language and level of detail to the intended audience. For example, release notes for end-users should be less technical than those for internal developers.

## Examples

### Example 1: Generating Release Notes from a Git Log

**Command:**
```bash
release-notes --from-git --version 1.2.0
```

**Git Log (simplified):**
```
feat: Add user authentication
fix: Correct spelling in the welcome email
docs: Update README with new instructions
feat: Implement dark mode
```

**Generated Release Notes (Markdown):**
```markdown
# Release Notes - Version 1.2.0

## New Features
- Add user authentication
- Implement dark mode

## Bug Fixes
- Correct spelling in the welcome email

## Documentation
- Update README with new instructions
```

### Example 2: Using a Custom Template

**Template File (`release-template.md`):**
```markdown
# {{version}} - {{release_date}}

## What's New

{{new_features}}

## Improvements

{{improvements}}

## Bug Fixes

{{bug_fixes}}
```

**Command:**
```bash
release-notes --from-jira --version 2.0.0 --template release-template.md
```

**Generated Release Notes:**
```markdown
# 2.0.0 - 2024-08-01

## What's New

- [PROJ-123] Add support for single sign-on (SSO)
- [PROJ-125] Introduce a new dashboard for analytics

## Improvements

- [PROJ-120] Improve the performance of the main search query

## Bug Fixes

- [PROJ-128] Fix a bug where the user could not reset their password
```

## Ready-to-Use Templates

### Template 1: Simple Release Notes

```markdown
--- 
name: release-notes-simple
description: A simple template for release notes.
---

# Release Notes: Version {{version}}

**Release Date:** {{release_date}}

## New Features

- {{new_features}}

## Bug Fixes

- {{bug_fixes}}

## Improvements

- {{improvements}}
```

### Template 2: Detailed Release Notes with Sections

```markdown
--- 
name: release-notes-detailed
description: A detailed template for release notes with multiple sections.
---

# Version {{version}} - {{release_date}}

## Highlights

- {{highlights}}

## New Features

{{new_features}}

## Enhancements

{{enhancements}}

## Bug Fixes

{{bug_fixes}}

## Known Issues

- {{known_issues}}

## Deprecations

- {{deprecations}}
```

### Template 3: Release Notes for a SaaS Product

```markdown
--- 
name: release-notes-saas
description: A template for SaaS product release notes, focused on user benefits.
---

# New in [Your Product Name] - {{month}} {{year}}

Hi {{user_name}},

We've been working hard to make [Your Product Name] even better for you. Here's what's new this month:

### ✨ New: [Feature 1 Name]

[Brief, benefit-oriented description of the feature.]

[Link to documentation or a blog post about the feature.]

### ✨ New: [Feature 2 Name]

[Brief, benefit-oriented description of the feature.]

[Link to documentation or a blog post about the feature.]

### 🚀 Improvements

- [Improvement 1]
- [Improvement 2]

### 🐞 Bug Fixes

- [Bug fix 1]
- [Bug fix 2]

Thanks for being a valued customer!

The [Your Company Name] Team
```

## References

-   [Conventional Commits](https://www.conventionalcommits.org/)
-   [Keep a Changelog](https://keepachangelog.com/en/1.0.0/)
-   [GitHub Docs: Managing Releases](https://docs.github.com/en/repositories/releasing-projects-on-github/managing-releases-in-a-repository)
-   [Atlassian: Writing Good Release Notes](https://www.atlassian.com/continuous-delivery/principles/writing-good-release-notes)

This skill provides a powerful and flexible way to manage release communications, saving time for development teams and providing clear, valuable information to all stakeholders.

## Advanced Usage

### Filtering Commits

You can filter commits by author, date, or message content to create more targeted release notes.

**Example:**
```bash
release-notes --from-git --version 1.3.0 --author "John Doe" --since "2 weeks ago"
```

This command will only include commits from "John Doe" in the last two weeks.

### Integrating with CI/CD

This skill can be integrated into a Continuous Integration/Continuous Deployment (CI/CD) pipeline to automate the release notes process entirely.

**Example (GitHub Actions):**
```yaml
name: Create Release Notes

on:
  push:
    tags:
      - 'v*'

jobs:
  release:
    runs-on: ubuntu-latest
    steps:
      - name: Checkout code
        uses: actions/checkout@v2

      - name: Generate Release Notes
        run: |
          # Install the release-notes tool
          pip install release-notes-tool

          # Generate the release notes
          release-notes --from-git --version ${{ github.ref_name }} > RELEASE_NOTES.md

      - name: Create GitHub Release
        uses: actions/create-release@v1
        env:
          GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
        with:
          tag_name: ${{ github.ref_name }}
          release_name: Release ${{ github.ref_name }}
          body_path: RELEASE_NOTES.md
```

### Custom Parsers

For non-standard commit messages or data sources, you can create custom parsers to extract the information you need.

**Example (Python):**
```python
import re

def parse_custom_log(log_text):
    features = []
    fixes = []

    for line in log_text.splitlines():
        if line.startswith("[FEATURE]"):
            features.append(line.replace("[FEATURE]", "").strip())
        elif line.startswith("[FIX]"):
            fixes.append(line.replace("[FIX]", "").strip())

    return {"new_features": features, "bug_fixes": fixes}
```

This skill is a comprehensive solution for automating the creation of release notes, designed to be flexible and adaptable to the needs of any software project. By leveraging this skill, teams can ensure that their release communication is always professional, consistent, and up-to-date.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
