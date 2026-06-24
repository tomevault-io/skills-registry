---
name: directory-tree-path-resolution
description: Walk up directory tree to resolve resource paths for dev and distribution Use when this capability is needed.
metadata:
  author: csharpfritz
---

## Context
Applications often need to find resource files (configs, data, assets) that exist at project root during development but may be in different locations when distributed. Walking up the directory tree provides a robust fallback that works in both scenarios.

## Patterns

### Directory Walk-Up Resolution
When a relative path might exist in an ancestor directory, walk up from the executable location checking each level. This handles:
- Running from nested bin/Debug/netX.0 folders during development
- Running from project root (e.g., via IDE)
- Packaged distributions where resources are bundled with the executable

### Implementation Strategy
1. Start from `AppContext.BaseDirectory` (or equivalent)
2. Walk up parent directories checking for the relative path
3. Return first match found
4. Fall back to original resolution strategy if not found

## Examples

```csharp
// C# .NET implementation
static string? ResolveWorldPath(string relativePath)
{
    // Walk up from exe directory to find worlds folder (for dev runs)
    var current = new DirectoryInfo(AppContext.BaseDirectory);
    while (current != null)
    {
        var candidate = Path.Combine(current.FullName, relativePath);
        if (File.Exists(candidate))
            return candidate;
        current = current.Parent;
    }
    return null;
}

// Usage
var worldPath = "worlds/game/world.json";
if (!Path.IsPathRooted(worldPath))
{
    var resolved = ResolveWorldPath(worldPath);
    worldPath = resolved ?? Path.Combine(AppContext.BaseDirectory, worldPath);
}
```

```javascript
// Node.js implementation
function resolveResourcePath(relativePath) {
    let current = __dirname;
    while (current) {
        const candidate = path.join(current, relativePath);
        if (fs.existsSync(candidate)) {
            return candidate;
        }
        const parent = path.dirname(current);
        if (parent === current) break; // Reached root
        current = parent;
    }
    return null;
}
```

```python
# Python implementation
def resolve_resource_path(relative_path):
    current = Path(__file__).parent
    while current != current.parent:
        candidate = current / relative_path
        if candidate.exists():
            return candidate
        current = current.parent
    return None
```

## Benefits
- **Development-friendly:** Works from any subdirectory during development
- **Distribution-ready:** Falls back to bundled resources
- **No configuration required:** Automatic path discovery
- **Cross-platform:** Uses platform-appropriate path APIs

## Anti-Patterns
- **Don't hardcode depth limits** — Walk until parent is null/root
- **Don't check directories if expecting files** — Use appropriate existence checks (File.Exists vs Directory.Exists)
- **Don't ignore fallback** — Always have a fallback strategy if walk-up fails

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/csharpfritz) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
