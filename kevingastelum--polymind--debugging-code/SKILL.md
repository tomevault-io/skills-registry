---
name: debugging-code
description: Systematic approach to identifying and resolving software defects. Use when the user asks to "debug", "fix error", "troubleshoot", or when a command fails unexpectedly. Use when this capability is needed.
metadata:
  author: kevingastelum
---

# Debugging Code

## When to use this skill
- User asks to "fix this error" or "debug this issue".
- A build process or test suite is failing.
- Runtime errors occur during execution (panics, exceptions).
- Unexpected behavior or logical flaws in the application.

## Workflow
- [ ] **Reproduce**: Confirm the issue exists and identify the exact steps to trigger it.
- [ ] **Read**: Analyze the full stack trace or error log. Don't just guess.
- [ ] **Isolate**: Narrow down the search space (specific file, function, or loop).
- [ ] **Hypothesize & Test**: Formulate a theory for the cause and verify it (e.g., adding logs).
- [ ] **Fix**: Implement the correction.
- [ ] **Verify**: Run the reproduction steps again to ensure the issue is resolved.

## Instructions

### 1. The Golden Rule of Debugging
**Read the error message TWICE.**
Most questions are answered by the error message itself. Look for:
- **File paths and line numbers**: Jump directly to the source.
- **Error Types**: `TypeError` (JS), `panic` (Go), `ImportError` (Python).
- **Context**: What was running? (Build, Runtime, Test).

### 2. Language-Specific Strategies

#### Go (Backend/Workers)
- **Panics**: Look for `panic: runtime error`. Use `recover()` in goroutines if the app crashes silently.
- **Type Issues**: Go is statically typed. `cannot use X (type string) as type int` usually means a struct field mismatch.
- **Concurrency**: Use `go run -race` to detect race conditions if data is behaving erratically.

#### TypeScript/Next.js (Frontend)
- **Console Logs**: Check both **Server Terminal** (Node.js) and **Browser Console** (Client).
- **Hydration Errors**: "Text content does not match server-rendered HTML". Cause: Random values (dates, math) generated during render. Fix: Use `useEffect` or fixed seeds.
- **Undefined is not a function**: Usually a missing import or a named export misuse.

#### Python (Data/Model)
- **PDB**: Use `import pdb; pdb.set_trace()` to step through code.
- **Shape Errors**: in Pandas/Numpy (`ValueError: operands could not be broadcast together`). Check `.shape` of all tensors/dataframes.

### 3. "Rubber Ducking" Protocol
If stuck for > 5 minutes:
1. Explain the code line-by-line to the user (or yourself).
2. "Here, I expect variable X to be 5 because..."
3. "But actually, the logs show it is 0."
4. "Therefore, the assignment must be failing upstream."

### 4. Low-Tech Debugging
- **Printf Debugging**: High-tech debuggers are great, but `fmt.Println("HERE 1")` never fails.
- **Binary Search**: Comment out half the code. Does it still crash? Repeat.

## Resources
- [Go Debugging](https://go.dev/doc/diagnostics)
- [Next.js Debugging](https://nextjs.org/docs/pages/building-your-application/configuring/debugging)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kevingastelum) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
