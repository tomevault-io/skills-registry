---
name: separation-of-concerns
description: Enforces code organization using features/ (verticals), platform/ (horizontals), and shell/ (thin wiring). Triggers on: code organization, file structure, where does this belong, new file creation, refactoring. Use when this capability is needed.
metadata:
  author: ntcoding
---

# Separation of Concerns

## Mental Model: Verticals and Horizontals

**Vertical** = all code for ONE feature, grouped together
**Horizontal** = capabilities used by MULTIPLE features

- `features/` — verticals, containing some combination of entrypoint/, commands/, queries/, domain/, infra/
  - commands/ orchestrates write operations (state mutations or external side-effects)
  - queries/ usually queries database directly but can query domain if easier
  - domain/ contains business rules
  - entrypoint/ only needed when exposing external interface (HTTP, CLI, events)
  - infra/ feature-specific infrastructure (mappers, middleware, persistence implementations)
- `platform/` — horizontals, contains `domain/` and `infra/`
  - domain/ depends on nothing — never imports from infra/
  - infra/ CAN depend on domain/ (implements domain contracts)
- `shell/` — app wiring/routing only (no business logic). Registers routes, bootstraps frameworks, connects message brokers. **Not a package entry point** — libraries use `src/index.ts` for that.
- `src/index.ts` — library package entry point. Pure barrel file (only re-export statements, no logic). Only needed for packages consumed by other packages.

**Note on terminology:** CLI subcommands (like `git commit`) are wired in shell/. Write operations in commands/ are CQRS commands — different concepts.

### Application structure

```
features/              platform/              shell/
├── checkout/          ├── domain/            └── cli.ts
│   ├── entrypoint/    │   └── tax-calc/
│   ├── commands/      └── infra/
│   ├── queries/           ├── external-clients/
│   ├── domain/            ├── http/
│   └── infra/             ├── cli/
│       ├── mappers/       ├── persistence/
│       └── persistence/   ├── config/
│                          └── logging/
└── refunds/
    ├── entrypoint/
    ├── commands/
    ├── queries/
    └── domain/
```

### Library package structure

Libraries use the same `features/` + `platform/` structure. The package is NOT the feature — still wrap in `features/{name}/`. Libraries don't need `shell/` unless they wire an app.

```
src/
├── index.ts               ← barrel (pure re-exports, no logic)
├── features/
│   └── extraction/
│       ├── queries/
│       ├── domain/
│       └── infra/
└── platform/
    ├── domain/
    └── infra/
```

Small utility/config/schema packages with no domain logic may be excluded from this architecture entirely.

---

## SoC-001: Always follow the code placement decision tree

🚨 **When unsure where code belongs, follow this decision tree.** Stop at the first match.

### Q1: Does it wire things together at startup?

Registers routes, bootstraps a framework, connects to a message broker, registers CLI subcommands with a framework.

