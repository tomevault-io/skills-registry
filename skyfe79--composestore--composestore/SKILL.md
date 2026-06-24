---
name: composestore
description: ComposeStore Swift state management library guide. Use when writing Store subclasses, defining State structs, using @Intercept/@BaseInterceptors macros, implementing interceptors (StoreInterceptor), handling async state with Async<T>, integrating with SwiftUI/UIKit/AppKit, or when the user mentions ComposeStore, Store, commit, dispatch, or interceptor in a Swift context. Use when this capability is needed.
metadata:
  author: skyfe79
---

# ComposeStore

Lightweight Swift state management built on `@Observable`. Provides `Store<S>` base class with `commit`/`dispatch` mutations, NestJS-style interceptor macros, and `Async<T>` for loading states.

## Quick Start

```swift
import ComposeStore

// 1. Define State (struct + Equatable recommended)
struct Counter: State, Equatable {
    var count: Int = 0
}

// 2. Create Store
@MainActor @Observable
final class CounterStore: Store<Counter> {
    init() { super.init(state: Counter()) }

    func increment() {
        commit { state in state.count += 1 }
    }

    func loadData() async {
        commit { state in state.count = 0 }  // reset
        let value = await fetchFromAPI()
        commit { state in state.count = value }
    }
}

// 3. Use in SwiftUI
struct ContentView: View {
    @SwiftUI.State private var store = CounterStore()
    var body: some View {
        Text("\(store.state.count)")
        Button("Increment") { store.increment() }
    }
}
```

## Core API

### State Protocol

```swift
public protocol State: Sendable {}
```

Implement as `struct` conforming to `Equatable` for efficient change detection. Without `Equatable`, `computed(new:old:)` fires on every mutation.

### Store<S: State>

`@MainActor @Observable open class Store<S: State>`

**Mutation methods:**

| Method | Usage |
|--------|-------|
| `commit { state in ... }` | Inline state mutation |
| `commit(mutation:payload:)` | Named mutation with payload |
| `dispatch(action:payload:) async` | Async action |

**Lifecycle:**

| Method | Purpose |
|--------|---------|
| `computed(new:old:)` | Override for derived values after mutation |
| `interceptors` | Override or use `@BaseInterceptors` for base interceptor chain |

### Async<T>

Unified async state enum: `.idle`, `.loading`, `.value(value: T)`, `.error(value: Error?)`

```swift
struct MyState: State, Equatable {
    var users: Async<[User]> = .idle
}
// Usage: commit { s in s.users = .loading }
```

## Interceptor System

NestJS-style interceptor chain wrapping Store method calls. See [references/interceptors.md](references/interceptors.md) for full guide.

### Minimal Example

```swift
// Interceptor with parameterless init (required for macros)
@MainActor
struct LoggingInterceptor: StoreInterceptor {
    func intercept(context: ExecutionContext, next: @escaping () -> any State) -> any State {
        print(">> \(context.methodName)")
        let result = next()
        print("<< \(context.methodName)")
        return result
    }
}

@BaseInterceptors(LoggingInterceptor.self)
@MainActor @Observable
final class MyStore: Store<Counter> {
    init() { super.init(state: Counter()) }

    // Logging -> body
    @Intercept
    func increment() {
        self.commit { state in state.count += 1 }  // self. REQUIRED
    }
}
```

### stopChain() — Skip Remaining Interceptors

`context.stopChain()` skips downstream interceptors but **always executes the body**. Use for caching, error handling, or conditional bypass.

```swift
func intercept(context: ExecutionContext, next: @escaping () -> any State) -> any State {
    if let cached = Self.cache[context.methodName] {
        context.payload[Self.cachedData] = cached
        return context.stopChain()  // skip remaining interceptors, run body
    }
    return next()  // cache miss → continue chain normally
}
```

- `context.isStopped` — readable by parent interceptors after `next()` returns
- Async version: `return await context.stopChain()`
- See [references/interceptors.md](references/interceptors.md) for full guide

### Critical: `self.` Requirement in @Intercept

**BodyMacro path** (methods WITHOUT `context: ExecutionContext`): The `@Intercept` macro wraps the body in an `@escaping` closure. Use explicit `self.` for all member access.

```swift
// CORRECT
@Intercept
func increment() {
    self.commit { state in state.count += 1 }
}

// WRONG - compile error in Xcode
@Intercept
func increment() {
    commit { state in state.count += 1 }  // Missing self.
}
```

**PeerMacro path** (methods WITH `context: ExecutionContext`): Body is NOT wrapped. No `self.` needed.

```swift
// CORRECT - no self. needed
@Intercept(FetchEffect.self)
private func fetchData(context: ExecutionContext) async {
    let data = context.payload[FetchEffect.result] ?? []
    commit { state in state.data = data }  // self. optional
}
```

### When to Use Which Path

| Pattern | When | `self.` needed? |
|---------|------|-----------------|
| `@Intercept` (no context) | Simple sync/async mutations | Yes |
| `@Intercept` + `context: ExecutionContext` | Side-effect separation | No |

## UIKit / AppKit Integration

Use `StoreObserver` for imperative UI frameworks. See [references/uikit-appkit.md](references/uikit-appkit.md).

```swift
observer = StoreObserver { [weak self] in
    guard let self else { return }
    self.label.text = "\(self.store.state.count)"
}
// In deinit: observer?.cancel()
```

## References

- **[Interceptor System](references/interceptors.md)** — Full interceptor guide: @BaseInterceptors, @Intercept, PeerMacro pattern, PayloadKey, side-effect separation
- **[UIKit/AppKit Integration](references/uikit-appkit.md)** — StoreObserver, observeStore helper
- **[Examples](references/examples.md)** — Complete Store examples with SwiftUI, interceptors, async patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/skyfe79) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
