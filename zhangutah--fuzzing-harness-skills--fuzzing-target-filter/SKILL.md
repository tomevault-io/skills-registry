---
name: fuzzing-target-filter
description: > Use when this capability is needed.
metadata:
  author: zhangutah
---


## Purpose

Identify fuzzing harnesses that may produce low-quality results due to:
- Invalid API usage patterns
- Unreachable or trivially-wrapped internal functions
- Memory safety and resource management issues
- Non-deterministic behavior

## When to Use This Skill

- Before integrating new fuzzing harnesses
- When triaging crashes to determine if they represent real bugs vs. harness artifacts
- When auditing existing fuzz targets for quality
- After initial harness development to validate design decisions

## Evaluation Criteria

### I. Valid API Usage

**Required characteristics:**

- Conforms to documented call order and lifecycle: init → configure → use → cleanup
- Mirrors real client flows, not synthetic minimal calls
- Uses documented defaults where possible
- Avoids undefined or "accidentally working" sequences

**Evaluation checks:**

1. **Manpage alignment**: Each API call should map to a specific manpage section
2. **Call-order validation**: Reject harnesses that skip required setup steps
3. **Header conformance**: API usage matches declarations in public headers

**Red flags:**
- Calling functions without required initialization
- Using deprecated or undocumented APIs
- Ignoring return values from critical functions

### II. Internal Function Fuzzing & Reachability Validation

#### 2.1 Internal Functions as Valid Targets

Internal (non-API) functions may be valid targets when:
- They contain complex parsing, decoding, or state logic
- They are historically bug-dense
- They sit below thin public wrappers
- They are performance-critical or memory-sensitive

**Fuzzing internal functions is acceptable only when real-world reachability is established.**

#### 2.2 Bottom-Up Reachability Verification

When fuzzing an internal function directly, verify reachability:

**Step 1 — Identify the Target Function**
```bash
# Check if function symbol exists
nm -D library.so | grep target_function
# Or for static analysis
grep -rn "target_function" --include="*.c" --include="*.h"
```

**Step 2 — Build the Caller Chain**

Using tools like cflow, cscope, or grep:
```bash
# Find direct callers
grep -rn "target_function(" --include="*.c"
# Build call graph with cflow
# realted package required: apt install cflow graphviz
cflow --main=public_api src/*.c 2>/dev/null | grep -A5 target_function
```

**Step 3 — Validate Entry Point Reachability**

The chain must end at:
- A program `main()` in a shipped binary
- A documented library callback
- A framework-registered handler
- A unit/integration test that mirrors production logic

#### 2.3 Filtering Trivial & OS-Primitive Wrappers

**Excluded Target Categories:**

| Category | Examples | Reason |
|----------|----------|--------|
| Memory ops | `memcpy`, `memset`, `strlen`, `strdup` | Heavily tested by OS vendors |
| Allocation | `malloc`, `calloc`, `realloc`, `free` | Low bug density |
| File I/O | `open`, `close`, `read`, `write`, `fopen` | Exercises libc, not app logic |
| String utils | `strcmp`, `strncmp`, `strcat` | Poor signal quality |

**Acceptable exceptions:** Wrappers that add:
- Custom error handling or retry logic
- Format conversion or validation
- Security checks or sandboxing
- Stateful resource management

### IV. Runtime Correctness & Object Lifecycle

#### Memory Safety & Resource Management

**Requirements:**
- All allocated resources are explicitly released (heap, file descriptors, sockets)
- Correct allocator/deallocator pairs are used
- No reliance on process teardown for cleanup

**Check for:**
```bash
# Run with AddressSanitizer to detect leaks
ASAN_OPTIONS=detect_leaks=1 ./fuzzer -max_total_time=30
```

#### Determinism & Stability

**Requirements:**
- Same input produces the same behavior
- No dependency on wall-clock time, random seeds, or environment-specific state

