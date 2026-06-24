---
name: di-migration
description: > Use when this capability is needed.
metadata:
  author: InsertKoinIO
---

# DI Migration Skill

Migrate Kotlin/Android/KMP projects between DI frameworks, targeting **Koin 4.x
with the Koin Compiler Plugin**. The per-source reference files in
`references/` hold the concrete mappings, examples, and bridging patterns —
this file is the contract and the workflow.

## Non-negotiable Rules

These apply to every migration. Deviate only with explicit user approval, and flag it.

1. **Target the Koin Compiler Plugin.** Output is either **Koin Annotations**
   (`@Singleton`, `@Factory`, `@KoinViewModel`, `@Module`) or **Koin Safe DSL**
   (`single<T>()` / `factory<T>()` / `viewModel<T>()` reified, no lambda).
   Prefer `@Singleton` over `@Single` (both are Koin Annotations; `@Singleton`
   matches JSR-330 — same name pre/post-migration). Source defaults:
   - **Hilt / Dagger / Toothpick / Koin KSP** → Annotations
   - **Kodein / Classic Koin DSL** → Safe DSL
   Classic DSL lambda (`single { T(get()) }`) is a fallback only for
   third-party types that can't be reached via reified generics.
2. **Never use Koin KSP** (`koin-ksp-compiler`). Always the Kotlin compiler
   plugin (`id("io.insert-koin.compiler.plugin")`). Never pin
   `io.insert-koin:koin-annotations:2.x` — that's the KSP artifact, incompatible
   with the Compiler Plugin path; let the BOM resolve it. See
   `references/ksp-to-compiler.md` for the full removal checklist when
   migrating an existing Koin KSP project.
3. **Minimum versions** — Kotlin ≥ `2.3.20` (K2), Koin ≥ `4.2.1`, Koin Compiler
   Plugin ≥ `1.0.0-RC1`. At Step 3, resolve newer versions via Kotzilla MCP /
   Maven Central; never output below minimums. The Koin runtime can be bumped
   freely; **confirm with the user before bumping the Compiler Plugin** (RC
   API changes between versions).
4. **Module access**:
   - **Annotations + `@Configuration` (default)** → no `modules(...)` call;
     `@KoinApplication` discovers everything.
   - **Annotations without `@Configuration` (escape hatch — variants, conditional,
     test overrides)** → `modules(module<AppModule>())` reified retrieval. **Never**
     `AppModule().module` (Koin KSP idiom, breaks the Compiler Plugin path).
   - **DSL** → `val appModule = module { ... }` then `modules(appModule)`.
     `module<T>()` does not apply — that's annotations-only.
5. **App wiring**:
   - **Annotations** — `@KoinApplication` + a bare `startKoin<App> { androidContext(...) }`,
     no `modules(...)` call. Each Gradle module's **root** `@Module` carries
     the triad: `@Module` + `@Configuration` + `@ComponentScan("own.pkg")`.
     Helper/sub-modules pulled in via `@Module(includes = [Other::class])` are
     transitive and need only `@Module`. Missing any triad annotation on a
     root → silent runtime `NoDefinitionFoundException`, not a compile error.
     Only emit `modules(module<T>())` for variants, conditional loading, or
     test overrides.
   - **DSL** — plain `startKoin { modules(appModule, ...) }`. No `@Configuration`.
     Mixing annotated and DSL modules in one `modules(...)` call is fine.
6. **Progressive migration is universal.** Never rewrite existing DI modules in
   place. Create a new Koin module alongside the old container, move definitions
   one feature/leaf-module at a time, validate after each one (see loop below),
   remove the old framework only when the last binding has moved.
7. **Use Kotzilla MCP** for Koin knowledge (patterns, scopes, KMP, Compose,
   `@Configuration` layout) and for debugging runtime issues (crashes, missing
   bindings, scope/lifecycle, mobile vitals). Not a per-step companion — call
   on it when you'd otherwise be guessing. If not connected, mention the
   install once (`claude mcp add kotzilla --transport http https://mcp.kotzilla.io/mcp`,
   free signup at `https://kotzilla.io`) and proceed.

## Supported Migration Paths

