---
name: stnl-mobile-ios-swift-quality
description: Portable iOS Swift quality guardrail for implementation and review, focused on SwiftUI and UIKit boundaries, state ownership, concurrency safety, lifecycle cleanup, navigation, forms, networking, persistence, design system reuse, platform conventions, and testability. Use when this capability is needed.
metadata:
  author: stnl-studios
---

# iOS Swift Quality Guardrail

## Mission
Prevent an iOS Swift change from being accepted only because it works when it degrades UI boundaries, state ownership, lifecycle safety, concurrency safety, navigation, platform conventions, contract boundaries, maintainability, or testability.

This guardrail is an operational quality capsule for implementation, technical planning, and review. It does not impose MVVM, Coordinator, Repository, Combine, SwiftData, CoreData, Alamofire, URLSession, a folder structure, a design system, or a testing strategy when the target project does not use it.

## Authority
Apply this order without inversion:

1. Task, feature specification, implementation brief, or approved scope.
2. Existing project conventions, rules, and contracts.
3. This iOS Swift quality guardrail.
4. Agent judgment.

Rules:
- the approved scope defines the strongest implementation boundary when it exists
- when used inside Sentinel, the Slice SPEC is the approved scope source
- the target project defines real paths, names, boundaries, architecture, libraries, contracts, UI patterns, and local shape
- this guardrail rejects structural iOS Swift degradation inside the approved scope
- agent judgment only fills small gaps; it does not replace the approved scope, project rules, local convention, or platform lifecycle constraints

## Explicit Non-Goals
This guardrail does not define:
- app architecture
- folder structure
- navigation pattern
- networking stack
- persistence technology
- state management library or reactive framework
- dependency injection style
- design system
- testing strategy

Infer these from the target project.

If an existing convention exists, follow it. If no convention exists, use the smallest local solution that preserves the quality rules in this guardrail. If the missing convention materially affects the task or approved scope, report it as a risk or blocker instead of inventing a global pattern.

## Project Pattern Discovery
Before implementing or reviewing iOS Swift code, inspect the target project's real pattern for:
- SwiftUI view and UIKit view controller responsibilities
- view models, stores, presenters, or observable state
- coordinators, routers, `NavigationStack`, `NavigationPath`, UIKit navigation, sheets, modals, alerts, and dismiss flows
- services, clients, repositories, mappers, DTOs, domain models, and view state
- async/await, Combine, delegates, callbacks, streams, and cancellation
- local persistence, cache, and storage boundaries
- forms, validation, keyboard, focus, and submit behavior
- shared components, design system, accessibility, typography, spacing, colors, and platform interaction patterns
- tests and preview patterns

Rules:
- use existing names, paths, and patterns
- do not force MVVM, Coordinator, Repository, Combine, SwiftData, CoreData, Alamofire, URLSession, or any fixed architecture
- follow existing ViewModel, Store, Coordinator, Router, Service, Repository, Client, Mapper, DTO, Domain, or Design System patterns when they exist
- do not introduce parallel architecture for one slice unless the approved scope explicitly requires it
- do not propagate locally inconsistent, deprecated, or superseded patterns when stronger evidence shows a dominant, newer, or area-local pattern
- when competing patterns exist, follow the dominant, newer, or area-local pattern evidenced by the approved scope and modified area

## iOS Concept Mapping
Names change by project; responsibilities do not.

Treat these as conceptual equivalents:
- `screen boundary`: SwiftUI View, UIKit ViewController, screen, route, host view, container, or feature root
- `state owner`: ViewModel, Store, Presenter, ObservableObject, model controller, or equivalent project abstraction
- `navigation layer`: coordinator, router, navigation model, `NavigationStack`, `NavigationPath`, UIKit navigation controller flow, sheet state, modal state, or equivalent
- `data boundary`: service, client, repository, SDK wrapper, query object, persistence adapter, cache, or equivalent
- `contract`: DTO, API model, persistence model, domain model, view state, form value, binding contract, or equivalent
- `mapping`: mapper, adapter, presenter, formatter, transformer, normalizer, or equivalent
- `lifecycle work`: `Task`, Combine subscription, timer, notification observer, delegate, stream, async sequence, callback, or closure retaining `self`

Use the target project's real terms when writing, editing, or reviewing.

## UI Boundary
SwiftUI Views and UIKit ViewControllers should not become the place where networking, parsing, persistence, navigation, validation, analytics, and state orchestration are all mixed together.

Rules:
- SwiftUI Views should focus on rendering state and expressing user intent
- UIKit ViewControllers should coordinate view lifecycle and UI behavior without becoming Massive View Controllers
- keep request construction, response interpretation, persistence mapping, and heavy validation out of render code
- keep reusable UI in the project's shared component or design-system location when one exists
- split responsibilities only where the project pattern supports it or complexity would remain mixed without separation

