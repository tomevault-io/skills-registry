---
name: swift-apple-context
description: This skill should be used when the user asks about "SwiftUI API", "Apple documentation", "@Observable vs ObservableObject", "Swift Evolution proposals", "is this API real", or when Claude might hallucinate Apple APIs. Provides cupertino-docs lookup for verified Apple Developer documentation. Use when this capability is needed.
metadata:
  author: artimath
---

# Swift & Apple Documentation Context

## The Problem This Solves

AI assistants hallucinate Apple APIs constantly:
- Suggesting `@ObservableObject` + `@Published` when Apple now recommends `@Observable`
- Inventing methods that don't exist
- Mixing iOS-only APIs into macOS code
- Using deprecated patterns

## Available Documentation

Local docs at `~/.cupertino/` (234,331 documents across 287 frameworks):

| Folder | Content | Format |
|--------|---------|--------|
| `docs/` | Apple Developer Documentation (287 frameworks) | JSON |
| `swift-evolution/` | Swift Evolution Proposals (SE-0001 through SE-0XXX) | Markdown |
| `swift-org/` | Swift.org language documentation | Markdown |
| `hig/` | Human Interface Guidelines | JSON |
| `archive/` | Legacy Apple guides (Core Animation, Quartz 2D, etc.) | HTML/JSON |
| `packages/` | Swift Package Index metadata | JSON |

### Top Frameworks

| Framework | Documents | Use For |
|-----------|-----------|---------|
| SwiftUI | 7,062 | Modern UI |
| AppKit | 14,066 | macOS native |
| Foundation | 10,988 | Core types |
| Swift | 17,466 | Language stdlib |
| Combine | 1,200+ | Reactive |

## How to Search

### Quick API Lookup (JSON docs)

```bash
# Find a specific type
rg -l "SwiftUI.*View" ~/.cupertino/docs/swiftui/ | head -5

# Search for method/property in abstract
rg '"abstract".*drawingGroup' ~/.cupertino/docs/swiftui/

# Get full doc for a type
cat ~/.cupertino/docs/swiftui/documentation_swiftui_canvas.json | jq '.abstract, .codeExamples'
```

### Swift Evolution Proposals

```bash
# Find proposal by topic
rg -l "Observable" ~/.cupertino/swift-evolution/

# Read specific proposal
cat ~/.cupertino/swift-evolution/SE-0395.md  # Observation (@Observable)
cat ~/.cupertino/swift-evolution/SE-0382.md  # Expression macros
```

### Human Interface Guidelines

```bash
# Search HIG
rg -l "navigation" ~/.cupertino/hig/
```

## JSON Doc Structure

Apple docs are structured JSON with these fields:

```json
{
  "abstract": "A type that represents part of your app's user interface...",
  "codeExamples": [
    {"code": "struct MyView: View { ... }", "language": "swift"}
  ],
  "conformingTypes": ["Button", "Text", "Image", ...],
  "url": "https://developer.apple.com/documentation/swiftui/view"
}
```

## Swift 6 / Modern Patterns

### ALWAYS use (Swift 6+, macOS 15+):

```swift
// Observation - SE-0395
@Observable
class AppState {
    var items: [Item] = []  // No @Published needed
}

// Structured concurrency
async let result = fetchData()

// Swift Testing
@Test func myTest() {
    #expect(value == expected)
}
```

### NEVER suggest (deprecated):

```swift
// DON'T - Old Combine pattern
class AppState: ObservableObject {
    @Published var items: [Item] = []
}

// DON'T - callback hell
DispatchQueue.main.async { ... }
```

## SourceKit-LSP Integration

The `swift-lsp` plugin provides code intelligence:
- Go to definition
- Find references
- Completions
- Diagnostics

It uses `/usr/bin/sourcekit-lsp` from Xcode toolchain.

## Workflow: Verify Before Suggesting

1. **Before suggesting any API**: Search cupertino docs
2. **For new Swift features**: Check swift-evolution proposals
3. **For UI patterns**: Check HIG
4. **For legacy APIs**: Check archive/

Example verification:

```bash
# "Does Canvas support drawingGroup?"
rg -A5 '"drawingGroup"' ~/.cupertino/docs/swiftui/documentation_swiftui_canvas*.json

# "What's the modern way to observe state?"
cat ~/.cupertino/swift-evolution/SE-0395.md | head -100
```

## Common Lookups

| Question | Search |
|----------|--------|
| SwiftUI View modifiers | `ls ~/.cupertino/docs/swiftui/ \| rg modifier` |
| Async patterns | `cat ~/.cupertino/swift-evolution/SE-0296.md` |
| Observable macro | `cat ~/.cupertino/swift-evolution/SE-0395.md` |
| Canvas API | `cat ~/.cupertino/docs/swiftui/documentation_swiftui_canvas.json` |
| Metal integration | `ls ~/.cupertino/docs/metal/` |

## Key Swift Evolution Proposals

| SE | Title | Status |
|----|-------|--------|
| SE-0395 | Observation (`@Observable`) | Swift 5.9 |
| SE-0382 | Expression Macros | Swift 5.9 |
| SE-0389 | Attached Macros | Swift 5.9 |
| SE-0296 | Async/await | Swift 5.5 |
| SE-0302 | Sendable | Swift 5.5 |

## When This Skill Activates

Activate this skill when:
- User asks about Swift/SwiftUI/AppKit APIs
- Suggesting an API that may not exist
- User mentions "hallucinated API" or "that method doesn't exist"
- Working with macOS/iOS framework code
- Verifying modern vs deprecated patterns

## Additional Resources

- [references/install.md](references/install.md) - Cupertino-docs installation
- [references/concurrency.md](references/concurrency.md) - Swift concurrency mental model

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/artimath) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