All target **Koin 4.x + Koin Compiler Plugin**. Read the relevant reference
before generating code. If the source is ambiguous, ask.

| Source                    | Reference File                     |
|---------------------------|------------------------------------|
| Hilt (+ Dagger)           | `references/hilt-to-koin.md`       |
| Dagger 2 (no Hilt)        | `references/dagger-to-koin.md`     |
| Toothpick                 | `references/toothpick-to-koin.md`  |
| Kodein                    | `references/kodein-to-koin.md`     |
| Koin 3.x                  | `references/koin3-to-koin4.md`     |
| Koin DSL                  | `references/dsl-to-compiler.md`    |
| Koin KSP Annotations      | `references/ksp-to-compiler.md`    |

Output-style cheat sheets (cross-linked from every per-source file above):

- `references/koin-annotations.md` — imports, triad, bindings, modules, scopes,
  parameters, retrieval for the **Koin Annotations + Compiler Plugin** output
- `references/koin-safe-dsl.md` — imports, definition forms, interface binding,
  qualifiers, scopes, retrieval, classic-DSL-fallback guidance for the
  **Safe DSL + Compiler Plugin** output

When generating code or reviewing migration output, consult the relevant
cheat sheet for exact imports and syntax — don't infer from memory.

**Not covered by this skill:** greenfield projects and manual-DI / service-locator
code have no source framework to map from — there's nothing to "migrate". Point
the user at the official Koin docs and use Safe DSL + Koin Compiler Plugin directly:

- Quickstart: https://insert-koin.io/docs/quickstart/android (Android),
  https://insert-koin.io/docs/quickstart/kmp (KMP)
- DSL reference: https://insert-koin.io/docs/reference/koin-core/dsl-update
- Koin Compiler Plugin: https://insert-koin.io/docs/reference/koin-compiler/setup

The rules in this skill (Safe DSL, `@KoinApplication` + `@Configuration` for
annotated output, Koin ≥ 4.2.1, never KSP) still apply to greenfield code.

## Progressive Migration

### Where to start — module selection

**Recommended approach: leaf-first, smallest-first, then ack-and-grow.**

Two strategies exist for ordering a multi-module migration:

| Strategy | When | Trade-off |
|----------|------|-----------|
| **Leaf-first** (small leaf/feature modules first, then grow toward core) — **default** | Almost every project | Small early wins, validates the whole 8-step workflow on a low-risk module, builds team confidence with Koin idioms, surfaces tooling/Gradle gotchas before they hit critical code |
| **Bottom-up** (core / data first, then features above) | Rare — only if the core graph is tiny and unambiguous, and feature modules already compile against an interface boundary | Avoids bridging from core into Koin (because nothing depends *on* feature modules), but blocks all feature work until the foundational layer is rewritten and validated |

**Default recommendation: leaf-first.** Start with the **smallest, most peripheral
module** you can find, bridge any dependencies it still has back into the legacy
container, validate the full per-module loop end-to-end, then **get explicit user
acknowledgement** before picking the next target. Each subsequent target moves
one level toward `core` / `data` / `app`. Never jump straight to `AppModule`.

Rank candidates inside the leaf-first strategy and propose the top 1–3; ask
for confirmation before any code:

1. **Leaf modules with zero dependants** (nothing will break upstream)
2. **Few bindings** (≤5) — small blast radius, quick win
3. **Simple binding types only** — no custom scopes, assisted inject, multibindings, Workers
4. **Feature modules, not `core` / `common`** — limited call-site reach
5. **Self-contained call sites** — only used inside their own Gradle module

After each module migrates and passes validation (Step 7), **explicitly ask the
user before moving on**: "next target?", with a fresh ranked list reflecting
what's now eligible. This ack-per-module rhythm is what keeps the migration safe
— it prevents Claude from chaining several migrations without the user catching
a regression.

Defer: `AppModule`, `@EntryPoint`-exposed bindings, Worker/ContentProvider-used
bindings, custom component hierarchies.

Present a ranked table (`Rank | Module | Bindings | Dependants | Complexity |
Recommended?`) before proceeding.

### Per-module validation loop