Reject a change when a screen becomes the place where UI, remote flow, persistence, mapping, navigation, validation, analytics, and feedback all accumulate without a project-approved reason.

## SwiftUI State Ownership
State wrappers must match ownership and lifecycle.

Rules:
- use `@State` for simple view-owned value state
- use `@StateObject` when the view owns the observable object and must keep it stable across redraws
- use `@ObservedObject` when the observable object is owned and injected by a parent or external layer
- use `@EnvironmentObject` only when the project already uses that pattern or the approved scope explicitly requires it
- use `@Binding` for parent-owned editable state
- do not recreate ViewModels, services, clients, repositories, or long-lived dependencies inside `body`
- do not perform heavy work in `body`
- avoid state ownership that resets user input, triggers duplicate loads, or hides data flow

## Swift Concurrency and Main Actor Safety
Async code must be lifecycle-aware and explicit about UI mutation.

Rules:
- mutate UI state on the main actor
- mark ViewModels, Stores, or Presenters that publish UI state as `@MainActor` when appropriate
- handle async errors intentionally according to the project pattern
- handle cancellation intentionally when work may outlive the screen or request
- do not use unstructured `Task {}` without lifecycle reasoning
- avoid duplicate loads caused by careless `.task`, `.onAppear`, view re-rendering, or repeated binding updates
- do not use `try!`, force unwrap, or `fatalError` in normal product flow
- do not hide failed async work behind silent fallbacks that mask contract or state errors

## Lifecycle and Memory Cleanup
This is an explicit gate.

If the implementation creates or touches any of these, it must define cleanup or cancellation ownership, or justify why cleanup is unnecessary:
- `Task`
- Combine subscription
- `Timer`
- `NotificationCenter` observer
- delegate
- stream
- async sequence
- callback
- closure retaining `self`

Rules:
- avoid retain cycles
- capture `self` intentionally in escaping closures
- store and cancel Combine subscriptions according to the project pattern
- invalidate timers
- remove observers when needed
- nil out or clear delegates when ownership requires it
- cancel tasks when the work should not continue after disappearance or deallocation
- avoid updating deallocated, stale, or no-longer-current UI state

## Navigation and Presentation Boundaries
Navigation belongs in the project's navigation boundary when one exists.

Cover relevant flows for:
- coordinators
- routers
- `NavigationStack` and `NavigationPath`
- UIKit navigation
- sheets
- modals
- alerts
- dismiss flows

Rules:
- keep navigation in the project's existing navigation layer when one exists
- do not put navigation in Service, Repository, Client, Mapper, persistence, or networking code
- do not hide navigation as an unreviewable side effect inside networking callbacks
- keep modal, sheet, alert, and dismiss state explicit and maintainable
- consider success, cancellation, and error dismissal paths when applicable
- preserve deep link, back navigation, and restoration behavior when the modified area depends on it

## Forms and Validation
Forms should preserve user input and make validation states explicit.

Rules:
- separate validation from rendering when the project has a pattern for it
- represent field errors in state
- handle disabled, loading, invalid, and double-submit behavior when applicable
- avoid duplicated validation logic across views
- preserve user input across loading, validation, error, and view state changes
- handle keyboard, focus, submit, and dismiss behavior according to project conventions
- cover loading, error, empty, disabled, success, and offline states when applicable
- isolate nontrivial mapping from form values to API, persistence, domain, or view-state contracts according to the local pattern

## Networking and Contract Boundaries
Do not collapse external contracts into UI code.

Rules:
- do not perform direct networking from a View when the project has Service, Client, Repository, SDK wrapper, or equivalent patterns
- do not construct requests in a View when a lower layer exists
- keep response interpretation and error normalization out of render code
- do not leak DTOs into UI when the project uses domain or view-state mapping
- decode predictable optional or missing fields safely
- do not collapse API, persistence, DTO, domain, and view-state mapping into one large UI method
- do not alter external contracts outside approved scope
- do not add silent fallbacks that hide contract errors
- preserve generated clients, schema contracts, and versioning rules when the project uses them

## Local Persistence
Follow the project's persistence boundary.

Rules:
- use the existing persistence layer, storage abstraction, cache, or data access pattern
- do not access CoreData, SwiftData, UserDefaults, files, Keychain, SQLite, or cache directly from UI when the project has a boundary for it
- keep persistence mapping away from render code
- handle cache invalidation and stale data intentionally when applicable
- preserve migration, key naming, storage security, and synchronization patterns when touched by the approved scope

## Design System and Platform Conventions
iOS work should preserve the project's UI system and platform behavior.

