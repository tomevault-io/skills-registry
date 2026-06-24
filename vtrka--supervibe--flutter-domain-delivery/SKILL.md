---
name: flutter-domain-delivery
description: Use WHEN implementing or reviewing Flutter and Dart features TO deliver widget boundaries, state management, async stream, navigation, platform channel, persistence, accessibility, golden, widget, and integration tests.
metadata:
  author: vTRKA
---

# Flutter Domain Delivery

## Overview

Flutter Domain Delivery is the specialist playbook for Dart and Flutter work
where rendering, state ownership, async streams, navigation, platform channels,
persistence, accessibility, tests, and release rollback must be treated as one
delivery problem. It prevents giant widgets, rebuild storms, leaky stream
subscriptions, stringly typed routes, fragile channel calls, and golden tests
that approve screenshots without behavior.

## When to Use

Use for Flutter screens, widgets, BLoC/Riverpod/Provider state, repositories,
streams, navigation, platform channels, storage, localization, accessibility,
golden/widget/integration tests, build flavors, or rollback planning.

## Expert Operating Standard

Follow `<resolved-supervibe-plugin-root>/docs/references/skill-expert-operating-standard.md`: read local source
first, preserve evidence, keep scope narrow, verify before completion claims,
and reduce confidence when analyzer, widget, golden, integration, channel,
device, or rollback evidence is missing.

## Step 0 - Read source of truth

1. Read the user request, `AGENTS.md`, active task item, owned write set, and
   Flutter decisions in `.supervibe/memory/`.
2. Inspect `pubspec.yaml`, `analysis_options.yaml`, `lib/`, `test/`,
   `integration_test/`, localization files, route setup, state-management
   packages, generated files, platform folders, and nearest test utilities.
3. Search existing patterns for BLoC, Riverpod, Provider, repository boundaries,
   stream ownership, route definitions, platform channels, persistence, golden
   tests, widget tests, and semantics.
4. Use CodeGraph for unfamiliar features and CodeGraph or symbol search before
   changing public widgets, providers/blocs, repository interfaces, route names,
   generated models, channel contracts, or shared design-system components.

## When not to use

- Do not use when the task is generic planning, product shaping, design, or release governance and no Flutter implementation or review boundary exists.
- Do not use when another stack, database, security, deployment, or API owner has the primary decision; hand off to that specialist and keep the Flutter part scoped.
- Do not use to justify dependency swaps, broad rewrites, heavy test runs during graph execution, or mixed old-plan scope without explicit approval.

## Decision tree

```text
Rendering UI? -> small Widget/Page/Component; no repository or platform calls.
State shared by widgets? -> chosen BLoC/Riverpod/Provider owner with scoped rebuilds.
Local ephemeral state only? -> StatefulWidget, ValueNotifier, or controller with dispose.
Async stream? -> ownership, subscription lifecycle, cancellation, error, backpressure.
Navigation? -> existing router style, typed args, guards, deep links, restoration.
Native capability? -> typed MethodChannel/EventChannel wrapper + timeout + mapped errors.
Persistence? -> existing store/db/cache, migrations, encryption, offline/stale behavior.
Accessibility? -> Semantics, focus, labels, tap target, text scale, contrast.
Visual regression risk? -> golden test with fonts/assets/platform fixed, plus behavior test.
Cross-platform behavior? -> Android/iOS/web/desktop differences named and tested/smoked.
```

## Procedure

1. Define the slice: feature, route, state owner, data source, channel or
   persistence surface, accessibility impact, verification command, and rollback
   path.
2. Map widget boundaries. Pages assemble dependencies and state providers;
   widgets render inputs and callbacks; components are small, reusable, and
   free of business logic. Dispose controllers, focus nodes, animation
   controllers, and subscriptions.
3. Choose state ownership from the repo's existing pattern. BLoC uses events,
   immutable states, equality, and `bloc_test`; Riverpod uses providers,
   `select`, overrides, and `ProviderContainer`; Provider uses narrow
   `Selector`s and explicit notifications. Do not mix patterns in one feature
   without an adapter seam.
4. Control async work. Model loading/data/empty/error/canceled states, map
   exceptions to domain failures, close stream controllers, cancel
   subscriptions, and avoid `setState` after dispose.
5. Protect navigation. Use existing router APIs, keep arguments typed and
   serializable, handle guards and deep links, test back behavior, and avoid
   business logic inside route builders.
