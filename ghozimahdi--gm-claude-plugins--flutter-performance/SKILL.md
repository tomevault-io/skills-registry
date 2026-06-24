---
name: flutter-performance
description: GM Flutter performance patterns — const class vs helper method, isolate vs compute, ListView builder, RepaintBoundary, image caching, build optimization Use when this capability is needed.
metadata:
  author: ghozimahdi
---

## Flutter Performance Patterns (GM Standard)

Rules + decision matrices for performance-critical Flutter UI choices. For full rationale, profiling workflow, and extended examples, read `${CLAUDE_PLUGIN_ROOT}/docs/PERFORMANCE.md`.

> **Note on colors in examples**: GM standard requires every color to come from generated `AppColors` (flutter_gen → `colors.gen.dart`). The snippets below use raw `Color(0x..)` / `Colors.xxx` only to keep the performance contrast self-contained — production code MUST use `AppColors.<name>`. See the Color rule in `${CLAUDE_PLUGIN_ROOT}/CLAUDE.md` and the implementer agent.

---

## 1. Const Class vs Helper Method (CRITICAL)

**Rule:** Prefer `const` widget class over helper method when the widget is reused or rebuilt frequently. Helper methods are called every rebuild and cannot be cached by Flutter; `const` widgets are instantiated once and reused.

### ❌ Helper method (cannot be cached)

```dart
Widget _buildPixelContainer() {
  return Container(
    width: 6, height: 6,
    decoration: BoxDecoration(color: Colors.red.shade400, borderRadius: BorderRadius.circular(1)),
  );
}
```

### ✅ Const widget class (cached by Flutter)

```dart
class PixelBox extends StatelessWidget {
  const PixelBox({super.key});
  @override
  Widget build(BuildContext context) {
    return Container(
      width: 6, height: 6,
      decoration: BoxDecoration(color: Color(0xFFEF5350), borderRadius: BorderRadius.circular(1)),
    );
  }
}

const PixelBox()  // Flutter caches this single instance
```

### Decision Matrix

| Situation                                                | Use              |
| -------------------------------------------------------- | ---------------- |
| Widget reused in multiple places                         | `const` class    |
| Inside `ListView.builder` itemBuilder                    | `const` class    |
| Inside `AnimatedBuilder` / `ValueListenableBuilder`      | `const` class    |
| Widget rebuilt frequently (animations, streams)          | `const` class    |
| Static UI element (icon, divider, spacer)                | `const` class    |
| One-time conditional `if/else` inside a single `build()` | helper method OK |
| Returns non-widget (String, double, etc.)                | helper method OK |

### Mandatory: `const` constructor on every widget

Every `StatelessWidget` and `StatefulWidget` MUST have a `const` constructor — no exceptions, even for StatefulWidgets.

```dart
class Spacer extends StatelessWidget {
  const Spacer({super.key});  // required
}
```

---

## 2. Isolate vs compute() vs async/await

**Rule:** Default to `async/await`. Use `compute()` for one-shot heavy CPU work (>16ms). Use full `Isolate.spawn()` only for long-lived background work.

### Decision Tree

```
Is the work CPU-bound (heavy computation)?
├── No (I/O, network, file read) → async/await
└── Yes
    ├── One-shot heavy computation (>16ms) → compute()
    └── Long-lived / streaming heavy work → Isolate.spawn() + ports
```

### Use `compute()` for

JSON parsing of large payloads (>10K objects), image manipulation, encryption/decryption, sorting/filtering huge lists, regex on huge strings.

```dart
Future<List<Tenant>> parseTenants(String rawJson) async {
  return compute(_parseTenantsSync, rawJson);
}

List<Tenant> _parseTenantsSync(String rawJson) {
  final list = jsonDecode(rawJson) as List;
  return list.map((e) => Tenant.fromJson(e)).toList();
}
```

`compute()` rules:

- The function MUST be top-level or `static`
- Args + return MUST be `SendPort`-compatible (primitives, lists, maps, simple `fromJson`/`toJson` objects)
- NEVER pass `BuildContext`, `Bloc`, or anything holding Flutter framework refs
- Spawn cost ~5–20ms — skip for work under 16ms

### ❌ Common mistakes

```dart
await compute(_fetchFromApi, url);   // I/O already async → pointless
await compute(_doWork, context);     // non-serializable → crash
await compute(_addTwoNumbers, [1,2]);// spawn cost > work → wasteful
```

