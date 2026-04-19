---
name: flutter-feature-based-clean-architecture
description: Standards for organizing code by feature at the root level to improve scalability and maintainability. Use when this capability is needed.
metadata:
  author: tuanvo3423
---

# Feature-Based Clean Architecture

## **Priority: P0 (CRITICAL)**

Standard for modular Clean Architecture organized by business features in `lib/src/features/`.

## Structure

```text
lib/
└── src/
    ├── core/ # Shared infrastructure & utilities
    ├── shared/ # Common UI components & shared entities
    └── features/
        └── <feature_name>/
            ├── domain/ # Business Logic (Pure Dart): entities, interfaces, use_cases
            ├── data/ # Implementation: data_sources, dtos, repositories
            └── presentation/ # UI & State: blocs, pages, widgets
```

## Implementation Guidelines

- **Feature Encapsulation**: Keep logic, models, and UI internal to the feature directory.
- **Strict Layering**: Maintain 3-layer separation (Domain/Data/Presentation) within each feature.
- **Dependency Rule**: `Presentation -> Domain <- Data`. Domain must have zero external dependencies.
- **Cross-Feature Communication**: Features only depend on the **Domain** layer of other features.
- **Flat features**: Keep `lib/src/features/` flat; avoid nested features.
- **No DTO Leakage**: Never expose DTOs or Data Sources to UI or other features; return Domain Entities.
- **Shared logic**: Move cross-cutting concerns to `lib/src/shared/` or `lib/src/core/`.

## Presentation: Component Extraction (P0)

Any **visually meaningful UI block** must be extracted into `presentation/components/`.

### What counts as “visually meaningful”

- A block users can visually identify as a unit (Header, SearchBar, Card, Section, BottomSheet content).
- A repeated layout (e.g., list item / card appears 2+ times).
- A block with its own spacing/background/border/gradient.
- A block that would make the page widget harder to scan if kept inline.

### Folder convention

```text
lib/src/features/<feature_name>/presentation/
├── pages/
│   └── <feature>_page.dart
├── components/
│   ├── <feature>_header.dart
│   ├── <feature>_search_bar.dart
│   ├── <feature>_card.dart
│   └── ...
└── models/ (optional; presentation-only models)
    └── ...
```

### Example: Activity Logs

**Page** (only orchestrates layout, scroll, state):

```dart
class ActivityLogsPage extends StatelessWidget {
  @override
  Widget build(BuildContext context) {
    return Column(
      children: [
        const ActivityLogsHeader(),
        const ActivityLogsSearchBar(),
        Expanded(child: ActivityLogsList(entries: entries)),
      ],
    );
  }
}
```

**Components** (each UI block lives in its own file):

- `components/activity_logs_header.dart`
- `components/activity_logs_search_bar.dart`
- `components/activity_log_card.dart`
- `components/activity_logs_list.dart`

### Example: Login screen

Break into:

- `components/login_header.dart` (logo + title)
- `components/login_form.dart` (email/password fields + CTA)
- `components/login_footer.dart` (forgot password / terms links)

## Reference & Examples

For feature folder blueprints and cross-layer dependency templates:
See [references/REFERENCE.md](references/REFERENCE.md).

## Related Topics

layer-based-clean-architecture | retrofit-networking | go-router-navigation | bloc-state-management | dependency-injection

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tuanvo3423) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