6. Wrap platform channels. Dart exposes a typed interface with timeout,
   `PlatformException` mapping, unavailable/permission-denied/user-cancel
   cases, and tests with a mock binary messenger. Native implementations mirror
   error codes on Android and iOS.
7. Handle persistence and offline. Use existing Hive/SQLite/Drift/SharedPrefs/
   secure-storage patterns, model migrations, encryption needs, stale data,
   conflict resolution, and cache invalidation.
8. Build accessible UI. Add Semantics where defaults are insufficient, preserve
   keyboard/focus order, support large text scale, use 48dp tap targets, and
   localize user-facing strings.
9. Add tests first when behavior changes. Cover domain/repository logic, state
   transitions, widget rendering, semantics, golden variants, channel success
   and failure, route behavior, and integration flows where user journeys cross
   framework boundaries.
10. Verify with scoped `flutter test`, `flutter analyze`, `dart format`,
    `build_runner`, golden update/review, and `integration_test` commands when
    policy allows. When this worker is told not to run tests, record the exact
    deferred commands and source evidence only.
11. Repair loop: localize failures to widget, state owner, repository, stream,
    route, channel, persistence, or platform folder; patch the smallest cause;
    rerun the same scoped command when allowed.

1. Read the source artifact, owned file paths, graph/task scope, and current project convention; record the evidence path, command, receipt, or runtime state that proves the starting point.
2. If required source, owner, dependency, runtime boundary, or approval is missing, stop and return BLOCKED with the missing field, impacted artifact, and next action instead of guessing.
3. After edits or reviewer findings, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, artifact path, confidence, and remaining blocker before completion.

## Worked example

Add a camera import flow:

1. Route creates `ImportPhotoPage` and provides an `ImportPhotoBloc`.
2. UI widgets render states only: idle, requesting permission, picker open,
   processing, imported, denied, failed.
3. Dart `CameraChannel` wraps MethodChannel with timeout and maps
   `permission_denied`, `user_cancelled`, and `hardware_unavailable`.
4. Repository persists imported metadata through the existing store and marks
   stale thumbnails for background refresh.
5. Tests cover bloc emissions, widget semantics, denied permission, channel
   timeout, route back behavior, one golden for key states, and an integration
   smoke on device.

## Good and bad delivery paths

Good delivery path: deliver through route, Bloc or state machine,
MethodChannel wrapper, repository persistence, permission states, and
background thumbnail refresh. Runtime-specific tests include Bloc emissions,
widget semantics and goldens, denied and permanently-denied permission,
MethodChannel timeout/error mapping, route back behavior, repository rollback,
and device smoke when hardware matters. Rollback removes or flags the route,
keeps persisted metadata backward compatible, and disables the
channel/background refresh. Failure boundaries are permission denial, user
cancel, hardware unavailable, channel timeout, storage failure, stale
thumbnail refresh, and route lifecycle loss.

Bad unsafe path: call MethodChannel from widgets, store imported files before
validation, collapse platform errors into generic failure, and verify with one
golden. That path has no runtime-specific tests for the changed stack surface,
no concrete rollback beyond hope or manual cleanup, and weak failure
boundaries for permission denial, user cancel, hardware unavailable, channel
timeout, storage failure, stale thumbnail refresh, and route lifecycle loss.

## Anti-example or Common rationalizations

- "This is just a small screen, so setState is fine" fails when the widget grows
  and every keystroke rebuilds the route.
- "The channel throws if native fails" fails because UI needs actionable domain
  errors, timeouts, and platform-specific unavailable states.
- "Golden tests prove the UI" fails if behavior, semantics, and text scaling
  are not tested.
- "The provider is global for convenience" fails when unrelated field changes
  rebuild the whole app and tests become order-dependent.

## Common rationalizations

- "It is just Flutter, so the generic implementation pattern is enough" fails because lifecycle, runtime, data, and deployment constraints differ by stack.
- "A broad suite will prove it faster" fails in graph execution; use the declared scoped command and reserve broad validators for the final release gate.
- "We can clean up the architecture while here" fails unless that cleanup is in the accepted graph scope, has rollback, and has its own verification path.

## Red flags

- Widget over 150-200 lines owning network, repository, route, or channel logic.
- `StreamSubscription` or controller without `dispose`.
- `context.watch` or `Consumer` high in the tree causing broad rebuilds.
- Platform channel call without timeout, error mapping, or native parity.
- Hard-coded strings, missing Semantics, or small tap targets.
- Generated files edited by hand instead of source annotations plus build step.
- Golden tests with unstable fonts, animations, network images, or platform
  rendering assumptions.

