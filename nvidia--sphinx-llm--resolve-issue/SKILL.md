---
name: resolve-issue
description: | Use when this capability is needed.
metadata:
  author: nvidia
---

# Resolve GitHub Issue

This skill guides you through resolving a GitHub issue for the sphinx-llm
project using a test-driven development workflow.

## Instructions

When the user asks you to resolve a GitHub issue, follow these steps:

### 1. Fetch Issue Information

First, retrieve the issue details:

**If `gh` CLI is available:**

```bash
gh issue view <issue-number> --repo NVIDIA/sphinx-llm
```

**If `gh` CLI is not available:**

- Use WebFetch to fetch the issue from
  `https://github.com/NVIDIA/sphinx-llm/issues/<issue-number>`
- Extract the title, body, and labels

### 2. Check for `ready` Label

**IMPORTANT:** Before proceeding with implementation, check if the issue has
the `ready` label.

- **If the `ready` label is present:** Proceed with implementation
- **If the `ready` label is missing:** Stop and inform the user:
  > "This issue does not have the `ready` label, which indicates it likely
  > requires more discussion before implementation. Please ensure the issue has
  > been discussed and approved by maintainers before proceeding."

### 3. Test-Driven Development Workflow

Once you've confirmed the issue is ready for implementation, follow this TDD workflow:

#### Step 3a: Write Tests First

- Review the existing test structure in `src/sphinx_llm/tests/`
- Write new test(s) that verify the desired functionality described in the
  issue
- The tests should **fail initially** (since the feature isn't implemented yet)
- Run the tests to confirm they fail as expected:

  ```bash
  uv run pytest src/sphinx_llm/tests/ -v
  ```

#### Step 3b: Implement the Feature

- Implement the minimum code necessary to make the tests pass
- Follow the project's architecture patterns (see CLAUDE.md)
- Run tests frequently to verify progress:

  ```bash
  uv run pytest src/sphinx_llm/tests/ -v
  ```

- Ensure all tests pass before proceeding

#### Step 3c: Run Linting and Formatting

- Run pre-commit hooks to ensure code quality:

  ```bash
  pre-commit run --all-files
  ```

- Fix any issues identified by the linters

### 4. Update Documentation

After the implementation is complete and tests are passing:

- Update relevant documentation in `docs/source/`
- If the feature adds new configuration options, document them in the
  appropriate `.rst` files
- If the feature adds new directives or extensions, add examples to the docs
- Build the docs locally to verify changes:

  ```bash
  uv run --dev sphinx-build docs/source docs/build/html
  ```

## Example Usage

User: "Resolve issue #42"

You should:

1. Run `gh issue view 42 --repo NVIDIA/sphinx-llm` (or use WebFetch)
2. Check for the `ready` label
3. If ready, write tests in `src/sphinx_llm/tests/`
4. Implement the feature
5. Update docs in `docs/source/`
6. Run tests and linting

## Notes

- Follow the existing code style and patterns
- Keep changes focused on the specific issue
- Don't over-engineer - implement only what's needed for the issue

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/nvidia) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
