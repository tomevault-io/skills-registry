---
name: knowledge-base
description: Create, organize, and manage enterprise knowledge bases. Use this skill when users want to centralize information, create a company wiki, or manage documentation. Triggers: knowledge base, wiki, documentation, centralize information, information management, base de conhecimento, gestão do conhecimento. Use when this capability is needed.
metadata:
  author: lkb-99
---

# Knowledge Base Management

## Overview
This skill empowers Manus to create, structure, and maintain comprehensive knowledge bases for businesses and teams. It is designed to centralize critical information, making it easily accessible and searchable. By leveraging this skill, you can build a single source of truth for company processes, documentation, FAQs, and best practices, thereby enhancing productivity and reducing information silos.

## Automatic Triggers

**ALWAYS activate this skill when user mentions:**
- Keywords: knowledge base, wiki, documentation, centralize information, information management, base de conhecimento, gestão do conhecimento, documentação, centralizar informação
- Phrases: "create a knowledge base", "organize company documents", "build a team wiki", "criar uma base de conhecimento", "gerenciar documentação"
- Context: Any discussion about creating, structuring, or managing a centralized repository of information for a team or company.

**Example user queries that trigger this skill:**
- "I need to create a knowledge base for my company."
- "How can I organize our internal documentation?"
- "Let's build a wiki for the engineering team."
- "Quero criar uma base de conhecimento para minha equipe."

## When to Use This Skill
This skill is particularly useful in the following scenarios:

- **Onboarding New Employees:** Quickly bring new hires up to speed by providing them with a structured repository of company information, policies, and procedures.
- **Standardizing Processes:** Document and standardize internal workflows, ensuring consistency and quality across all teams.
- **Customer Support:** Create a detailed knowledge base for customer support agents to quickly find answers to common customer questions.
- **Project Documentation:** Centralize all documentation related to a project, including requirements, meeting notes, and technical specifications.
- **Preserving Institutional Knowledge:** Capture and retain valuable knowledge from experienced employees before they leave the company.
- **Self-Service Information Portal:** Empower employees to find answers to their questions independently, reducing interruptions and freeing up expert time.

## Core Capabilities

### 1. Knowledge Base Scaffolding
This capability allows for the rapid creation of a structured knowledge base from scratch. It includes generating a directory structure and placeholder files based on a predefined template.

**Template Structure:**
```
/knowledge-base
├── /general
│   ├── company-vision-and-mission.md
│   ├── company-values.md
│   └── team-directory.md
├── /departments
│   ├── /engineering
│   │   ├── coding-standards.md
│   │   └── deployment-process.md
│   ├── /marketing
│   │   ├── brand-guidelines.md
│   │   └── social-media-policy.md
│   └── /sales
│       ├── sales-playbook.md
│       └── crm-usage-guide.md
├── /processes
│   ├── employee-onboarding.md
│   ├── expense-reporting.md
│   └── performance-review-process.md
├── /tutorials
│   ├── how-to-set-up-vpn.md
│   └── using-the-internal-wiki.md
└── README.md
```

### 2. Content Ingestion and Organization
Manus can read existing documents (Markdown, text files) and incorporate them into the knowledge base. It can also categorize and tag content to improve searchability.

**Example: Ingesting a Document**
If you have a document named `project-alpha-specs.md`, you can instruct Manus to move it to the appropriate location and add relevant metadata.

```bash
mv project-alpha-specs.md knowledge-base/departments/engineering/project-alpha-specs.md
```

### 3. Information Retrieval and Search
Users can query the knowledge base using natural language. Manus will search the content and provide the most relevant information.

**Example Query:**
"What are the coding standards for Python projects?"

Manus would search for `coding-standards.md` within the `engineering` directory and return its content.

### 4. Maintenance and Updates
This skill enables the continuous improvement of the knowledge base. Manus can identify outdated content, suggest updates, and fix broken links.

**Example: Identifying Outdated Content**
Manus can be programmed to scan for documents that haven't been updated in a specific period (e.g., 6 months) and flag them for review.

## Step-by-Step Workflow

1.  **Initialization:**
    -   Start by defining the main categories of your knowledge base. Common categories include `General`, `Departments`, `Processes`, and `Tutorials`.
    -   Use the `Bash` tool to create the main directory:
        ```bash
        mkdir knowledge-base
        ```

