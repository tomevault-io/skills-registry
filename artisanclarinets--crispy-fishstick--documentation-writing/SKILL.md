---
name: documentation-writing
description: Guidelines for writing clear, comprehensive, and technically accurate documentation for Project SENTINEL. Use when creating READMEs, API docs, or internal engineering guides. Use when this capability is needed.
metadata:
  author: artisanclarinets
---

# Documentation Writing

This skill defines the standards for technical communication within the Vantus Systems platform. It ensures that all documentation is actionable, consistent, and accessible to both developers and stakeholders.

## Quick Start

Create a new documentation file in 4 steps:

1.  **Define Audience**: Identify if the doc is for developers, admins, or end-users.
2.  **Choose Format**: Use Markdown (`.md`) for general docs and MDX (`.mdx`) for interactive content.
3.  **Structure Content**: Use clear headings, bullet points, and code blocks.
4.  **Review & Refine**: Ensure technical accuracy and clarity through peer review.

```markdown
# Example: API Integration Guide

## Overview
This guide explains how to integrate with the Project SENTINEL Admin API.

## Authentication
All requests must include a valid JWT in the `Authorization` header.

```bash
curl -H "Authorization: Bearer <token>" https://api.vantus.com/v1/projects
```
```

## Core Concepts

### 1. Clarity & Conciseness
*   **Direct Language**: Use active voice and avoid unnecessary jargon.
*   **Actionable Steps**: Provide clear, numbered instructions for tasks.

### 2. Technical Accuracy
*   **Code Examples**: Always include tested, up-to-date code snippets.
*   **Diagrams**: Use Mermaid or hosted images to explain complex architectures.

### 3. Consistency
*   **Terminology**: Use consistent terms for system entities (e.g., always use "Tenant", not "Organization").
*   **Formatting**: Follow the project's Markdown style guide for headings, lists, and links.

## Documentation Workflows

### Creating a New Engineering Guide

**Step 1: Outline the Problem**
Start with a clear "Why" and "What" before diving into the "How".

**Step 2: Provide Step-by-Step Instructions**
Break down complex processes into manageable steps. Use the `updateTodoList` pattern to show progress.

**Step 3: Include Troubleshooting Tips**
Document common errors and their solutions to reduce support overhead.

```markdown
### Troubleshooting
If you encounter a `403 Forbidden` error, verify that your user has the `resource.read` permission.
```

### Maintaining READMEs
Ensure every major directory has a `README.md` explaining its purpose, structure, and key files.

## Advanced Patterns

### Interactive Documentation (MDX)
Leverage custom components like `<Callout />` or `<Metric />` to highlight critical information or data points.

### Versioning & Changelogs
Maintain a `CHANGELOG.md` at the root to track breaking changes, new features, and bug fixes.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artisanclarinets) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
