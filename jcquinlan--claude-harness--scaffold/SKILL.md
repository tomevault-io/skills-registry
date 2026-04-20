---
name: scaffold
description: Choose language, framework, and test runner for a project. Creates project.json, initializes the project, and updates CLAUDE.md code style. Run after /create-prd and before /architect. Use when this capability is needed.
metadata:
  author: jcquinlan
---

# Scaffold Project

You are setting up the technical foundation for a project. The user's project description is:

**$ARGUMENTS**

Your goal is to create `project.json` — the single source of truth for how this project is built, tested, and run. Every hook, agent, and skill in the harness reads this file.

## Step 1: Analyze Requirements

Read the project description (and `prd.json` if it exists) to understand:
- What kind of project is this? (CLI tool, web app, API, library, data pipeline)
- Does it need a web framework? A database? A build step?
- Are there performance, deployment, or ecosystem constraints?

## Step 2: Choose Technologies

For each category, pick the simplest option that meets requirements:

| Category | Question | Examples |
|----------|----------|----------|
| **Language** | What language fits best? | TypeScript, Python, Rust, Go, Java |
| **Runtime** | What runs the code? | Bun, Node.js, Deno, Python 3, cargo |
| **Framework** | Does it need one? | Express, FastAPI, Actix, Gin, none |
| **Package manager** | How are deps installed? | npm, pip, cargo, go mod |
| **Test runner** | How are tests run? | vitest, pytest, cargo test, go test |
| **Build command** | Is a build step needed? | tsc, cargo build, go build, none |

Prefer the simplest option. Don't add a framework if the project is a CLI tool. Don't add a build step if the language is interpreted.

Present your recommendations to the user and ask for confirmation before proceeding.

## Step 3: Create `project.json`

Write `project.json` in the current working directory:

```json
{
  "language": "<language>",
  "runtime": "<runtime>",
  "framework": "<framework or null>",
  "package_manager": "<package manager>",
  "test_runner": "<test runner>",
  "test_command": "<exact command to run unit tests>",
  "browser_test_command": "<exact command to run browser tests, or null>",
  "build_command": "<exact build command, or null>",
  "init_command": "./init.sh",
  "source_extensions": ["<.ext1>", "<.ext2>"],
  "test_extensions": ["<.test.ext>", "<.spec.ext>"],
  "entry_point": "<main entry file>",
  "src_dir": "<source directory>",
  "test_dir": "<test directory>"
}
```

The `test_command` field is critical — hooks run this exact string. Make sure it:
- Runs all tests, not just one file
- Returns exit code 0 on success, non-zero on failure
- Works from the project root directory

## Step 4: Initialize the Project

Based on the chosen tech stack:

1. Create the project structure (src dir, test dir, config files)
2. Initialize the package manager if needed (`npm init -y`, `cargo init`, etc.)
3. Install the test runner and any core dependencies
4. Create a minimal test file that runs and passes (proves the test runner works)

## Step 5: Update CLAUDE.md Code Style

Edit the `## Code Style` section of `CLAUDE.md` to reflect the chosen technologies. Replace any placeholder or default content with project-specific conventions:

```markdown
## Code Style

- <Language>: <Runtime>, <module system or conventions>
- <Framework conventions if applicable>
- Functions over classes for simple utilities
- Early returns for error handling (guard clauses, not deep nesting)
```

Also update the `## Testing` section to reference the actual test command:

```markdown
## Testing

- Tests are derived from PRD steps: each step maps to one or more assertions
- `"unit"` features are tested via `<test_command from project.json>`
- `"browser"` features are tested via Playwright MCP tools
- `"both"` features require passing both unit and browser tests
- Run the full suite after every change, not just the new feature's tests
- Tests must be deterministic
```

## Step 6: Verify

Run the test command to confirm the scaffold works:
- The test runner executes without errors
- At least one placeholder test passes
- `project.json` is valid JSON and `jq` can read it

Report the result and suggest running `/architect` next to design the domain model.

## Output

After completing all steps, summarize:
- Language/runtime chosen and why
- Test runner and command
- Project structure created
- What to do next (`/architect` → test-writer agent → `loop.sh`)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jcquinlan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
