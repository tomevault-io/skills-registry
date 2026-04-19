---
name: zig-build-system
description: This skill should be used when the user asks questions like "how does build.zig work", "how to add dependencies in Zig", "explain Zig build system", "how to cross-compile in Zig", "what are Zig build modes", "how to create build steps", or generally asks about Zig's build system and package management. Use when this capability is needed.
metadata:
  author: surt666
---

# Zig Build System

## Overview

This skill explains Zig's built-in build system for experienced programmers. Unlike Make, CMake, or other external build tools, Zig's build system is written in Zig itself (`build.zig`). The build system provides cross-compilation, dependency management, and flexible build configuration out of the box.

Target Zig version: **0.15.2**

## The build.zig File

### Basic Structure

Every Zig project has a `build.zig` file at the root:

```zig
const std = @import("std");

pub fn build(b: *std.Build) void {
    // Build configuration goes here
}
```

The `build()` function receives a `*std.Build` pointer providing the build API.

### Simple Executable

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    b.installArtifact(exe);
}
```

**Build the executable**:
```bash
zig build
# Output: zig-out/bin/my-app
```

**Key components**:
- `standardTargetOptions()`: Cross-compilation target (from CLI: `zig build -Dtarget=...`)
- `standardOptimizeOption()`: Build mode (from CLI: `zig build -Doptimize=...`)
- `addExecutable()`: Creates executable artifact
- `installArtifact()`: Copies to output directory (`zig-out/`)

### Adding a Library

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // Static library
    const lib = b.addStaticLibrary(.{
        .name = "mylib",
        .root_source_file = b.path("src/lib.zig"),
        .target = target,
        .optimize = optimize,
    });

    b.installArtifact(lib);

    // Executable that links the library
    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    exe.linkLibrary(lib);  // Link against our library
    b.installArtifact(exe);
}
```