For CRUD apps, full `Isolate.spawn()` is rarely needed — prefer `compute()`.

---

## 3. ListView / GridView Performance

**Rule:** ALWAYS use `.builder` for lists with >10 items. Add `itemExtent` when height is fixed and stable `ValueKey` for reorderable items.

```dart
// ❌ Renders all items eagerly, even off-screen
ListView(children: tenants.map((t) => TenantCard(tenant: t)).toList())

// ✅ Lazy + fully optimized
ListView.builder(
  itemCount: tenants.length,
  itemExtent: 80.h,            // fixed height → skip layout pass
  cacheExtent: 500,            // pre-render off-screen items
  addAutomaticKeepAlives: false,
  itemBuilder: (context, index) => TenantCard(
    key: ValueKey(tenants[index].id),  // stable id, NOT index
    tenant: tenants[index],
  ),
)
```

- `itemExtent` only when height is truly fixed → drops layout cost
- `ValueKey(id)` for reorderable/insertable/deletable items — never `ValueKey(index)`
- Avoid `shrinkWrap: true` unless nested inside another scrollable — forces full layout
- `addRepaintBoundaries: true` is default — keep it; do NOT wrap items in another `RepaintBoundary`

---

## 4. RepaintBoundary

**Rule:** Wrap widgets that repaint independently from their parent — animated widgets, charts, canvases, video players. Skip for `ListView.builder` items (already wrapped by default).

```dart
RepaintBoundary(child: Lottie.asset('assets/loading.json'))
```

---

## 5. Image Performance

**Rule:** Decode at display size, not source size. A 4000×3000 image at 100×100 still allocates ~48MB by default → 50 items = 2.4GB.

```dart
// ❌ Full-resolution decode
Image.network('https://.../photo.jpg')

// ✅ Decoded at display size
Image.network(
  'https://.../photo.jpg',
  cacheWidth: (100 * MediaQuery.of(context).devicePixelRatio).toInt(),
  cacheHeight: (100 * MediaQuery.of(context).devicePixelRatio).toInt(),
)

// ✅ Or via cached_network_image (preferred for network)
CachedNetworkImage(
  imageUrl: url,
  memCacheWidth: 200,
  memCacheHeight: 200,
)
```

---

## 6. Build Method Discipline

**Rules:**

- NO allocation in `build()` — no list mapping, no parsing, no `DateTime.now()`
- NO `setState` synchronously inside `build()` (causes infinite rebuild)
- Move expensive computations to `initState`, `didChangeDependencies`, or memoize via `late final`
- For Bloc UI, derive computed values inside the bloc state — NEVER inside `build()`

```dart
// ❌ Recomputed every rebuild
@override
Widget build(BuildContext context) {
  final filtered = tenants.where((t) => t.active).toList();
  return ListView(...);
}

// ✅ Computed once in bloc state
state.copyWith(filteredTenants: tenants.where((t) => t.active).toList())
```

---

## 7. BlocSelector — Narrow Rebuilds

**Rule:** When a widget depends only on part of a Bloc state, use `BlocSelector` so it skips rebuilds for unrelated state changes.

```dart
// ❌ Rebuilds on ANY state change
BlocBuilder<TenantBloc, TenantState>(
  builder: (context, state) => Text(state.tenant.name),
)

// ✅ Rebuilds only when name changes
BlocSelector<TenantBloc, TenantState, String>(
  selector: (state) => state.tenant.name,
  builder: (context, name) => Text(name),
)
```

For nested objects, ensure the selector returns a stable reference (rely on Freezed `==`).

---

## 8. Animations

**Rules:**

- Use `AnimatedBuilder` with a `child:` parameter for static subtrees (child is built once)
- Wrap animated widgets in `RepaintBoundary`
- Prefer implicit animations (`AnimatedContainer`, `AnimatedOpacity`) over manual `AnimationController` when possible
- Reuse `Tween` and `Curves` instances — never create them per build

```dart
AnimatedBuilder(
  animation: _controller,
  child: const HeavyWidget(),  // built once
  builder: (context, child) => Transform.rotate(
    angle: _controller.value * 6.28,
    child: child,
  ),
)
```

---

## 9. Stream / Future Patterns

