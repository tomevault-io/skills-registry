---
name: code-review
description: Review code for type safety, idiomatic style, architecture, security, and performance, with concrete fixes (not vague nits). Trigger this whenever the user asks to "review", "check", "look over", "audit", "find issues in", or "PR review" some code, or pastes a diff and asks what's wrong. Works on any language with deeper Python checks (Google style, ruff, ty, PEP compliance) when the target is Python. Outputs severity-ranked findings with line numbers and before/after code. Use when this capability is needed.
metadata:
  author: NekoBend
---

# Code Review

Find real problems and write the fix. Vague feedback ("consider extracting this", "could be cleaner") wastes the author's time. Every finding gets a line number, an observed behavior, an impact, and a concrete code change.

Reason internally in English. Code references and quoted snippets stay in their original form. Final review prose goes in the user's detected language.

## Detect target language and project conventions

1. From file extension, pasted snippet syntax, or explicit user instruction, identify the language.
2. From project files (`pyproject.toml`, `package.json`, `go.mod`, `Cargo.toml`, `.editorconfig`), identify the existing toolchain. **Defer to the project's existing config** — if they use `biome` not `prettier`, review against `biome`. If they have a custom `eslint` config, check against that.
3. Run the language's standard checks first and treat their output as the starting list of findings.

| Language   | Formatter            | Linter                     | Type checker  |
|------------|----------------------|----------------------------|---------------|
| Python     | `ruff format --check` | `ruff check --select ALL` | `ty check`    |
| TypeScript | `prettier --check`   | `eslint`                   | `tsc --noEmit` |
| Go         | `gofmt -l`           | `golangci-lint run`        | (built-in)    |
| Rust       | `rustfmt --check`    | `clippy`                   | (built-in)    |
| Java       | `google-java-format --dry-run` | `checkstyle`     | (built-in)    |
| C#         | `dotnet format --verify-no-changes` | `dotnet analyzers` | (built-in) |
| Shell      | `shfmt -d`           | `shellcheck`               | N/A           |

## Severity rubric

| Severity   | Trigger                                                        |
|------------|----------------------------------------------------------------|
| 🔴 High    | Security risk, data loss, crash, undefined behavior, broken contract |
| 🟡 Medium  | Type gaps, missing error handling, performance issues on hot paths   |
| 🔵 Low     | Style, naming, doc gaps, organizational suggestions                  |

When at least one 🔴 High exists, collapse all 🔵 Low findings into a single summary row — the author needs to focus.

## Review criteria

**1. Type safety (often the highest-leverage finding).** Flag every untyped escape hatch (`Any`, `any`, `object`, `interface{}`) and propose a specific replacement type — `TypedDict`, `Protocol`, generics, discriminated union. Flag missing return types and missing parameter types on public functions. At public API boundaries and deserialization points, suggest runtime validation (Pydantic, `isinstance`, schema validators).

**2. Style and readability.** Enforce the language's official style guide and naming conventions (Python: snake_case; TypeScript / Java / C# / Go: camelCase / PascalCase per language). Flag missing docs on public APIs. Flag comments that restate the code, decorative separators, or non-English comments. Flag chained ternaries (≥ 2), nested comprehensions, single-letter names outside conventional cases, functions > 50 lines of executable code, nesting ≥ 4 levels, cyclomatic complexity ≥ 10. Identify dead code (unused imports, unreachable branches).

**3. Architecture.** Flag classes with > 5 public methods or > 200 lines (likely SRP violation). Flag direct instantiation of external dependencies in business logic (DI violation). Prefer immutable data structures where the language supports them cheaply. Catch the narrowest exception / error type — no catch-all outside top-level boundaries.

**4. Performance and security.** O(n²) or worse in loops on user-sized data. Synchronous I/O in async contexts. Resources not closed (no context manager, `defer`, `using`, or RAII). SQL injection (string-interpolated queries), hardcoded secrets, `eval` / `exec` / `Function()`, unsafe deserialization (`pickle`, `yaml.load`), path traversal. N+1 query patterns when an ORM is in use.

## Anti-pattern checklist

These are the recurring offenders. Look for each on every review:

1. **Untyped escape hatch** — `Any`, `any`, `object`, `interface{}` to bypass the type checker.
2. **Unsafe defaults** — Python mutable default args (`def f(items=[])`); JS/TS mutating default object params; Go nil slice/map assumptions.
3. **God function/class** — > 5 public methods or > 200 lines on a class; > 50 lines on a function.
4. **Catch-all error handling** — bare `except:`, `except Exception:`, `catch(e)` without narrowing, ignored Go `error`, `.unwrap()` in library code, `catch (Exception e)` in Java.
5. **Hardcoded config** — paths, secrets, URLs, timeouts inline.
6. **N+1 query** — sequential queries in loops when an ORM supports batching or eager loading.

For each, the fix is specific, not generic. Don't write "use proper error handling" — write "catch `ValueError` here; promote to a `ParseError` so the caller can distinguish parse failures from missing data".

## Workflow