### Adding Tests

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // Main executable
    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    b.installArtifact(exe);

    // Unit tests
    const unit_tests = b.addTest(.{
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    const run_tests = b.addRunArtifact(unit_tests);

    const test_step = b.step("test", "Run unit tests");
    test_step.dependOn(&run_tests.step);
}
```

**Run tests**:
```bash
zig build test
```

## Build Modes (Optimize Options)

Zig has four build modes controlling optimization and safety:

| Mode | Flag | Optimizations | Safety Checks | Use Case |
|------|------|---------------|---------------|----------|
| **Debug** | `-Doptimize=Debug` | None | All enabled | Development, debugging |
| **ReleaseSafe** | `-Doptimize=ReleaseSafe` | Yes | All enabled | Production with safety |
| **ReleaseFast** | `-Doptimize=ReleaseFast` | Maximum | Disabled | Production, performance |
| **ReleaseSmall** | `-Doptimize=ReleaseSmall` | Size | Disabled | Embedded, size-critical |

**Examples**:
```bash
zig build                              # Debug (default)
zig build -Doptimize=ReleaseSafe       # Safe release
zig build -Doptimize=ReleaseFast       # Fast release
zig build -Doptimize=ReleaseSmall      # Small release
```

**Choosing a mode**:
- **Debug**: Development (safety checks, no optimization)
- **ReleaseSafe**: Production default (optimized but safe)
- **ReleaseFast**: Performance-critical (no safety checks)
- **ReleaseSmall**: Embedded/minimal binaries

## Cross-Compilation

Zig excels at cross-compilation—build for any target from any host.

### Target Triple

Targets specified as: `<arch>-<os>-<abi>`

**Examples**:
```bash
# Linux ARM64
zig build -Dtarget=aarch64-linux

# Windows x86_64
zig build -Dtarget=x86_64-windows

# macOS ARM64 (M1/M2)
zig build -Dtarget=aarch64-macos

# WebAssembly
zig build -Dtarget=wasm32-freestanding

# Native (host system)
zig build -Dtarget=native
```

### Cross-Compile Configuration

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});

    // Query target properties
    if (target.result.cpu.arch == .wasm32) {
        // WebAssembly-specific configuration
    }

    if (target.result.os.tag == .windows) {
        // Windows-specific configuration
    }

    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = b.standardOptimizeOption(.{}),
    });

    b.installArtifact(exe);
}
```

### Multiple Targets

```zig
pub fn build(b: *std.Build) void {
    const targets = [_]std.Target.Query{
        .{ .cpu_arch = .x86_64, .os_tag = .linux },
        .{ .cpu_arch = .x86_64, .os_tag = .windows },
        .{ .cpu_arch = .aarch64, .os_tag = .macos },
    };

    for (targets) |target_query| {
        const target = b.resolveTargetQuery(target_query);

        const exe = b.addExecutable(.{
            .name = "my-app",
            .root_source_file = b.path("src/main.zig"),
            .target = target,
            .optimize = .ReleaseSafe,
        });

        b.installArtifact(exe);
    }
}
```

## Dependencies and Packages

### Package Manager (.zon files)

Zig 0.15.2 uses `.zon` (Zig Object Notation) files for dependency declaration.

**build.zig.zon**:
```zig
.{
    .name = "my-app",
    .version = "0.1.0",

    .dependencies = .{
        .@"some-lib" = .{
            .url = "https://github.com/user/some-lib/archive/v1.0.0.tar.gz",
            .hash = "1220abcdef...",  // Content hash
        },
    },

    .paths = .{
        "build.zig",
        "build.zig.zon",
        "src",
    },
}
```

### Using Dependencies

**build.zig**:
```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // Get dependency
    const some_lib = b.dependency("some-lib", .{
        .target = target,
        .optimize = optimize,
    });

    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    // Add module from dependency
    exe.root_module.addImport("some-lib", some_lib.module("some-lib"));

    b.installArtifact(exe);
}
```

**Using in code** (`src/main.zig`):
```zig
const some_lib = @import("some-lib");

pub fn main() !void {
    some_lib.doSomething();
}
```

### Fetching Dependencies

```bash
# Fetch all dependencies
zig build

# Or explicitly
zig fetch --save https://github.com/user/some-lib/archive/v1.0.0.tar.gz
```

Zig automatically downloads and caches dependencies.

## Build Steps and Custom Logic

### Creating Custom Steps

```zig
pub fn build(b: *std.Build) void {
    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = b.standardTargetOptions(.{}),
        .optimize = b.standardOptimizeOption(.{}),
    });

    b.installArtifact(exe);

    // Run step
    const run_cmd = b.addRunArtifact(exe);
    run_cmd.step.dependOn(b.getInstallStep());

    if (b.args) |args| {
        run_cmd.addArgs(args);
    }

    const run_step = b.step("run", "Run the application");
    run_step.dependOn(&run_cmd.step);
}
```

**Usage**:
```bash
zig build run
zig build run -- arg1 arg2  # Pass arguments
```

### Custom Build Commands

```zig
pub fn build(b: *std.Build) void {
    // ... executable setup ...

    // Custom step: Generate code
    const gen_step = b.step("generate", "Generate code");

    const gen_cmd = b.addSystemCommand(&[_][]const u8{
        "python3",
        "scripts/generate.py",
    });

    gen_step.dependOn(&gen_cmd.step);
}
```

**Usage**:
```bash
zig build generate
```

### Build Options

Pass compile-time configuration:

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    // Create build options
    const options = b.addOptions();
    options.addOption(bool, "enable_logging", b.option(
        bool,
        "enable_logging",
        "Enable logging (default: false)",
    ) orelse false);

    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    exe.root_module.addOptions("config", options);

    b.installArtifact(exe);
}
```

**Using in code**:
```zig
const config = @import("config");