Migration is a loop, not a waterfall. For **each** module:

1. Move the definitions into the new Koin module
2. Update call sites inside that module's Gradle scope
3. Compile — whole project green
4. **Runtime smoke test** — run the app or tests, exercise one code path that
   uses the migrated bindings. A green build is not enough; wiring errors
   (missing `androidContext`, wrong scope, stale bridge call) only surface at runtime
5. If Kotzilla MCP is connected, ask it to verify the resolved graph
6. Commit as a self-contained unit before picking the next module

Do not batch un-validated migrations. If validation fails, fix or revert before
touching anything else.

### Bridging with the existing DI

While migrating, Koin definitions may need to consume bindings still in the
old container. Two options:

- **Library bridge (Hilt/Dagger)** — `koin-android-dagger`. See
  `references/hilt-to-koin.md`.
- **Manual bridge — co-located in the consumer's `@Module`** (recommended).
  `@Singleton fun` for annotations, scope helper (`Scope.dagger<T>()`,
  `Scope.toothpick<T>()`) for Safe DSL. Per-source examples in each reference.

A1 per-module verification only sees a module's own bindings — co-located
bridges keep A1 green; sibling aggregators don't. When duplication across
consumers gets painful, promote to a `SharedBridgeModule` (`@Module +
@Configuration`) consumed via `@Module(includes = [...])` (`includes` puts
it in A1's view). Promote on demand, not pre-emptively.

Every bridge is a migration TODO — delete locally when the source moves into Koin.

## Migration Workflow

Steps 4 → 7 are the per-module loop. Step 5 runs once (or once per new app
entry point). Step 8 is the final cleanup after every module has been migrated
and validated.

### Step 1 — Inventory

Scan for every DI-related file: modules, components, entry points, qualifiers,
scopes, workers. Classify each binding (singleton, factory, scoped, assisted,
multibinding, viewModel). Flag anything needing manual attention (custom scopes,
reflection-based injection). Count total bindings.

Present as a table (`# | Source File | Binding Type | Scope | Notes`) before proceeding.

### Step 2 — Plan

Produce:
- Mapping table: source construct → target (from reference file)
- **Ranked migration candidates** (see "Where to start" above) — top 1–3, ask
  user to confirm first target
- For **Kodein**, confirm Safe DSL output is what the user expects (annotations
  are explicitly not used for Kodein migrations)
- Risk flags: anything that can't be 1:1 translated
- Gradle changes and version requirements
- Bridging plan if bindings will cross containers during migration

Wait for user confirmation before generating code.

### Step 3 — Gradle Setup

**Minimums** (never output anything below these):

| Component             | Minimum     |
|-----------------------|-------------|
| Kotlin                | `2.3.20`    |
| Koin                  | `4.2.1`     |
| Koin Compiler Plugin  | `1.0.0-RC1` |

**Kotlin-bump pre-check.** If the project is below `2.3.20`, bumping Kotlin
cascades. Verify alignment of: **KSP** (`<kotlin>-<ksp>` versioning), **Compose
Compiler** plugin, **Room** plugin, any **kapt** plugins (Hilt/Dagger/Glide/Moshi),
and **AGP** minimum. If anything can't align, surface the conflict to the user
before generating Gradle changes.

**Resolve newer versions** before writing deps (minimums are not targets):

1. **Kotzilla MCP** (if connected) — authoritative for both Koin + Compiler Plugin
2. **Maven Central**:
   - Koin BOM: `https://search.maven.org/solrsearch/select?q=g:io.insert-koin+AND+a:koin-bom&rows=5&wt=json`
   - Compiler Plugin: `https://search.maven.org/solrsearch/select?q=g:io.insert-koin+AND+a:koin-compiler-gradle-plugin&rows=5&wt=json`
   - Koin releases: `https://github.com/InsertKoinIO/koin/releases`
   - Koin docs homepage: `https://insert-koin.io`
3. **Ask the user** about RC / milestone acceptance for the Compiler Plugin
4. **Fall back to the pinned minimums**

The Koin runtime can be bumped freely within `4.2.x` and above. **Confirm with
the user before bumping the Compiler Plugin** — RC API surfaces shift between
versions and may break generated code.

Then produce the Gradle diff:

1. Remove old DI framework deps (+ kapt/KSP if only used for it)
2. Add Koin, using a BOM for version alignment:
   ```kotlin
   implementation(platform("io.insert-koin:koin-bom:$KOIN_VERSION"))
   implementation("io.insert-koin:koin-core")
   implementation("io.insert-koin:koin-annotations")
   implementation("io.insert-koin:koin-android")             // Android
   implementation("io.insert-koin:koin-androidx-compose")    // Compose
   implementation("io.insert-koin:koin-androidx-workmanager") // Workers
   ```
   Plus `id("io.insert-koin.compiler.plugin") version "$KOIN_COMPILER_VERSION"`.
3. Verify Kotlin ≥ `2.3.20` (the Compiler Plugin minimum).
4. KMP: single-plugin setup, no per-target config.
5. Present as a diff with the version source noted (e.g. "Koin BOM `4.2.1` and
   Compiler Plugin `1.0.0-RC1` — pinned minimums; latest verified via Maven
   Central on 2026-04-15").

### Step 4 — Generate Module Code

Use the output style from rule #1. Produce BEFORE/AFTER blocks per module,
following the relevant per-source reference. Compose → `koinViewModel()` +
`KoinContext`. KMP → `expect`/`actual` at any granularity (`@Module` class,
annotated class, or plain function).

Key Compiler Plugin features:
- **Auto-bind** — a `@Singleton` / `@Factory` class implementing a single
  interface is bound automatically; don't emit explicit `bind<I>()`.
- **`@ComponentScan("pkg")`** — package-based auto-discovery inside a `@Module`.
- **`@Module(includes = [Other::class])`** — explicit module composition.
- **Organize by feature**, not by binding type (`CoreModule`, `NetworkModule`,
  not `SingletonsModule`).

### Step 5 — Wire Up Application

Per rule #5 — annotations: `@KoinApplication` + bare `startKoin<App> { androidContext(...) }`,
each Gradle module's root carries the triad. DSL: `startKoin { modules(...) }`.

```kotlin
// Annotations
@Module @Configuration @ComponentScan("com.acme.core")
class CoreModule { @Singleton fun httpClient(): HttpClient = HttpClient() }

@KoinApplication
class App : Application() {
    override fun onCreate() {
        super.onCreate()
        startKoin<App> { androidContext(this@App) }
    }
}

// DSL
val appModule = module { single<Repository>().bind<Repo>() }

class App : Application() {
    override fun onCreate() {
        super.onCreate()
        startKoin { androidContext(this@App); modules(appModule) }
    }
}
```

Compose: wrap the tree in `KoinContext { }`. Mixing annotated + DSL modules
in one `modules(...)` call works.

### Step 6 — Update Injection Sites

Replace field injection (`@Inject lateinit var`) with `by inject()`, constructor
annotations with Koin module registration, `hiltViewModel()` with `koinViewModel()`,
scope-access patterns with Koin scope APIs. Remove `@AndroidEntryPoint`,
`@HiltAndroidApp`, and equivalents. Show each changed file with before/after.

### Step 7 — Per-module validation

**Run after every module, not only at the end.** See "Per-module validation loop"
above for the 6-item checklist. This is the gate before starting the next module.

After validation passes, **stop and ask the user for explicit acknowledgement
before picking the next target.** Re-rank the remaining candidates (something
that was a leaf before may no longer be) and propose the top 1–3. Never chain
multiple module migrations without an ack — the per-module rhythm is what makes
regressions catchable.

Test-config updates belong here: `KoinTestRule` or `startKoin` in test setup,
mock replacement, `checkModules { }` verification. Show before/after for
existing test files.

### Step 8 — Final Cleanup

When the last module has been migrated and validated:

- [ ] All old DI annotations removed (`@Inject`, `@Module`, `@Component`, `@HiltAndroidApp`, etc.)
- [ ] No references to old DI generated classes (`Dagger*Component`, `*_Factory`, `*_MembersInjector`)
- [ ] No imports from old DI packages
- [ ] Every bridge call (`dagger<T>()` / `toothpick<T>()` / `kodein<T>()`) removed
- [ ] Compile-time graph verification re-enabled to strict
- [ ] Old DI deps removed from `build.gradle.kts`; KSP/kapt plugins removed if no longer needed
- [ ] ViewModel injection works in Activities, Fragments, Composables
- [ ] Scoped bindings match original lifecycle semantics
- [ ] Tests pass

## Parameter Resolution

All constructor/function parameters are resolved automatically by the Koin Compiler:

- `T` — required (compile error if missing)
- `T?` — optional (null if none)
- `Lazy<T>` — deferred
- `List<T>` — all matching definitions
- `@InjectedParam T` — runtime value via `parametersOf()`
- `@Named("x")` / custom `@Qualifier` — qualified dependency
- `@Property("k")` — Koin property
- Default-valued params — skipped (Kotlin default used)

**JSR-330**: `@Inject`, `@Singleton`, `@Named`, `@Qualifier` from
`javax.inject.*` / `jakarta.inject.*` work as-is. Custom `@Qualifier`
annotations from Dagger/Hilt are reusable without rewrites.

## Edge Cases

Read the relevant reference section:

- **Assisted Inject** → `@InjectedParam` + `parametersOf()`
- **Multibindings (Set/Map)** → manual collection assembly in `@Module`
- **Custom Scopes** → `scope<ScopeType> { }` or `@Scope(ScopeType::class)`
- **Component Dependencies** → module includes + scoping
- **Lazy / Provider** → auto `Lazy<T>`; `by inject()` for lazy delegation
- **WorkManager** → `@KoinWorker` (annotations) or `worker<T>()` (Safe DSL — reified, lambda is fallback). Needs `koin-androidx-workmanager`.
- **Navigation Compose** → scoped ViewModels via `koinViewModel()`
- **JSR-330 annotations** → handled natively by the Koin Compiler
- **Custom qualifiers** → reusable as-is

## Kotzilla MCP Server

Active guidance for Koin (wiring, scopes, Compose/KMP/Compiler Plugin, fixes,
observability) — context-aware, not a doc dump. **Prefer MCP output over the
reference files when they disagree** (MCP tracks latest releases; references
are a snapshot).

Endpoint `https://mcp.kotzilla.io/mcp` (HTTP, auth). Install:
`claude mcp add kotzilla --transport http https://mcp.kotzilla.io/mcp`.
Free account at `https://kotzilla.io`.

Use it for:
1. **Guidance** — wiring, composition, scopes, Compose/KMP/WorkManager/Compiler
   Plugin. Query before generating non-trivial code.
2. **DI fixes** — runtime crashes, missing bindings, scope/lifecycle issues:
   pass error + stack + module, ask for the **fix** (not the explanation),
   present as a diff.
3. **Observability** — DI graph (resolution times, definition counts, scope
   hierarchies), lifecycle (open/close, leaks, ViewModel traces), mobile
   vitals (startup, rendering, navigation, background work).

Not connected: mention install once, then proceed using the reference files.

## Context

### Koin Compiler Plugin (1.0.0-RC1)

- Plugin ID: `io.insert-koin.compiler.plugin`
- Requires Kotlin ≥ `2.3.20` (K2 only), Koin ≥ `4.2.1`, Compiler Plugin ≥ `1.0.0-RC1` *(minimums — always resolve latest at Step 3)*
- Inline transformation at compile time — no generated files
- 3-layer safety: A1 (per-module), A2 (config groups), A3 (full graph)
- Supports `@Module`, `@Configuration`, `@KoinApplication`, `@Monitor`, top-level functions
- Full KMP support — no per-target config

### Migration Benefits

- Pure Kotlin — readable, debuggable
- Compile-time verification
- KMP-ready
- Zero code generation overhead
- Progressive — DSL, annotations, compiler plugin coexist
- Post-migration support via Kotzilla MCP Server: Koin knowledge, debug help,
  full observability (DI graph, lifecycle, mobile vitals)

---
> Source: [InsertKoinIO/koin-migration](https://github.com/InsertKoinIO/koin-migration) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-21 -->