- Avoid `StreamBuilder` / `FutureBuilder` for Bloc-flowing data — let the Bloc own the lifecycle
- Cancel `StreamSubscription` in `close()` of Bloc / `dispose()` of StatefulWidget
- Debounce/buffer high-frequency streams (search input, scroll events) with rxdart

---

## 10. Avoid `saveLayer()` Triggers (off-screen rendering)

**Rule:** Avoid widgets that silently call `Canvas.saveLayer()` — the GPU then renders to an off-screen buffer and copies back. Doubles work per frame; multiplied across list items or animation frames it kills FPS.

### Widgets that trigger `saveLayer()`

| Widget                             | When it triggers                         | Mitigation                                                                                                              |
| ---------------------------------- | ---------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- |
| `ShaderMask`                       | always                                   | static gradient via `Container` decoration                                                                              |
| `ColorFiltered` / `ColorFilter`    | always                                   | bake filter into asset/image                                                                                            |
| `BackdropFilter`                   | always                                   | static blurred image asset if blur is constant                                                                          |
| `Opacity` (with painted child)     | when `opacity != 1.0 && != 0.0`          | `Image(opacity:)` for images, `Color.withOpacity()` on `BoxDecoration` for color, `AnimatedOpacity` only when animated  |
| `Chip` / `RawChip`                 | when `disabledColor` alpha `!= 0xff`     | use full-alpha disabled color or custom widget                                                                          |
| `Text` with overflow shader        | `overflow: TextOverflow.fade`            | use `TextOverflow.ellipsis` or `clip`                                                                                   |
| `ClipPath` / `ClipOval`            | always (with anti-aliasing)              | `BoxDecoration.shape: BoxShape.circle` for circles                                                                      |

### ❌ Triggers saveLayer

```dart
ShaderMask(shaderCallback: (b) => LinearGradient(colors: [...]).createShader(b), child: ...)
RawChip(isEnabled: false, disabledColor: Colors.grey.withAlpha(150), label: ...)
Opacity(opacity: 0.5, child: ComplexCard(...))
```

### ✅ No saveLayer

```dart
Container(decoration: BoxDecoration(gradient: LinearGradient(colors: [...])), child: ...)
RawChip(isEnabled: false, disabledColor: Color(0xFFE0E0E0), label: ...)
Container(color: Colors.black.withOpacity(0.5), child: ComplexCard(...))
```

### How to verify

DevTools → Performance → Timeline Events → filter `saveLayer`. Cross-reference with red "Raster Jank" spikes in the Frames graph.

---

## 11. ClipRRect — prefer `BoxDecoration.borderRadius`

**Rule:** `ClipRRect` forces off-screen rendering. For rounded shapes with color/gradient, use `BoxDecoration.borderRadius` on `Container` — drawn natively in a single GPU pass.

```dart
// ❌ Clipping triggers off-screen render
ClipRRect(
  borderRadius: BorderRadius.circular(12),
  child: Container(padding: EdgeInsets.all(16), color: Colors.blue, child: Text('Submit')),
)

// ✅ Native single-pass paint
Container(
  padding: const EdgeInsets.all(16),
  decoration: BoxDecoration(color: Colors.blue, borderRadius: BorderRadius.circular(12)),
  child: const Text('Submit'),
)
```

### Decision

| Need                                   | Use                                                                           |
| -------------------------------------- | ----------------------------------------------------------------------------- |
| Rounded button / card with solid color | `Container` + `BoxDecoration`                                                 |
| Rounded gradient                       | `Container` + `BoxDecoration(gradient: ..., borderRadius: ...)`               |
| Rounded image                          | `Container` + `BoxDecoration(image: DecorationImage(...), borderRadius: ...)` |
| Clipping arbitrary child widgets       | `ClipRRect` (last resort — Hero, video, CustomPaint, animated children)       |

---

## 12. String Concatenation — `StringBuffer` for loops

**Rule:** For string building inside a loop, use `StringBuffer` (O(n)). The `+=` operator allocates a new `String` on every iteration → O(n²).

```dart
// ❌ O(n²) — 1000 users ≈ 1000 throwaway strings, ~500K chars of garbage
String result = '';
for (final user in users) {
  result += 'Mr ${user.firstName} ${user.lastName}, ';
}

// ✅ O(n)
final buffer = StringBuffer();
for (final user in users) {
  buffer.write('Mr ${user.firstName} ${user.lastName}, ');
}
final result = buffer.toString();

// ✅ Idiomatic for collection + separator
final result = users.map((u) => 'Mr ${u.firstName} ${u.lastName}').join(', ');
```

