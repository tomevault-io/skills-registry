---
name: ios-domain-delivery
description: Use WHEN implementing or reviewing iOS Swift features TO deliver SwiftUI, UIKit, async/await, Combine, navigation, persistence, permissions, background work, accessibility, test, release, and rollback changes.
metadata:
  author: vTRKA
---

# iOS Domain Delivery

## Overview

iOS Domain Delivery is the specialist method for Swift and Apple-platform
changes that must respect view ownership, structured concurrency, persistence,
privacy permissions, background execution limits, accessibility, tests, App
Store release constraints, and rollback. It supports SwiftUI-first codebases
while recognizing UIKit, Combine, Core Data, SwiftData, Keychain, and extension
surfaces that appear in mature apps.

## When to Use

Use for SwiftUI or UIKit features, view models, services/repositories,
async/await or Combine pipelines, navigation, persistence, permissions, app or
scene lifecycle, background tasks, widgets/intents, accessibility, unit/UI
tests, release risk, or rollback planning.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source
first, preserve evidence, keep scope narrow, verify before completion claims,
and reduce confidence when simulator/device, accessibility, persistence,
permission, or rollback evidence is missing.

## Step 0 - Read source of truth

1. Read the user request, `AGENTS.md`, active task item, owned write set, and
   prior iOS decisions in `.supervibe/memory/`.
2. Inspect `Package.swift` or Xcode project conventions, app targets, schemes,
   Info.plist privacy keys, entitlements, persistence containers, app lifecycle,
   routing, dependency injection, localized resources, and nearest XCTest or
   XCUITest files.
3. Search existing patterns for `@Observable`, `ObservableObject`, `@MainActor`,
   `.task`, cancellation, Combine bridges, navigation, Core Data/SwiftData,
   Keychain, permission prompts, localization, and accessibility modifiers.
4. Use CodeGraph for unfamiliar modules and CodeGraph or symbol search before
   changing public views, view models, protocols, route identifiers, entity
   models, persistence schemas, or App Intent surfaces.

## When not to use

- Do not use when the task is generic planning, product shaping, design, or release governance and no iOS implementation or review boundary exists.
- Do not use when another stack, database, security, deployment, or API owner has the primary decision; hand off to that specialist and keep the iOS part scoped.
- Do not use to justify dependency swaps, broad rewrites, heavy test runs during graph execution, or mixed old-plan scope without explicit approval.

## Decision tree

```text
Rendering UI? -> SwiftUI View or UIKit controller; no direct repository logic.
Screen state? -> @MainActor view model or local @State; own async tasks and cancellation.
Existing stream pipeline? -> keep Combine if publisher-native; bridge intentionally to async sequence.
One-shot request? -> async/await with typed throws or Result; no fire-and-forget Task.
Navigation? -> typed route/path/coordinator contract; preserve deep link/back behavior.
Persistence? -> Core Data/SwiftData/GRDB/local file with migration and rollback story.
Permission needed? -> purpose string, user-intent prompt, denied/restricted/settings state.
Background work? -> BGTask, URLSession background, push, or silent refresh with OS budget.
UIKit interop? -> wrapper owns delegate lifecycle, main-thread UI updates, and teardown.
Accessibility affected? -> VoiceOver, Dynamic Type, contrast, rotor/action, 44pt target.
```

## Procedure

1. Define the slice: target, minimum OS, entry view/controller, state owner,
   persistence or service boundary, permission need, verification command, and
   rollback path.
2. Map layers. Views render state and send intents; view models own screen
   state and structured tasks; services/repositories hide I/O; persistence
   models and migrations are isolated; UIKit adapters stay at platform seams.
3. Control concurrency. Prefer `async/await` for request/response work, actors
   for shared mutable state, `.task` or stored cancellable `Task` handles for
   lifecycle, `@MainActor` for UI-facing models, and explicit cancellation tests.
4. Use Combine deliberately. Keep it for publisher-native sources such as
   notifications or reactive stores; bridge at one seam with `.values` or an
   adapter; do not intermix Combine and async/await throughout one feature.
5. Design navigation contracts. Preserve existing coordinator/path style, keep
   route arguments value-typed and minimal, handle deep links, restore state,
   and avoid stringly-typed route branching.
