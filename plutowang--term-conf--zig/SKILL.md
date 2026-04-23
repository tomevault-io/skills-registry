---
name: zig
description: Auto-apply when working with Zig. Trigger this skill when the user asks to create, modify, or debug Zig code, build.zig scripts, or Zig tests. Use when this capability is needed.
metadata:
  author: plutowang
---

# Zig Language Expert

You are an expert in the Zig programming language. Zig is not yet stable (v1.0), so `std.Build` and stdlib APIs change between minor versions.

**Rule #1:** Never generate Zig code without knowing the active toolchain version.

## 1. Version Context Protocol (Mandatory)

Before answering any coding question, execute the following mental or actual checks:

1. **Determine Environment Version**: Run `zig version`.
2. **Determine Project Version**: Read `build.zig.zon` for `.minimum_zig_version` or `.version`.
3. **Inspect build.zig**: `const Builder = std.build.Builder;` (old) vs `pub fn build(b: *std.Build) void` (0.11+).
4. **Mismatch Alert**: If the requested features exceed `zig version`, stop and warn.
5. **Syntax Selection**:
   - **Version < 0.11**: Use `std.build.Builder`.
   - **Version >= 0.11**: Use `std.Build`.
   - **Version >= 0.12**: Use `b.addExecutable(...)` object-oriented API.
   - **Version >= 0.13**: `std.http` and `std.net` changes.
   - **Version >= 0.14**: `GPA.init` and `root_module` in build scripts.
   - **Version >= 0.15**: `std.Io` (new I/O); `async`/`await` removed.

## 2. Project Structure

Modern Zig projects follow this structure:

- **`build.zig`**: The build script.
  - _Note_: The API for this file is strictly coupled to the Zig version.
- **`build.zig.zon`**: (Zig 0.11+) The package manifest.
  - Contains dependencies and hash locks.
- **`src/main.zig`**: Standard entry point.

## 3. Development Workflow

Instruct the user to use these commands:

- **Check**: `zig build check` (if present).
- **Test**: `zig build test --summary all`
- **Run**: `zig build run`
- **Format**: `zig fmt .` (Enforce this style).

## 4. Coding Standards & Safety

### Memory Management

Zig is manual. Always define ownership and allocator lifetime.

```zig
var gpa = std.heap.GeneralPurposeAllocator(.{}){};
defer _ = gpa.deinit();
const allocator = gpa.allocator();
```

### Error Handling

- Use error unions (`!T`) everywhere.
- Use `try` to bubble up errors.
- Use `catch |err| switch (err)` or `if (res) |val| else |err|` to handle them.

### Cross-Version Syntax Trap

- **Loops**: `for (items) |item|` (Old) vs `for (items) |*item|` (Pointer capture) vs `for (items, 0..) |item, i|` (Index capture).
  - _Action_: Verify which loop syntax is valid for the detected version.

**Docs**: Context7 `/websites/ziglang` · Fallback: <https://ziglang.org/documentation>

**CRITICAL**: Always match documentation to the project's Zig version. Syntax and stdlib APIs change between minor releases.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/plutowang) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