2.  **Scaffolding the Structure:**
    -   Based on the defined categories, create the subdirectory structure.
        ```bash
        mkdir -p knowledge-base/general knowledge-base/departments/engineering knowledge-base/processes
        ```

3.  **Creating Initial Content:**
    -   Use the `Write` tool to create initial placeholder files within each directory. For example, to create a file for the company's mission:
        ```python
        file.write(
            path="knowledge-base/general/company-vision-and-mission.md",
            text="# Company Vision and Mission\n\n*Our vision is to...*\n"
        )
        ```

4.  **Populating the Knowledge Base:**
    -   Gather existing documentation from various sources.
    -   Use the `Read` and `Write` tools to transfer this information into the newly created structure.
    -   For larger documents, you can use the `append` functionality to add content incrementally.

5.  **Adding Metadata and Tags:**
    -   At the beginning of each Markdown file, you can add a YAML front matter block for metadata.
        ```yaml
        ---
        title: "Coding Standards"
        owner: "Engineering Team"
        last-updated: "2026-02-02"
        tags: ["python", "best-practices", "linting"]
        ---
        ```

6.  **Implementing Search:**
    -   To search the knowledge base, you can use the `grep` command with the `Bash` tool.
        ```bash
        grep -r "Python projects" knowledge-base/departments/engineering/
        ```

7.  **Regular Maintenance:**
    -   Set up a recurring task to review and update the content. For example, a monthly review of all documents in the `processes` directory.
    -   Use `find` to locate files that have not been modified recently:
        ```bash
        find knowledge-base/ -type f -mtime +180
        ```

## Best Practices

-   **Keep it Simple:** Start with a simple structure and expand it as needed. Over-complicating the structure from the beginning can make it difficult to maintain.
-   **Assign Ownership:** Assign an owner to each section or document. This ensures that the content is kept up-to-date and accurate.
-   **Use a Consistent Style:** Define a style guide for all content to ensure consistency in formatting, tone, and voice.
-   **Encourage Contributions:** Make it easy for everyone on the team to contribute to the knowledge base. This could be through a simplified pull request process or a dedicated Slack channel for suggestions.
-   **Integrate with Other Tools:** Connect your knowledge base with other tools your team uses, such as Slack, Jira, or Trello, to make it more accessible.
-   **Prioritize High-Value Content:** Focus on documenting the most critical processes and information first.

## Examples

### Example 1: Creating a New Department Section

**Goal:** Add a new `Human Resources` section to the knowledge base.

**Steps:**
1.  Create the directory:
    ```bash
    mkdir -p knowledge-base/departments/human-resources
    ```
2.  Create initial documents:
    ```python
    file.write(
        path="knowledge-base/departments/human-resources/hiring-process.md",
        text="# Hiring Process\n\n*This document outlines the steps for hiring a new employee.*\n"
    )
    file.write(
        path="knowledge-base/departments/human-resources/benefits-overview.md",
        text="# Employee Benefits\n\n*Details about our health insurance, retirement plans, and other benefits.*\n"
    )
    ```

### Example 2: Finding Information

**Goal:** Find the company's policy on remote work.

**Steps:**
1.  Use the `grep` command to search for "remote work":
    ```bash
    grep -ri "remote work" knowledge-base/
    ```
2.  Manus will return the path to the relevant file and the lines containing the search term.

### Example 3: Using a Template for a New Tutorial

**Template:**
```markdown
# [Tutorial Title]

**Author:** [Your Name]
**Date:** [Current Date]

## Introduction
[Briefly describe what this tutorial covers.]

## Prerequisites
[List any software, tools, or knowledge required before starting.]

## Steps
1.  **Step 1:** [Detailed instruction]
2.  **Step 2:** [Detailed instruction]
3.  **Step 3:** [Detailed instruction]

## Troubleshooting
[List common problems and their solutions.]
```

**Usage:**
```python
# Read the template
template_content = file.read(path="templates/tutorial-template.md")

# Create a new tutorial file
file.write(
    path="knowledge-base/tutorials/how-to-use-the-new-crm.md",
    text=template_content
)
```

## References

-   [Atlassian's Guide to Knowledge Management](https://www.atlassian.com/itsm/knowledge-management)
-   [Building a Second Brain: An Overview](https://fortelabs.com/blog/basboverview/)
-   [Git-based Knowledge Base](https://www.daniele.tech/2020/05/a-git-based-knowledge-base/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/lkb-99) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