6. Handle persistence. Choose the repository's current persistence tool, model
   optionality honestly, migrate schema safely, keep file and app group paths
   platform-correct, and never force unwrap decoded or persisted user data.
7. Implement permissions as product states. Include usage description keys,
   rationale copy, denied/restricted/limited paths, settings recovery, and
   graceful degradation. Do not prompt on launch unless the platform requires it.
8. Respect background limits. Use BGTaskScheduler, background URLSession,
   notifications, or app lifecycle hooks according to the job; make work
   idempotent, bounded, cancelable, observable, and recoverable after launch.
9. Add accessibility from the first diff. Labels, hints, values, traits,
   Dynamic Type up to accessibility sizes, focus order, contrast, and 44pt
   targets are part of acceptance, not polish.
10. Write tests first when behavior changes. Cover view model state, async
    success/failure/cancellation, Combine emissions, persistence migration,
    permission denial, navigation, accessibility semantics, and UI flow where
    risk justifies XCUITest.
11. Verify with scoped `xcodebuild`, Swift Package, lint, format, and UI test
    commands when policy allows. When this worker is told not to run tests,
    record the exact deferred commands and source evidence only.
12. Repair loop: localize failures to view, view model, service, persistence,
    permission, background, or navigation boundary; patch the smallest cause;
    rerun the same focused command when allowed.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Add a SwiftUI medication reminder screen:

1. `MedicationReminderView` renders state from a `@MainActor @Observable`
   view model and never calls notification APIs directly.
2. The view model validates time, stores reminders through a repository, and
   schedules notifications through a protocol-defined service.
3. Permission state covers not determined, denied, provisional, authorized, and
   settings recovery.
4. Persistence migration adds a nullable `timezoneIdentifier`, backfills on
   first write, and documents rollback.
5. Tests cover validation failure, permission denial, successful scheduling,
   cancellation, migration, dynamic type rendering, and XCUITest smoke for the
   create/edit flow.

## Good and bad delivery paths

Good delivery path: deliver through SwiftUI view state, MainActor view model,
repository, notification service protocol, permission state machine,
persistence migration, and settings recovery. Runtime-specific tests include
XCTest validation and permission branches, repository and migration coverage,
notification scheduling/cancellation fakes, dynamic type and accessibility
checks, and XCUITest smoke for changed flows. Rollback removes or flags the
route, cancels scheduled notifications, and keeps nullable migration/backfill
compatible. Failure boundaries are denied or provisional permission, timezone
conversion, duplicate scheduling, persistence failure, notification
unavailable, settings recovery, and migration mismatch.

Bad unsafe path: schedule notifications directly from SwiftUI, assume
authorization, write a required migration without downgrade thinking, and
verify only in preview. That path has no runtime-specific tests for the
changed stack surface, no concrete rollback beyond hope or manual cleanup, and
weak failure boundaries for denied or provisional permission, timezone
conversion, duplicate scheduling, persistence failure, notification
unavailable, settings recovery, and migration mismatch.

## Anti-example or Common rationalizations

- "This Task is short, so cancellation does not matter" fails when users leave
  the view, app goes background, or a stale response overwrites newer state.
- "The value is always non-nil here" fails when network, persistence,
  localization, deep links, or restored state supplies unexpected input.
- "Combine is already in the app, so use it everywhere" fails when a simple
  request becomes a pipeline with hidden cancellation and error behavior.
- "We can prompt permissions during onboarding" fails if the user has not
  expressed intent and denial leaves no usable feature path.

## Common rationalizations

- "It is just iOS, so the generic implementation pattern is enough" fails because lifecycle, runtime, data, and deployment constraints differ by stack.
- "A broad suite will prove it faster" fails in graph execution; use the declared scoped command and reserve broad validators for the final release gate.
- "We can clean up the architecture while here" fails unless that cleanup is in the accepted graph scope, has rollback, and has its own verification path.

## Red flags

- `Task {}` without stored handle, `.task`, cancellation path, or actor context.
- UI-facing model not `@MainActor` while mutating observable state.
- Force unwraps, `try!`, or implicitly unwrapped optionals on external data.
- View model imports SwiftUI types unnecessarily or performs navigation by
  hard-coded strings.
- Missing Info.plist privacy string, entitlement explanation, or denied path.
- Fixed-height text layouts that truncate at accessibility Dynamic Type sizes.
- Persistence schema changes without migration, seed compatibility, or rollback.

