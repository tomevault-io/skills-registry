---
name: documentation-from-code
description: Generate documentation from source code including README, API docs, and inline comments Use when this capability is needed.
metadata:
  author: daniel-silva-perez
---

# Documentation from Code Skill

## When to Use
When you need to create or update documentation from existing code. Generates README files,
API documentation, architecture overviews, and inline documentation improvements.

## Procedure
1. Read the codebase structure using `filesystem.list`
2. Identify entry points, public APIs, and key modules
3. Read source files to understand functionality
4. Extract docstrings, type hints, and comments
5. Generate documentation in appropriate format (Markdown, JSDoc, etc.)
6. Write documentation files using `filesystem.write`
7. Verify generated docs are accurate and complete

## Examples

### Example 1: Generate README
Input: "Generate a README for this project"
Walkthrough:
1. List project root files to understand structure
2. Read package.json, Cargo.toml, or similar for metadata
3. Read main source files to understand functionality
4. Identify installation steps, usage examples, and API overview
5. Generate README with sections: Install, Usage, API, Contributing, License
6. Write README.md to project root

### Example 2: API documentation
Input: "Document the REST API endpoints"
Walkthrough:
1. Find route definitions in the codebase
2. Extract HTTP methods, paths, parameters, and response types
3. Read validation schemas for request/response shapes
4. Generate OpenAPI/Swagger-style documentation
5. Include example requests and responses

---
> Source: [daniel-silva-perez/zeus](https://github.com/daniel-silva-perez/zeus) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