→ **shell/**

**Test:** If you deleted this code, would the app still have all its logic but no way to start?

❌ **Not shell/ if:** It parses input, formats output, contains business logic, or loads/saves data. Those are deeper layers. Also not shell/ if it's a package barrel file re-exporting types for consumers — that's `src/index.ts`.

### Q2: Does it translate between external and internal formats?

Parses HTTP requests, CLI arguments, or queue messages into internal types. Formats internal results into HTTP responses, CLI output (tables, JSON, plain text), or outgoing messages. Maps domain errors to status codes or exit codes. Handles interactive prompts, progress bars, spinners.

→ **entrypoint/**

**Test:** If you changed protocols (HTTP → CLI, CLI → queue consumer, etc.), would you rewrite this code but keep commands/ and domain/ unchanged?

❌ **Not entrypoint/ if:** It loads data, modifies state, persists results, talks to a database, or enforces business rules. If you see load→modify→save, that's commands/, not entrypoint/.

### Q3: Does it orchestrate a write operation?

Loads data, invokes domain logic to modify it, then persists the result. Or coordinates a side-effect through an external service (payment, email, deployment).

→ **commands/**

**Test:** Does it change state? Would "Undo" make sense for this operation?

❌ **Not commands/ if:** It parses external input (HTTP requests, CLI args, queue messages) — that's entrypoint/. It contains the business rules themselves — that's domain/. It only reads data — that's queries/.

### Q4: Does it read and return data without modifying anything?

Loads data, transforms/aggregates it, returns a result. No side effects, no state changes.

→ **queries/**

**Test:** Could you run this 100 times with the same input and get the same result (assuming no external writes)?

❌ **Not queries/ if:** It writes, deletes, sends emails, triggers side effects, or enforces invariants.

### Q5: Is it business logic specific to ONE feature?

Validation rules, state transitions, invariants, domain calculations that only this feature cares about. Repository interfaces (domain contracts for persistence) also live here.

→ **features/{name}/domain/**

**Test:** Does another feature need this? If no → feature domain. If yes → keep reading.

❌ **Not feature domain/ if:** It orchestrates persistence (that's commands/) or is needed by multiple features (that's platform/domain/ or a dedicated domain library package).

### Q6: Is it infrastructure specific to ONE feature?

Repository implementations, response mappers, format adapters, feature-specific middleware. Implements domain contracts or handles protocol/format concerns for this feature only.

→ **features/{name}/infra/**

**Test:** Is this technical plumbing (not business rules) that only this feature needs?

❌ **Not feature/infra/ if:** It contains business rules (that's domain/). It's used by multiple features (that's platform/infra/). It parses external input or invokes commands (that's entrypoint/).

### Q7: Is it shared across features?

**Contains project-specific domain language** (your entity names, your business concepts, your workflow terms)?

→ **platform/domain/** (or a dedicated domain library package)

**Test:** Would a new developer need to understand your business to understand this code?

❌ **Not platform/domain/ if:** It's generic infrastructure with no project-specific concepts.

Shared value objects (Money, Email, Address) that enforce validation → platform/domain/ or a dedicated domain library.

**Shared technical concerns** (HTTP clients, database wrappers, logging, config, response formatters, shared middleware)?

→ **platform/infra/**

Platform/infra/ includes both generic utilities and project-specific conventions for infrastructure concerns (response formatters, error handling middleware).

**Test:** Is it infrastructure that multiple features or entrypoints use?

❌ **Not platform/infra/ if:** It contains business rules or domain invariants. That's platform/domain/.

---

## SoC-002: Dependencies point inward

**What:** Each layer can only depend on layers deeper than itself. Never depend on layers above you.

**Direction:** entrypoint → commands/queries → domain. Infrastructure supports all layers but domain never depends on infrastructure.

| From | Can depend on | Forbidden |
|---|---|---|
| entrypoint/ | commands/, queries/, own feature/infra/, platform/infra/ (restricted — see SoC-012) | domain/, platform/domain/ |
| commands/ | domain/, platform/infra/, platform/domain/, own feature/infra/ | entrypoint/, other features |
| queries/ | domain/ (read-only), platform/infra/, platform/domain/, own feature/infra/ | entrypoint/, commands/ |
| domain/ | platform/domain/ | all infra/ (feature or platform), entrypoint/, commands/, queries/ |
| shell/ | entrypoint/ (to wire routes) | commands/, queries/, domain/ directly |
| src/index.ts (barrel) | any internal module (re-exports only) | must not contain logic |

---

## SoC-003: Features never cross-import

**What:** Code that belongs to one feature stays in that feature's folder. Code used across features lives in platform/ or a dedicated domain library package.

**Why:** When shared logic is buried in one feature, other features either import across boundaries (coupling) or duplicate the logic (divergence). Both cause bugs.

**How:**
- Ask: "Does this conceptually belong to one feature?"
- YES → keep in features/
- NO → extract to platform/ or a dedicated domain library, name it for what it IS

```
❌ BAD - buried in one feature:
features/checkout/tax-calculator.ts
features/refunds/refund.ts           ← imports ../checkout/tax-calculator

❌ BAD - duplicated:
features/checkout/tax-calculator.ts
features/refunds/tax-calculator.ts   ← rules diverge over time

✅ GOOD - extracted to platform:
features/checkout/
features/refunds/
platform/domain/tax-calculation/     ← shared domain logic
```

**Query-only features:** If queries need to be shared across features, extract to a dedicated query library package — cross-feature imports are forbidden.

---

## SoC-004: Domain never does I/O

domain/ contains pure business logic. No database access, no HTTP calls, no file system, no external services. Domain defines contracts (interfaces/types) that infrastructure implements. Domain never imports from any infra/. Repository interfaces live in domain/; implementations live in infra/.

---

## SoC-005: No business logic in commands

Commands orchestrate write operations: load → delegate to domain → persist. Business rules (validation, state transitions, invariants) belong in domain/. Commands MAY invoke external services directly when no business rules are involved.

**Why:** Domain invariants must be enforced by domain objects, not scattered across command files.

**Violation signals:** conditionals on business state, inline validation, calculations, invariant checks in command files.

**Naming:** Imperative verb phrase — `place-order.ts`, `cancel-subscription.ts`, `approve-refund.ts`. Menu test: would this appear on a UI menu?

---

## SoC-006: Entrypoints are thin translation layers

Entrypoints translate between external world and commands/queries: parse external input → invoke command/query → map result to external response. Nothing else.

Entrypoints own: input parsing, output formatting, interactive prompts (progress bars, spinners), exit code mapping. When entrypoint/ grows large, extract infrastructure helpers to features/{name}/infra/.

**Violation signals:** orchestration logic, domain rules, data fetching, or database access in entrypoint files.

---

## SoC-007: Commands own their inputs

Each command defines its own dedicated input type. No sharing of input DTOs between commands. No dependency on external input types.

**Why:** Shared input types create coupling — changing one command's input breaks another. External types (HttpRequest, CLI arg objects) leak protocol concerns into commands.

**Violation signals:** HttpRequest, CLI arg objects, or raw message payloads passed to commands. Multiple commands sharing one input type.

---

## SoC-008: Queries read, never write

Queries handle read operations with no side effects. Can query database directly (no repository required) or load domain objects read-only.

**Why minimal layering:** Queries don't mutate state — no invariants to protect. Optimize for read performance and simplicity.

**Violation signals:** writes, deletes, emails, side effects, or invariant enforcement in query files.

**Naming:** Read-operation prefix — `get-order-summary.ts`, `list-pending-refunds.ts`, `search-products.ts`. Query-only features need only `queries/`, no domain/ required.

---

## SoC-009: No helpers in commands or queries

commands/ contains ONLY command files. queries/ contains ONLY query files. No helpers, utilities, or nested folders. If logic doesn't fit in the command/query itself, it belongs in domain/ or infra/.

---

## SoC-010: Co-locate by change, not kind

**What:** Files used together live together. Never group by category.

**Why:** Type-based grouping scatters related code. One change = many folders. Co-location means one change = one folder.

**How:**
- Ask: "If I change this feature, which files change together?"
- Group those files in one folder

Forbidden everywhere: `types/`, `models/`, `validators/`, `assertions/`, `schemas/`, `interfaces/`, `value-objects/`, and their single-file equivalents.

**Exception:** Shared test fixtures used across multiple test files may live in a `fixtures/` file or folder.

---

## SoC-011: External wrappers in platform/infra

Generic wrappers for external services (APIs, databases, SDKs) live separately from code that uses them in domain-specific ways.

**Test:** "Would the creators of this external service recognize this code?" YES → platform/infra/external-clients/. NO → your domain code.

---

## SoC-012: Infra uses standard sub-folders

**What:** All infra/ directories (feature and platform) use standard sub-folders. Only add non-standard sub-folders when logic genuinely doesn't fit these locations.

🚨 **CRITICAL:** No files at infra/ root. Everything must be in a sub-folder. Dumping files at `infra/file.ts` is forbidden.

### feature/infra/ sub-folders

| Sub-folder | Contains |
|---|---|
| `external-clients/` | Wrappers for third-party libraries used by this feature only (ts-morph, git, etc.). Same concept as platform/infra/external-clients/ but scoped to one feature |
| `mappers/` | Response/format mapping (domain result → external format) |
| `middleware/` | Feature-specific middleware (validation, rate limiting for this feature only) |
| `persistence/` | Repository implementations (the concrete database code behind domain contracts) |

### platform/infra/ sub-folders

| Sub-folder | Contains |
|---|---|
| `external-clients/` | Third-party service wrappers (Stripe, SendGrid, AWS SDKs). NOT for stdin/stdout or OS-level I/O — those go in `cli/` or `http/` |
| `middleware/` | Shared middleware used across features (auth, CORS, request logging) |
| `persistence/` | Database clients, connection pools, shared query builders |
| `http/` | Shared HTTP formatters, error handling middleware, response utilities |
| `cli/` | CLI I/O utilities (stdin readers, terminal formatting, TTY detection, arg parsing helpers, progress bars) |
| `messaging/` | Queue clients, event bus, pub/sub infrastructure |
| `config/` | Configuration loading, environment variable parsing |
| `logging/` | Structured logging, log formatters |

### Access rules per sub-folder

Which layers can access which infra sub-folders:

| Sub-folder | entrypoint/ | commands/ | queries/ | domain/ |
|---|---|---|---|---|
| `persistence/` | ❌ | ✅ | ✅ | ❌ |
| `external-clients/` | ❌ | ✅ | ✅ | ❌ |
| `http/` | ✅ | ❌ | ✅ | ❌ |
| `cli/` | ✅ | ❌ | ❌ | ❌ |
| `messaging/` | ✅ | ✅ | ❌ | ❌ |
| `config/` | ✅ | ✅ | ✅ | ❌ |
| `logging/` | ✅ | ✅ | ✅ | ❌ |
| `external-clients/` (feature) | ❌ | ✅ | ✅ | ❌ |
| `mappers/` | ✅ | ❌ | ❌ | ❌ |
| `middleware/` (feature) | ✅ | ❌ | ❌ | ❌ |
| `middleware/` (platform) | ✅ | ❌ | ❌ | ❌ |

---

## SoC-013: Separate intent from execution

High-level flow should be visible at one abstraction level without reading implementation details of each step.

**Violation signals:** methods where you can't describe the overall flow without reading every line; inline error handling or rollback logic that obscures the happy path sequence.

---

## SoC-014: Separate functions that depend on different state

Functions that depend on different state (different fields, databases, services) belong in different modules.

**Violation signals:** a class where methods cluster around different subsets of dependencies; constructor with unrelated dependencies used by different method groups.

---

## SoC-015: Separate functions that don't have related names

Functions in the same module should have names that relate to a common concept. If you can't name the module after what the functions have in common, they don't belong together.

**Violation signals:** module names ending in -helpers, -utils, -service containing unrelated operations; functions that change for different business reasons grouped together.

---

## Audit Checklist

When designing, implementing, refactoring, or reviewing code, verify each applicable rule.

**For code/architecture reviews:** Evaluate each file against SoC-001 through SoC-015. Verdict per rule: **PASS**, **FAIL** (cite file:line), or **N/A**.

| Code | Rule | Applies to |
|------|------|-----------|
| SoC-001 | Always follow the code placement decision tree | All files |
| SoC-002 | Dependencies point inward | All layer files |
| SoC-003 | Features never cross-import | features/ |
| SoC-004 | Domain never does I/O | domain/ |
| SoC-005 | No business logic in commands | commands/ |
| SoC-006 | Entrypoints are thin translation layers | entrypoint/ |
| SoC-007 | Commands own their inputs | commands/ |
| SoC-008 | Queries read, never write | queries/ |
| SoC-009 | No helpers in commands or queries | commands/, queries/ |
| SoC-010 | Co-locate by change, not kind | All |
| SoC-011 | External wrappers in platform/infra | platform/ |
| SoC-012 | Infra uses standard sub-folders | infra/ |
| SoC-013 | Separate intent from execution | All |
| SoC-014 | Separate functions that depend on different state | All |
| SoC-015 | Separate functions that don't have related names | All |

Each code references detailed rules in the sections above. Do not proceed until all applicable rules pass.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntcoding) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