## Checklist

- View, view model, service/repository, persistence, and platform seams are
  separated.
- Concurrency has owner, actor/main-thread rule, cancellation, and error model.
- Navigation, permissions, background work, and persistence each name rollback
  or compatibility behavior when touched.
- Accessibility covers VoiceOver, Dynamic Type, contrast, traits, values, and
  hit targets.
- XCTest/XCUITest or deferred final-gate commands cover success, failure,
  cancellation, permission, and migration paths.

## Failure modes

- The iOS specialist applies a generic pattern and misses framework-owned lifecycle, typing, permission, migration, cache, or deployment behavior.
- The worker mixes a new graph task with stale plan scope and creates a larger review surface than the MVP flow needs.
- The task closes with prose only: no source evidence, no scoped command or final-gate deferral, no rollback, and no next action for blockers.

## Output contract

- `status`: PASS, BLOCKED, PARTIAL, or DEFERRED.
- `slice`: SwiftUI, UIKit, view model, service, persistence, permission,
  background, navigation, accessibility, or release behavior changed.
- `boundaries`: files touched by layer and why each belongs there.
- `concurrency`: async/await, Combine, actor, cancellation, and main-thread
  decisions.
- `persistencePermissionsBackground`: schema/data, privacy, background budget,
  and rollback evidence.
- `accessibilityAndRelease`: Dynamic Type, VoiceOver, device/simulator, phased
  release, and rollback notes.
- `tests`: focused commands run with exit code or explicit deferral reason.
- `confidence`: score with caps from missing UI, device, migration, permission,
  accessibility, or release evidence.

## Guard rails

- DO NOT: use force unwraps or `try!` for user, network, persistence, deep link,
  or permission data.
- DO NOT: introduce a new architecture, router, persistence framework, or
  dependency container when the repo already has one.
- DO NOT: expand entitlements, background modes, or privacy permissions without
  product reason, user flow, and rollback.
- ALWAYS: keep UI mutations on the main actor and long-running work cancellable.
- ALWAYS: localize user-facing strings and verify accessibility at large
  content sizes when UI changes.

## Verification

- Unit: XCTest for view models, services, repositories, actor behavior,
  async/await cancellation, and Combine emissions.
- UI: XCUITest or ViewInspector/snapshot tests where local project conventions
  support them.
- Persistence: migration tests or seed compatibility checks for Core Data,
  SwiftData, GRDB, file storage, or Keychain wrapper changes.
- Accessibility: Accessibility Inspector, VoiceOver traversal, Dynamic Type at
  accessibility sizes, and contrast checks for changed surfaces.
- Release: scoped `xcodebuild test`, `swift test`, `swiftlint`, `swift-format`,
  build/archive dry run, phased rollout, feature flag, or rollback notes when
  policy allows.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

- examples/delivery.md - worked iOS implementation example, anti-example, and verification fixture.
- domain-packs/ios.md - iOS practice pack with review matrix, rollback prompts, and MVP guard rails.
- evals/regression.json - Use when calibrating ios-domain-delivery trigger boundaries, happy-path/failure-path coverage, boundary rollback behavior, or resource-tree regressions.

## Related

- `supervibe:source-driven-development`
- `supervibe:tdd`
- `supervibe:test-strategy`
- `supervibe:verification`
- `supervibe:code-review`
- `supervibe:stacks/ios:ios-developer`
- `supervibe:stacks/android:android-developer`
- `supervibe:stacks/flutter:flutter-developer`
## Supporting references

### Resource tree hardening

- `references/practice-pack.md` - Read when ios-domain-delivery needs deeper load rules, local evidence anchors, gotchas, or a final checklist.
- `scripts/self-check.mjs` - Run with `--check` before claiming the ios-domain-delivery resource tree is complete; add `--json` when machine-readable evidence is needed.
- `evals/regression.json` - Use when tuning ios-domain-delivery trigger boundaries or checking should-trigger and should-not-trigger prompts.
- `examples/workflow.md` - Load when a concrete ios-domain-delivery workflow example or anti-example would clarify the next action.
- `templates/output-contract.md` - Use when emitting agent-output so status, evidence, blockers, confidence, and nextAction stay consistent.

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