1. **Run the tools.** Format check, lint, type check. Capture findings.
2. **Type-safety pass.** Walk every parameter and return. Flag escape hatches with specific replacements.
3. **Architecture pass.** Look for SRP, DI, error-handling shape, immutability opportunities.
4. **Security and performance pass.** Walk anti-pattern checklist. Verify resource lifecycles.
5. **Style pass.** Naming, doc presence, comment quality, dead code.
6. **Severity assignment.** Map each finding to 🔴 / 🟡 / 🔵 using the rubric. If no findings, say "No critical findings" and list residual risks instead of inventing nits.
7. **Compose review.** Sort by severity. Each finding: location, observed behavior, impact, concrete fix. For files > 200 lines, do passes in order: safety → correctness → maintainability → style.

For 🔴 findings involving SQL injection, credential exposure, data deletion, or unsafe deserialization, double-check the suggested fix internally before publishing — these are the cases where a wrong recommendation does real damage.

## Python specifics

When reviewing Python, also enforce:

- **Google Python Style Guide + PEP 8 / 257 / 484.** Variable naming (snake_case), class naming (PascalCase), line length per project config.
- **Modern syntax for the project's Python version.** Check `python_requires` or `[tool.ruff] target-version`. Suggest `list[int]` over `typing.List` (3.9+), `X | Y` over `Union` (3.10+), `type` statement (3.12+) when applicable.
- **Google-style docstrings.** One-line summary in imperative mood. `Args` format `name: Description.` (no types in docstring — they're in annotations). `__init__` args go in the class docstring. Flag missing docstrings on public functions, classes, modules.
- **Async hygiene.** Flag `open()`, `requests.get()`, `time.sleep()` inside `async def`. Suggest `aiofiles`, `httpx.AsyncClient`, `asyncio.sleep()`.
- **Pythonic patterns.** Suggest comprehensions over manual loops, context managers over manual close, `pathlib` over `os.path`, `enumerate` over manual indices, EAFP over LBYL where appropriate.

## Output format

````markdown
## 📊 Review Summary
- **Quality Score:** [1-10]/10 — (10: no findings; 8-9: 🔵 only; 6-7: ≤ 3 🟡; 4-5: any 🔴; 1-3: multiple 🔴 or security breach)
- **Status:** [Approved | Changes Requested | Critical Issues]
- **Key Strengths:** <bullets>
- **Critical Issues:** <bullets>

## 🔍 Detailed Analysis

| Severity | Location  | Issue                          | Recommendation                  |
|:--------:|:---------:|:-------------------------------|:--------------------------------|
| 🔴 High  | file.ts:12 | <observed behavior>            | <concrete fix>                  |
| 🟡 Med   | file.ts:45 | <observed behavior>            | <concrete fix>                  |
| 🔵 Low   | file.ts:88 | <observed behavior>            | <concrete fix>                  |

## 💡 Refactoring Suggestions

### 1. <Issue Title>
**Current:**
```<lang>
<before>
```
**Recommended:**
```<lang>
<after>
```
**Reasoning:** <why — reference the rule, PEP, RFC, or design principle>

## 🛡️ Security & Performance Notes
- <specific finding or "no concerns">
````

If no critical findings, replace the table with `**No critical findings.** Residual risks: <list>` rather than fabricating issues.

## Don't

- Flag style nits when there are unaddressed 🔴 findings — collapse them into a summary row.
- Recommend deprecated syntax (`typing.List` on 3.9+, `var` over `let` in TS, `interface{}` over generics in modern Go).
- Write "consider X" without committing to whether X is recommended. The author wants a verdict.
- Flag a violation that would require restructuring > 20 lines of unrelated code as a required change — flag it as a suggestion instead.
- Cite a "violation" without a specific line number and concrete failure mode.
- Recommend insecure shortcuts (disabling type checks, broadening exception handlers) to make a finding "pass".

## Example

**Input:**
```typescript
function processData(data: any[]) {
  let results: any[] = [];
  for (let i = 0; i < data.length; i++) {
    if (data[i].active) {
      results.push(data[i].value * 2);
    }
  }
  return results;
}
```

**Review:**

## 📊 Review Summary
- **Quality Score:** 5/10
- **Status:** Changes Requested
- **Critical Issues:** Untyped `any` parameter and return; missing return type; non-idiomatic loop.

## 🔍 Detailed Analysis

| Severity | Location | Issue                                      | Recommendation                          |
|:--------:|:--------:|:-------------------------------------------|:----------------------------------------|
| 🔴 High  | file.ts:1 | `data: any[]` bypasses type safety        | Define `DataItem` interface, use `readonly DataItem[]` |
| 🔴 High  | file.ts:2 | `results: any[]` loses type information   | Use `number[]`                          |
| 🟡 Med   | file.ts:1 | Missing return type annotation            | Add `: number[]`                        |
| 🔵 Low   | file.ts:3 | C-style `for` loop is non-idiomatic       | Use `.filter().map()` chain             |

## 💡 Refactoring Suggestions

### 1. Type safety + idiomatic style
**Current:**
```typescript
function processData(data: any[]) { /* ... */ }
```
**Recommended:**
```typescript
interface DataItem {
  active: boolean;
  value: number;
}

/** Filter active items, double their values. */
function processData(data: readonly DataItem[]): number[] {
  return data.filter((item) => item.active).map((item) => item.value * 2);
}
```
**Reasoning:** `DataItem` replaces `any` — gains compile-time checking and IDE autocompletion. `readonly` prevents accidental mutation of the input. `.filter().map()` removes a class of off-by-one risks the C-style loop carries.

---
> Source: [NekoBend/dotfiles](https://github.com/NekoBend/dotfiles) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-22 -->