**Test determinism:**
```bash
# Run twice with same input, compare behavior
./fuzzer input.bin 2>&1 | md5sum
./fuzzer input.bin 2>&1 | md5sum
# Should produce identical hashes
```

## Quick Coverage Analysis (Without Recompilation)

Get initial function coverage feedback from an existing LibFuzzer binary without `llvm-cov`:

### Command

```bash
/out/fuzz_target -max_total_time=10 -print_coverage=1 /path/to/corpus
```

### Example Output

```
COVERED_FUNC: hits: 31 edges: 9/11 ber_tag_and_rest decode.c:0
  UNCOVERED_PC: decode.c:0
  UNCOVERED_PC: decode.c:0
UNCOVERED_FUNC: hits: 0 edges: 0/29 ber_get_stringbvl decode.c:0
UNCOVERED_FUNC: hits: 0 edges: 0/4 sb_stream_setup sockbuf.c:0
```

### Available scripts

Available scripts: `scripts/parse_libfuzzer_coverage.sh`:

### Usage Examples

```bash
# Direct pipeline
/out/fuzz_ber_decode -max_total_time=10 -print_coverage=1 /tmp/corpus 2>&1 | ./parse_libfuzzer_coverage.sh

# Save output first
/out/fuzz_ber_decode -max_total_time=10 -print_coverage=1 /tmp/corpus 2>&1 > coverage.txt
./parse_libfuzzer_coverage.sh coverage.txt

# Quick one-liner for coverage percentage
/out/fuzz_target -max_total_time=5 -print_coverage=1 corpus 2>&1 | \
  grep -c "^COVERED_FUNC" | xargs -I{} echo "Covered functions: {}"
```

## Evaluation Workflow

### Step 1: Static Analysis

```bash
# Check API usage patterns
grep -n "init\|setup\|create" harness.cpp
grep -n "free\|cleanup\|destroy\|close" harness.cpp

# Verify paired init/cleanup
```

### Step 2: Reachability Check (for internal functions)

```bash
# If targeting internal function, verify call chain exists
cflow --main=public_entry src/*.c | grep -B10 internal_target
```

### Step 3: Runtime Validation

```bash
# Run with sanitizers
ASAN_OPTIONS=detect_leaks=1 ./fuzzer -max_total_time=30 corpus/

# Check for determinism
for i in 1 2 3; do ./fuzzer corpus/seed1 2>&1 | md5sum; done
```

### Step 4: Coverage Analysis

```bash
# Get initial coverage
./fuzzer -max_total_time=10 -print_coverage=1 corpus 2>&1 | ./parse_libfuzzer_coverage.sh
```

### Step 5: Check Fuzzing Obstacles

Codebases often contain constraint or predicates that prevent effective coverage, please check if the harness itself meaningful but just need better seeds.

## Decision Matrix

| Criterion | Pass | Fail | Action |
|-----------|------|------|--------|
| API lifecycle correct | init→use→cleanup | Missing steps | Fix harness |
| Internal func reachable | Call chain to public API | No path found | Reconsider target |
| Not trivial wrapper | Significant logic | Pure delegation | Exclude target |
| No memory leaks | ASAN clean | Leaks detected | Add cleanup |
| Deterministic | Consistent results | Varies per run | Remove randomness |
| Coverage >20% funcs | Meaningful reach | Shallow coverage | Improve seeds/harness |

## Related Techniques

- `libfuzzer` - LibFuzzer fundamentals and configuration
- `harness-writing` - Techniques for writing effective fuzzing harnesses
- `coverage-analysis` - Detailed coverage measurement with llvm-cov
- `fuzzing-dictionary` - Creating dictionaries to improve coverage
- `fuzzing-obstacles` - Overcoming checksums and other fuzzing barriers
- `ossfuzz` - Continuous fuzzing infrastructure setup

## References

- [LibFuzzer Documentation](https://llvm.org/docs/LibFuzzer.html)
- [Fuzzing Best Practices](https://github.com/google/fuzzing/blob/master/docs/good-fuzz-target.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/zhangutah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
