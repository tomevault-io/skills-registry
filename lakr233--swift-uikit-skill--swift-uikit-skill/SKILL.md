---
name: swift-uikit-skill
description: Build, review, and improve programmatic UIKit applications following proven patterns for Auto Layout, table views, collection views, navigation, animation, Core Data, networking, and architecture. Use when writing new UIKit features, refactoring existing view controllers, reviewing UIKit code quality, building custom controls, or implementing common iOS design patterns like load-and-retry, onboarding, or collapsible headers. Use when this capability is needed.
metadata:
  author: Lakr233
---

# Swift UIKit Skill

## Operating Rules

- All UI is built **programmatically** (no Storyboards). Always set `translatesAutoresizingMaskIntoConstraints = false`.
- Prefer `NSLayoutConstraint.activate([...])` for batch constraint activation.
- Organize view controller code with extensions: separate `style()`, `layout()`, and protocol conformances.
- Use factory functions for repetitive UI element creation.
- Use `weak` references for delegates and `[weak self]` in closures to prevent retain cycles.
- Prefer `Result<Success, Error>` for async completion handlers.
- Always dispatch UI updates to the main thread from async callbacks.
- Extract custom views flush to their container; parent manages spacing.
- Use child view controllers for complex, self-contained screen sections.
- Consult relevant reference files before implementing any topic.

## Task Workflow

### Review existing UIKit code

- Read the code under review and identify which topics apply
- Run the Topic Router below for each relevant topic
- Check for retain cycles (missing `weak` delegates, missing `[weak self]`)
- Verify Auto Layout completeness (unambiguous constraints, no warnings)
- Flag deprecated patterns (e.g., manual `beginUpdates`/`endUpdates` when Diffable is available)

### Improve existing UIKit code

- Audit current implementation against the Topic Router topics
- Extract large view controllers into child VCs and custom views
- Replace inline UI creation with factory functions
- Migrate legacy table/collection view data sources to Diffable where appropriate
- Add proper error handling with `Result` types

### Implement new UIKit feature

- Design data flow first: identify service, view model, view controller, and view layers
- Structure views for composition (extracted views + child view controllers)
- Apply correct animation patterns (constraint-based, Core Animation, or UIView)
- Use protocol-delegate for sustained relationships; closures for one-shot callbacks
- Add proper loading/error states

## Topic Router

Consult the reference file for each topic relevant to the current task:

| Topic                  | Reference                              |
| ---------------------- | -------------------------------------- |
| UITableView            | `references/uitableview.md`            |
| UICollectionView       | `references/uicollectionview.md`       |
| UINavigationController | `references/uinavigationcontroller.md` |
| UIScrollView           | `references/uiscrollview.md`           |
| Core Animation         | `references/core-animation.md`         |
| Core Graphics          | `references/core-graphics.md`          |
| Auto Layout animation  | `references/auto-layout-animation.md`  |
| Communication patterns | `references/communication-patterns.md` |
| Architecture patterns  | `references/architecture-patterns.md`  |
| View extraction        | `references/view-extraction.md`        |
| Navigation patterns    | `references/navigation-patterns.md`    |
| Design patterns (UI)   | `references/design-patterns.md`        |
| Networking             | `references/networking.md`             |
| Factory functions      | `references/factory-patterns.md`       |
| Nib / XIB patterns     | `references/nib-patterns.md`           |
| NSAttributedString     | `references/nsattributedstring.md`     |
| Gesture recognizers    | `references/gesture-recognizers.md`    |
| Custom controls        | `references/custom-controls.md`        |
| Currency formatting    | `references/currency-formatting.md`    |
| Deep linking           | `references/deep-linking.md`           |

## Correctness Checklist

These are hard rules -- violations are always bugs:

- [ ] Every programmatic view sets `translatesAutoresizingMaskIntoConstraints = false`
- [ ] Delegates are declared `weak var delegate: SomeDelegate?`
- [ ] Async closures capture `[weak self]` when referencing the owning object
- [ ] UI updates from async callbacks are dispatched to `DispatchQueue.main`
- [ ] `beginUpdates()` / `endUpdates()` bracket all batch table view mutations
- [ ] Diffable data source items conform to `Hashable` with stable identifiers
- [ ] Child view controllers follow the 3-step lifecycle: `addChild()`, `addSubview()`, `didMove(toParent:)`
- [ ] Custom views define `intrinsicContentSize` when they have a natural size
- [ ] `NSFetchedResultsController` delegate implements all three callbacks (`willChange`, `didChange`, `didChangeContent`)
- [ ] Core Animation model layer is updated after animation to persist final state
- [ ] `NSLayoutConstraint.activate()` is used instead of `isActive = true` one-by-one
- [ ] Factory functions return views with `translatesAutoresizingMaskIntoConstraints` already set to `false`

## Quick Diagnostics

| Symptom                        | Likely cause                                                | Reference                              |
| ------------------------------ | ----------------------------------------------------------- | -------------------------------------- |
| View doesn't appear            | Missing `translatesAutoresizingMaskIntoConstraints = false` | `references/view-extraction.md`        |
| Table view crashes on insert   | Data source count mismatch with `beginUpdates`              | `references/uitableview.md`            |
| Animation snaps to final state | Model layer not updated after CA animation                  | `references/core-animation.md`         |
| Retain cycle / memory leak     | Missing `weak` on delegate or `[weak self]`                 | `references/communication-patterns.md` |
| Scroll view doesn't scroll     | Missing content size or broken constraint chain             | `references/uiscrollview.md`           |
| Shadow clipped by view         | `masksToBounds = true` on layer                             | `references/core-animation.md`         |
| Gradient/shadow wrong size     | Set bounds-dependent layers in `viewDidAppear`              | `references/core-animation.md`         |
| Collection view empty          | Forgot to register cell or data source nil                  | `references/uicollectionview.md`       |
| Core Data threading crash      | Accessing managed object on wrong queue                     | `references/networking.md`             |
| Navigation bar title missing   | Not setting `title` or `navigationItem.title`               | `references/uinavigationcontroller.md` |

---
> Source: [Lakr233/Swift-UIKit-Skill](https://github.com/Lakr233/Swift-UIKit-Skill) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-17 -->