## Checklist

- Widget, state owner, domain, repository, data source, route, and channel
  boundaries are explicit.
- State management matches local convention and scopes rebuilds.
- Async streams/subscriptions have lifecycle, cancellation, error, and close
  behavior.
- Platform channel and persistence changes have typed contracts and rollback.
- Widget, golden, integration, semantics, and analyzer evidence is run or
  explicitly deferred.

## Failure modes

- The Flutter specialist applies a generic pattern and misses framework-owned lifecycle, typing, permission, migration, cache, or deployment behavior.
- The worker mixes a new graph task with stale plan scope and creates a larger review surface than the MVP flow needs.
- The task closes with prose only: no source evidence, no scoped command or final-gate deferral, no rollback, and no next action for blockers.

## Output contract

- `status`: PASS, BLOCKED, PARTIAL, or DEFERRED.
- `slice`: widget, state, stream, route, channel, persistence, accessibility,
  golden, integration, or release behavior changed.
- `boundaries`: files touched by Flutter layer and why each belongs there.
- `stateAndAsync`: chosen state tool, rebuild scope, stream lifecycle, and
  error/cancellation decisions.
- `navigationChannelPersistence`: route contract, native channel contract,
  storage/offline/migration, and rollback notes.
- `accessibilityAndVisuals`: Semantics, text scale, focus, golden coverage, and
  platform visual risk.
- `tests`: focused commands run with exit code or explicit deferral reason.
- `confidence`: score with caps from missing analyzer, widget, golden,
  integration, platform, accessibility, or rollback proof.

## Guard rails

- DO NOT: introduce a second state-management framework inside one feature
  unless adapting legacy code at one named seam.
- DO NOT: call repositories, channels, or route navigation from reusable leaf
  widgets.
- DO NOT: hand-edit generated `*.g.dart`, `*.freezed.dart`, or router output.
- ALWAYS: dispose controllers/subscriptions and map platform failures to domain
  failures.
- ALWAYS: pair golden coverage with widget/semantics behavior tests.

## Verification

- Unit: Dart tests for domain/repository logic and state owner transitions.
- Widget: `flutter_test` for rendered states, interactions, semantics, and text
  scale.
- Golden: stable, reviewed screenshots for visual regression when layout risk
  exists.
- Integration: `integration_test` on at least one representative device when
  navigation, platform channels, permissions, or storage flows cross app seams.
- Static/build: `flutter analyze`, `dart format --set-exit-if-changed .`,
  `dart run build_runner build --delete-conflicting-outputs`, and flavor builds
  when policy allows.

- If any scoped check fails or new evidence appears, repair the smallest changed slice, rerun the same scoped command, and record command, exit code, pass/fail status, blockers, and final-gate deferrals before claiming completion.

## Supporting references

- examples/delivery.md - worked Flutter implementation example, anti-example, and verification fixture.
- domain-packs/flutter.md - Flutter practice pack with review matrix, rollback prompts, and MVP guard rails.
- evals/regression.json - Use when calibrating flutter-domain-delivery trigger boundaries, happy-path/failure-path coverage, boundary rollback behavior, or resource-tree regressions.

## Related

- `supervibe:source-driven-development`
- `supervibe:tdd`
- `supervibe:test-strategy`
- `supervibe:verification`
- `supervibe:code-review`
- `supervibe:stacks/flutter:flutter-developer`
- `supervibe:stacks/android:android-developer`
- `supervibe:stacks/ios:ios-developer`
## Supporting references

### Resource tree hardening

- `references/practice-pack.md` - Read when flutter-domain-delivery needs deeper load rules, local evidence anchors, gotchas, or a final checklist.
- `scripts/self-check.mjs` - Run with `--check` before claiming the flutter-domain-delivery resource tree is complete; add `--json` when machine-readable evidence is needed.
- `evals/regression.json` - Use when tuning flutter-domain-delivery trigger boundaries or checking should-trigger and should-not-trigger prompts.
- `examples/workflow.md` - Load when a concrete flutter-domain-delivery workflow example or anti-example would clarify the next action.
- `templates/output-contract.md` - Use when emitting agent-output so status, evidence, blockers, confidence, and nextAction stay consistent.

---
> Source: [vTRKA/supervibe](https://github.com/vTRKA/supervibe) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-15 -->
