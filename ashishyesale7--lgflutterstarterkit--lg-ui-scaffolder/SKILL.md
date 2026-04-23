---
name: lg-ui-scaffolder
description: Generates Flutter screen and widget code from visualization designs. Produces controller-style UI that delegates all business logic to services while respecting strict layer boundaries.
metadata:
  author: ashishyesale7
---

# LG UI Scaffolder — Flutter Screen Generator

Produces phone-controller screens and reusable widgets from a visualization
design. Every generated screen follows the strict rule: **presentation code
reads state and dispatches actions — it never performs network calls, generates
KML, or opens SSH connections**.

**Announce:** "UI Scaffolder activated. Generating controller screens for the LG rig."

**⚠️ PROMINENT GUARDRAIL**: The **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md) and **LG Shield** (.agent/skills/lg-shield/SKILL.md) are active at all times. If the student can't trace a tap from UI to rig, STOP and invoke the Critical Advisor.

## When to Invoke
- After `lg-plan-writer` defines the screens and interactions.
- After `lg-viz-architect` maps phone actions to rig responses.
- When the student needs a new screen added to an existing app.

## Screen Generation Rules

### 1. Architecture Compliance
Every generated screen must:
- Use `context.watch<LGService>()` or `Consumer<LGService>` for state.
- Call service methods (`lgService.flyTo(...)`, `lgService.sendKML(...)`) for actions.
- **NEVER** import `dartssh2`, `dart:io`, or `package:http`.
- **NEVER** contain KML string literals or XML generation logic.
- **NEVER** call `http.get()` or open sockets.

### 2. Standard Screen Template

```dart
import 'package:flutter/material.dart';
import 'package:provider/provider.dart';
import '../services/lg_service.dart';

class {{ScreenName}} extends StatefulWidget {
  const {{ScreenName}}({super.key});

  @override
  State<{{ScreenName}}> createState() => _{{ScreenName}}State();
}

class _{{ScreenName}}State extends State<{{ScreenName}}> {
  bool _isLoading = false;

  Future<void> _onAction() async {
    setState(() => _isLoading = true);
    try {
      final lgService = context.read<LGService>();
      await lgService.someAction(); // Delegate to service
    } catch (e) {
      if (mounted) {
        ScaffoldMessenger.of(context).showSnackBar(
          SnackBar(content: Text('Error: $e')),
        );
      }
    } finally {
      if (mounted) setState(() => _isLoading = false);
    }
  }

  @override
  Widget build(BuildContext context) {
    final lgService = context.watch<LGService>();
    // Build UI based on lgService state...
  }
}
```

### 3. Widget Extraction
Extract into `lib/widgets/` when:
- A UI element appears on 2+ screens.
- A section exceeds ~80 lines of build code.
- The element has its own interaction logic (e.g., `ConnectionCard`, `PlacemarkList`).

### 4. Screen Types for LG Apps

| Screen Type | Purpose | Key Widgets |
|-------------|---------|-------------|
| **Connection Screen** | Enter LG rig IP/port/credentials | TextFormField, ElevatedButton |
| **Dashboard Screen** | Quick actions: FlyTo, Orbit, Send KML | GridView of ActionCards |
| **Data List Screen** | Browse API data (earthquakes, POIs) | ListView.builder with ListTile |
| **Detail Screen** | Show single item, "Visualize on LG" button | Card, FlyTo + Balloon actions |
| **Settings Screen** | Rig config, screen count, defaults | Form with SharedPreferences |
| **Tour Screen** | Compose and play KML tours | ReorderableListView, Play/Stop |

### 5. Responsive Layout
- Use `LayoutBuilder` for tablet vs phone layouts.
- Phone: single-column with bottom navigation.
- Tablet: two-pane layout (list + detail side-by-side).
- Always support dark mode (LG demos are in dark rooms).
- Minimum touch target: 48×48 dp.

## Generation Workflow

### Step 1 — Screen Inventory
Read the plan from `docs/plans/` and list all required screens:

```
Screen 1: ConnectionScreen  — LG rig connection
Screen 2: DashboardScreen   — Quick action grid
Screen 3: EarthquakeListScreen — USGS data browser
Screen 4: EarthquakeDetailScreen — Single quake detail + visualize
```

### Step 2 — Generate Screens
For each screen:
1. Create file in `lib/screens/`.
2. Wire up provider reads for state.
3. Add action handlers that delegate to services.
4. Add loading states and error handling.
5. Register route in `MaterialApp.routes` in `main.dart`.

### Step 3 — Generate Shared Widgets
For reusable elements:
1. Create file in `lib/widgets/`.
2. Keep widgets stateless where possible.
3. Accept callbacks (`onTap`, `onAction`) — no direct service calls in widgets.

### Step 4 — Verify Compliance
After generation, run the boundary check:
- `grep -rn "import.*dartssh2" lib/screens/ lib/widgets/` → must return nothing.
- `grep -rn "http.get\|HttpClient" lib/screens/ lib/widgets/` → must return nothing.
- `grep -rn "kml\|KML\|<Placemark" lib/screens/ lib/widgets/` → must return nothing (unless displaying KML status text).
- `flutter analyze` → zero errors.

## Output
For each screen generated:
- Dart file in `lib/screens/`
- Route entry in `main.dart`
- Any extracted widgets in `lib/widgets/`
- Entry in `docs/learning-journal.md` explaining the screen's purpose

## ⛔️ Student Interaction — MANDATORY

**After generating each screen, STOP and explain:**
1. How the screen reads state from Provider (watch vs read).
2. Why NO business logic exists in the screen — it only delegates to services.
3. Show the corresponding entry in `docs/learning-journal.md`.
4. Ask: *"What happens when you tap [action] on this screen? Trace the call from UI to rig."*
5. If the student cannot trace the flow, link to **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md).

**DO NOT auto-generate the next screen** until the student confirms understanding.

## Reference Links

- **Lucia's screen structure**: https://github.com/LiquidGalaxyLAB/LG-Master-Web-App (splash, main, connection, settings, help)
- **Flutter layout guide**: https://docs.flutter.dev/ui/layout
- **Material Design 3**: https://m3.material.io/
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## Handoff
Passes generated screens to `lg-code-reviewer` for quality review and
`lg-shield` for boundary validation.

## 🔗 Skill Chain

After screens are generated and the student passes the checkpoint, **automatically offer the next stage**:

> *"Screens are scaffolded and you can trace the UI-to-service flow! Now let's execute the full implementation plan in batches. Ready to start coding the features? ⚙️"*

If student says "ready" → activate `.agent/skills/lg-exec/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