`+=` is fine for 2–5 fixed parts outside a loop (`final fullName = '${u.firstName} ${u.lastName}';`).

| Situation                                   | Use                  |
| ------------------------------------------- | -------------------- |
| Accumulating in a `for` / `while` loop      | `StringBuffer`       |
| Joining a collection with separator         | `.map().join()`      |
| 2–5 fixed parts                             | `+` or interpolation |
| Building structured output (CSV, log lines) | `StringBuffer`       |

---

## 13. Performance Quick Checklist (Reviewer)

- [ ] Every `StatelessWidget` / `StatefulWidget` has `const` constructor
- [ ] No helper methods returning widgets (use `const` widget class instead)
- [ ] `ListView` / `GridView` uses `.builder` for dynamic lists
- [ ] `itemExtent` set when item height is fixed
- [ ] No allocation / heavy work in `build()`
- [ ] `compute()` used for heavy CPU work, NOT for async I/O
- [ ] `BlocSelector` used when widget depends on partial state
- [ ] `RepaintBoundary` around independent-paint widgets
- [ ] Images use `cacheWidth`/`cacheHeight` or `CachedNetworkImage` with mem cache size
- [ ] No `shrinkWrap: true` outside nested scrollables
- [ ] Stable `ValueKey` (from id) for reorderable list items
- [ ] No `ShaderMask` / `ColorFiltered` / `BackdropFilter` inside list items or animated subtrees
- [ ] No `Chip` / `RawChip` with `disabledColor` alpha `!= 0xff`
- [ ] No `Opacity` wrapping complex children — use `Color.withOpacity()` on decoration, or `AnimatedOpacity` only when needed
- [ ] `ClipRRect` only when truly needed — prefer `Container` + `BoxDecoration.borderRadius`
- [ ] `StringBuffer` (or `.join()`) used for string accumulation in loops — never `+=`
- [ ] No `Text` with `TextOverflow.fade` (triggers saveLayer) — use `ellipsis` / `clip`

---

## Anti-Pattern Reference

| Anti-pattern                                                 | Replace with                                                          |
| ------------------------------------------------------------ | --------------------------------------------------------------------- |
| `Widget _buildHeader() => Container(...)`                    | `class _Header extends StatelessWidget { const _Header(); ... }`      |
| `await compute(_fetchApi, url)`                              | `await dio.get(url)`                                                  |
| `ListView(children: list.map(...).toList())`                 | `ListView.builder(itemCount, itemBuilder)`                            |
| `Image.network(url)` for thumbnails                          | `Image.network(url, cacheWidth: ...)`                                 |
| `BlocBuilder` for one field                                  | `BlocSelector`                                                        |
| `Container()` with no decoration                             | `SizedBox()`                                                          |
| `setState` inside `build()`                                  | move to event handler / Bloc                                          |
| Computing filtered list in `build()`                         | compute in Bloc state                                                 |
| `ClipRRect(borderRadius: ..., child: Container(color: ...))` | `Container(decoration: BoxDecoration(color: ..., borderRadius: ...))` |
| `ShaderMask` for static gradient                             | `Container(decoration: BoxDecoration(gradient: ...))`                 |
| `Opacity(opacity: 0.5, child: ComplexWidget())`              | `Container(color: ...withOpacity(0.5), child: ...)`                   |
| `RawChip(disabledColor: Colors.grey.withAlpha(150))`         | `RawChip(disabledColor: Color(0xFFE0E0E0))` (full alpha)              |
| `result += '...'` inside `for`                               | `StringBuffer().write(...)` then `.toString()`                        |
| Loop building separator-joined string                        | `iterable.map(...).join(', ')`                                        |
| `Text(overflow: TextOverflow.fade)`                          | `TextOverflow.ellipsis` or `clip`                                     |

---

## References

- `${CLAUDE_PLUGIN_ROOT}/docs/PERFORMANCE.md` — full developer-facing reference: rationale, profiling workflow, extended examples

---
> Source: [ghozimahdi/gm-claude-plugins](https://github.com/ghozimahdi/gm-claude-plugins) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
