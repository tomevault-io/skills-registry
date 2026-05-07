---
name: zig
description: > Use when this capability is needed.
metadata:
  author: neversight
---

## Critical Patterns

### Error Handling (REQUIRED)

```zig
// ✅ ALWAYS: Use error unions
fn readFile(path: []const u8) ![]u8 {
    const file = try std.fs.cwd().openFile(path, .{});
    defer file.close();
    return try file.readToEndAlloc(allocator, max_size);
}

// Usage with catch
const content = readFile("config.txt") catch |err| {
    std.log.err("Failed: {}", .{err});
    return err;
};
```

### Memory Management (REQUIRED)

```zig
// ✅ ALWAYS: Use allocators explicitly
const allocator = std.heap.page_allocator;

var list = std.ArrayList(u8).init(allocator);
defer list.deinit();

try list.append(42);
```

### Comptime (REQUIRED)

```zig
// ✅ Use comptime for compile-time computation
fn Vec(comptime T: type, comptime N: usize) type {
    return struct {
        data: [N]T,
        
        pub fn init() @This() {
            return .{ .data = undefined };
        }
    };
}

const Vec3f = Vec(f32, 3);
```

---

## Decision Tree

```
Need error handling?       → Use error unions with try/catch
Need cleanup?              → Use defer
Need compile-time?         → Use comptime
Need optional value?       → Use ?T (optional type)
Need C interop?            → Use @cImport
```

---

## Commands

```bash
zig build                  # Build project
zig run src/main.zig       # Build and run
zig test src/main.zig      # Run tests
zig fmt src/               # Format code
```

---

## Resources

- **Best Practices**: [best-practices.md](best-practices.md)
- **Comptime**: [comptime.md](comptime.md)
- **Memory**: [memory.md](memory.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