Rules:
- use existing components, tokens, theme, typography, spacing, colors, and accessibility patterns
- preserve native iOS interaction expectations unless the approved scope requires a different behavior
- avoid custom UI when the project already has a reusable component
- do not degrade accessibility, Dynamic Type, VoiceOver labels, hit targets, or color contrast when touching UI
- preserve localization and formatting patterns for strings, dates, numbers, currencies, and units
- keep previews, snapshots, or UI tests aligned with the project pattern when the touched UI relies on them

## Relevant UI States
iOS work is not complete when it only covers the happy path.

For the modified flow, handle or explicitly justify these states as not applicable:
- loading
- empty
- error
- disabled
- validation
- success
- cancellation
- offline or stale cache when applicable
- unauthorized or permission denied when applicable
- retry when applicable

State handling should follow the target project's UI and feedback patterns.

## Testability
Use testability as a signal of structural quality.

Rules:
- validation, mapping, state transitions, async success and error paths, and navigation decisions should be testable according to the target project's test pattern
- avoid coupling that forces a full app flow test for every small rule when a local test would match the project pattern
- do not add artificial layers only for tests
- separate responsibilities when mixing them prevents reasonable testing in the target project

## Anti-overengineering
Do not create ViewModels, Stores, Coordinators, Routers, Services, Repositories, Clients, Mappers, protocols, dependency containers, or component hierarchies only to satisfy a generic pattern.

Reject:
- broad refactors outside scope
- architecture migration inside a small slice
- creating new abstractions just to satisfy the guardrail
- replacing existing project patterns with a preferred architecture
- large code samples or rigid templates
- speculative cleanup unrelated to the requested task

Add structure only when:
- the target project already uses that structure
- the approved scope requires it
- complexity would remain mixed without the separation
- testability or maintainability would clearly be worse without the separation

Any new structure must be the smallest sufficient structure and compatible with its surroundings.

## Conceptual Violation Examples
- Massive ViewController: a ViewController owns lifecycle, networking, validation, mapping, persistence, navigation, analytics, and UI state. Better direction: preserve the project's screen, state, data, and navigation boundaries.
- Massive ViewModel or Store: one state owner becomes a long procedural feature script with unrelated validation, networking, persistence, navigation, and formatting. Better direction: separate only the responsibilities the local architecture already separates or clearly needs.
- Bypassed networking boundary: a View calls a raw client or SDK while the surrounding area uses a service, client, repository, or generated API layer. Better direction: preserve the existing data boundary.
- Unowned lifecycle work: a `Task`, subscription, timer, observer, stream, delegate, callback, or retaining closure is created without cleanup or cancellation ownership. Better direction: define lifecycle ownership and cleanup.
- Hidden navigation side effect: a network callback dismisses or pushes a screen from a lower data layer. Better direction: surface success, cancellation, and error state to the navigation boundary.
- DTO leakage: raw API or persistence models drive UI directly where the project uses domain or view-state mapping. Better direction: map at the established boundary.
- Design system bypass: a local button, input, modal, alert, loading, empty, or error UI duplicates a project component. Better direction: reuse the shared component or approved pattern.
- Overengineered architecture: a new coordinator, repository, protocol, or dependency container is added for a small slice only because a generic architecture would use it. Better direction: keep the smallest structure that matches local conventions.

## Completion Gate
iOS Swift work is not complete if it introduces:
- Massive ViewController
- Massive ViewModel, Store, or Presenter
- direct networking from View against project pattern
- mixed UI, API, persistence, mapping, navigation, validation, and analytics in one place
- unsafe async UI mutation
- unowned lifecycle work
- retain cycle risk
- duplicate async loads
- happy-path-only UI
- ignored design system
- ignored project navigation pattern
- broad refactor outside approved scope
- DTO or contract leakage into UI against project convention
- platform, accessibility, Dynamic Type, VoiceOver, hit target, or contrast regression in touched UI

## Review Gate
A reviewer, validator, or equivalent agent must reject an implementation that works but violates iOS Swift structural quality.

Expected result:
- `FAIL` when the implementation violates the approved scope, project-specific contract, target project's pattern, platform lifecycle constraints, or this iOS Swift quality gate
- `PASS` only when it respects the approved scope, project conventions, UI boundaries, state ownership, concurrency safety, lifecycle cleanup, navigation boundaries, contract boundaries, design system reuse, platform conventions, relevant states, and testability

When failing, identify the mixed responsibility, bypassed boundary, unsafe state ownership, lifecycle risk, concurrency risk, navigation leak, contract leakage, missing UI state, design system bypass, accessibility regression, or testability problem.

---
> Source: [stnl-studios/sentinel-protocol](https://github.com/stnl-studios/sentinel-protocol) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
