---
name: zig
description: Quality-first Zig 0.15.1 code generation. Loads stdlib context, generates small pieces, tests, reviews for idioms and slop, refactors. Use for any Zig coding task. Use when this capability is needed.
metadata:
  author: atlantropaz
---

# Zig Development Workflow

You are generating Zig 0.15.1 code. Follow this process strictly.

## CRITICAL: Test File Separation (ENFORCED)

**NEVER put `test` blocks in `src/` or `examples/` files. The workflow REJECTS task completion if violated.**

```
src/           # Implementation ONLY - zero test blocks allowed
  wal.zig      # NO `test "..."` blocks
  snapshot.zig # NO `test "..."` blocks
examples/      # Demo apps ONLY - zero test blocks allowed
  kv_demo.zig  # NO `test "..."` blocks
tests/         # ALL tests go here
  wal_test.zig
  snapshot_test.zig
  kv_demo_test.zig
```

When implementing a function in `src/` or `examples/`:
- Generate ONLY the implementation
- Do NOT generate an accompanying test block
- Tests are a SEPARATE task targeting `tests/*_test.zig`

**Enforcement:** The workflow script runs `grep -r '^test "' src/ examples/` after every task.
If ANY match is found, the task completion is REJECTED and you must retry.

**Verification before completing:**
```bash
grep -rn "^test \"" src/ examples/
```
This MUST return nothing. If it returns anything, move those tests to `tests/` first.

---

## Before Writing Any Code

### 1. Load Context

Read relevant stdlib source files from `/Users/liangchenzhou/.zvm/0.15.1/lib/std/`:

| Task | Read These Files |
|------|------------------|
| File I/O | `std/fs/File.zig`, `std/Io.zig` |
| Memory/Allocators | `std/mem.zig`, `std/heap.zig` |
| Data structures | `std/array_list.zig`, `std/hash_map.zig` |
| Testing | `std/testing.zig` |
| Hashing/CRC | `std/hash.zig`, `std/hash/crc.zig` |

Read at least 200 lines of relevant stdlib code before generating. Pattern-match off real 0.15.x code, not memory.

### 2. Read Existing Project Code

Before modifying or adding to existing files, read them first. Match:
- Error handling patterns
- Naming conventions
- Code organization
- Test structure

## Project Structure

See **CRITICAL: Test File Separation** at the top of this document.

- Tests import from src: `@import("../src/wal.zig")` or via build.zig module

## Generation Rules

### One Thing at a Time

Generate ONE of these per request:
- A single function (max 20 lines)
- A single struct with its methods
- A single test case (in tests/ directory)

Never generate multiple unrelated pieces at once.

### Constraints (Always Apply)

- No function longer than 20 lines
- No nesting deeper than 2 levels
- No code duplication
- No defensive code for impossible cases
- No unnecessary abstractions
- No verbose error handling unless errors are expected at runtime
- Use `const` by default, `var` only when mutation required

### Zig 0.15.x Specifics

**File I/O** - Use new concrete types:
```zig
// Correct (0.15.x)
var write_buf: [4096]u8 = undefined;
var file_writer = file.writer(&write_buf);
const writer = &file_writer.interface;

// Wrong (deprecated)
const writer = file.writer();
```

**Buffers** - BoundedArray removed:
```zig
// Correct (0.15.x)
var buffer: [capacity]T = undefined;
var list = std.ArrayListUnmanaged(T).initBuffer(&buffer);

// Wrong (removed)
var arr = std.BoundedArray(T, capacity){};
```

**Error types** - Use concrete errors:
- `std.Io.Writer.Error`
- `std.Io.Reader.Error`

**Version check** - Verify you're on 0.15.1:
```bash
zig version  # should output 0.15.1
```

## After Generation

### Run Tests Immediately

```bash
zig build test
```

If tests fail, fix before proceeding. Never move on with failing tests.

### Quality Review

After tests pass, review the generated code for:

1. **Unnecessary complexity** - Can this be simpler?
2. **Redundant logic** - Is anything repeated?
3. **Non-idiomatic patterns** - Does this look like stdlib code?
4. **Inconsistent naming** - Does it match the project?
5. **Over-engineering** - Is there code that isn't needed?
6. **Test separation violation** - Run this command as a fail-safe:
   ```bash
   grep -rn "^test \"" src/ examples/
   ```
   If ANY output appears, move those tests to `tests/*_test.zig` before proceeding.
   **This is enforced by the workflow script - task completion is rejected if violated.**

Ask: "Is there anything here that could be removed?"

### Refactor

Simplify without changing behavior. Run tests again after refactoring.

## Anti-Slop Checklist

Before considering any code complete:

- [ ] Read stdlib source for this feature area
- [ ] Read existing project code for patterns
- [ ] Generated in small, focused pieces
- [ ] Tests pass
- [ ] Quality review completed
- [ ] Refactoring pass completed
- [ ] No unnecessary code remaining
- [ ] Matches project style
- [ ] **Zero test blocks in src/ or examples/** - `grep -rn "^test \"" src/ examples/` returns nothing

## Error Recovery

If `zig build test` fails:
1. Read the full error message
2. Check if it's a 0.15.x API change
3. If unsure, re-read the relevant stdlib source
4. Fix and re-run - don't guess

If you're unsure about 0.15.x patterns, say so and read more stdlib.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/atlantropaz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