pub fn main() !void {
    if (config.enable_logging) {
        std.debug.print("Logging enabled\n", .{});
    }
}
```

**Build with option**:
```bash
zig build -Denable_logging=true
```

## Common Patterns

### Conditional Compilation

```zig
pub fn build(b: *std.Build) void {
    const target = b.standardTargetOptions(.{});
    const optimize = b.standardOptimizeOption(.{});

    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = target,
        .optimize = optimize,
    });

    // Platform-specific linking
    if (target.result.os.tag == .windows) {
        exe.linkSystemLibrary("ws2_32");  // Windows sockets
    } else {
        exe.linkSystemLibrary("c");
    }

    b.installArtifact(exe);
}
```

### Installing Files

```zig
pub fn build(b: *std.Build) void {
    // Install executable
    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = b.standardTargetOptions(.{}),
        .optimize = b.standardOptimizeOption(.{}),
    });

    b.installArtifact(exe);

    // Install additional files
    b.installFile("config.json", "config.json");
    b.installDirectory(.{
        .source_dir = b.path("assets"),
        .install_dir = .prefix,
        .install_subdir = "assets",
    });
}
```

### Linking C Libraries

```zig
pub fn build(b: *std.Build) void {
    const exe = b.addExecutable(.{
        .name = "my-app",
        .root_source_file = b.path("src/main.zig"),
        .target = b.standardTargetOptions(.{}),
        .optimize = b.standardOptimizeOption(.{}),
    });

    // Link system C library
    exe.linkSystemLibrary("curl");

    // Link libc
    exe.linkLibC();

    b.installArtifact(exe);
}
```

## Build Output

Default output structure:

```
zig-out/
├── bin/           # Executables
├── lib/           # Libraries
└── include/       # Headers (for C libraries)
```

**Custom output directory**:
```bash
zig build --prefix /custom/path
```

## Common Commands

```bash
# Build project (Debug mode)
zig build

# Build with optimization
zig build -Doptimize=ReleaseFast

# Cross-compile
zig build -Dtarget=x86_64-windows

# Run tests
zig build test

# Run application
zig build run

# Clean build artifacts
rm -rf zig-out zig-cache

# List available build steps
zig build --help
```

## Best Practices

1. **Use `standardTargetOptions()` and `standardOptimizeOption()`**: Enables CLI configuration
2. **Create `run` and `test` steps**: Standard across Zig projects
3. **Document build options**: Use descriptive help text in `b.option()`
4. **Organize source files**: Keep `src/` for source, `build.zig` at root
5. **Pin dependency versions**: Specific commit hashes or tags in `.zon`
6. **Use `b.path()` for file paths**: Portable path handling
7. **Leverage cross-compilation**: Test on multiple targets
8. **Prefer ReleaseSafe for production**: Safety with optimization

## Additional Resources

### Reference Files

For detailed examples and advanced patterns:
- **`references/build-examples.md`** - Complete build.zig examples for common scenarios
- **`references/dependency-management.md`** - Advanced dependency patterns, vendoring, local packages

### Official Documentation

- [Zig Build System](https://ziglang.org/learn/build-system/)
- [Standard Build API](https://ziglang.org/documentation/0.15.2/std/#std.Build)

## Quick Reference

| Task | Command |
|------|---------|
| Build project | `zig build` |
| Build optimized | `zig build -Doptimize=ReleaseFast` |
| Cross-compile | `zig build -Dtarget=x86_64-windows` |
| Run tests | `zig build test` |
| Run app | `zig build run` |
| Add dependency | Edit `build.zig.zon`, run `zig build` |
| Custom step | `zig build <step-name>` |
| List steps | `zig build --help` |

## Summary

Zig's build system is:
- **Self-contained**: Written in Zig, no external tools
- **Cross-platform**: Build for any target from any host
- **Flexible**: Custom build steps and configuration
- **Declarative**: Clear dependency graph and artifact configuration
- **Fast**: Incremental builds and caching

Master `build.zig` to create portable, cross-platform Zig projects with minimal configuration.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/surt666) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
