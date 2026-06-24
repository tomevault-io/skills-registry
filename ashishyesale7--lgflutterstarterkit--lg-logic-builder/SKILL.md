---
name: lg-logic-builder
description: Designs and implements business logic for LG Flutter controller apps. Covers state management (Provider/Riverpod), SSH communication, KML data pipelines, and the Controller-to-Rig architecture pattern. Use when this capability is needed.
metadata:
  author: ashishyesale7
---

# Liquid Galaxy Logic Builder

## Overview

This skill designs and implements the core business logic layer for Liquid Galaxy Flutter controller applications. It ensures proper separation between UI, state, and data layers following the Controller-to-Rig architecture pattern.

**Announce at start:** "I'm using the lg-logic-builder skill to implement [Feature Logic]."

## Architecture Pattern

### The Controller-to-Rig Model (Primary)

```text
┌─────────────────┐     ┌──────────────┐     ┌─────────────────┐
│  Flutter App     │     │  LG Master   │     │  Google Earth   │
│  (Smartphone)    │────>│  (Linux PC)  │────>│  (All Screens)  │
│  The Controller  │ SSH │  Receives    │     │  Renders KML    │
│  Sends Commands  │     │  KML + Cmds  │     │  Across Rig     │
└─────────────────┘     └──────────────┘     └─────────────────┘
```

**Rules:**
1. **Flutter app is the controller** — user interacts with the phone, app generates KML and commands.
2. **SSH sends data to LG master** — the master machine distributes to all screens.
3. **Google Earth renders everything** — KML placemarks, tours, overlays appear across all screens automatically.
4. **No multi-screen Flutter rendering** — the phone shows a single-screen control UI. Google Earth handles the panoramic display.

### Data Pipeline

```text
User Input → Service Layer → API Fetch → Data Model → KML Generation → SSH Send → LG Display
```

For apps that fetch and visualize external data:
1. **User selects data category** — earthquakes, satellites, volcanoes, etc.
2. **Service fetches from API** — USGS, NASA, OpenWeather, etc.
3. **Data converted to models** — Dart classes with lat/lon/metadata.
4. **KML generated from models** — Placemarks, tours, balloons.
5. **SSH sends KML to rig** — Google Earth renders on all screens.

## State Management Patterns

### Pattern 1: Provider (Recommended for LG apps)

```dart
// main.dart — Register all providers
void main() {
  runApp(
    MultiProvider(
      providers: [
        ChangeNotifierProvider(create: (_) => LGService()),
        ChangeNotifierProvider(create: (_) => DataService()),
      ],
      child: const LGControllerApp(),
    ),
  );
}
```

### Service Interaction Rules

```dart
// CORRECT: Service calls service
class DataService with ChangeNotifier {
  final LGService _lgService;

  DataService(this._lgService);

  Future<void> visualizeData() async {
    final data = await _fetchFromApi();
    final kml = _generateKml(data);
    await _lgService.sendKml(kml);
    notifyListeners();
  }
}

// WRONG: UI calls multiple services directly
// Don't do this in the widget:
//   final data = await apiService.fetch();
//   final kml = kmlService.generate(data);
//   await sshService.send(kml);
// Instead, encapsulate this pipeline in a single service method.
```

### Pattern 2: State Machine for App Flow

```dart
enum AppState {
  disconnected,  // No LG connection
  connecting,    // SSH handshake in progress
  connected,     // Connected to LG rig
  loading,       // Fetching data from API
  ready,         // Data loaded, ready to visualize
  visualizing,   // KML sent, showing on LG
  touring,       // Playing a KML tour
  error,         // Something went wrong
}

class AppStateService with ChangeNotifier {
  AppState _state = AppState.disconnected;
  AppState get state => _state;
  String? _errorMessage;
  String? get errorMessage => _errorMessage;

  void transition(AppState newState, {String? error}) {
    _state = newState;
    _errorMessage = error;
    notifyListeners();
  }

  bool get canVisualize => _state == AppState.ready;
  bool get isConnected => _state != AppState.disconnected && _state != AppState.connecting;
}
```

## SSH Communication Logic

### Connection Lifecycle

```dart
/// Standard SSH connection lifecycle for LG apps
class SSHLifecycle {
  /// 1. Connect — establish SSH tunnel to LG master
  /// 2. Verify — confirm it's an LG rig (check for lg-specific files)
  /// 3. Send — push KML/commands as needed
  /// 4. Cleanup — clear KML overlays before disconnect
  /// 5. Disconnect — close SSH tunnel, dispose resources

  static Future<void> cleanDisconnect(SSHService ssh) async {
    // Always clean up before disconnecting
    await ssh.clearKML();
    ssh.disconnect();
  }
}
```

### KML Send Patterns

```dart
/// Send KML to specific rig locations
class KMLSendPatterns {
  /// Master screen overlay: /var/www/html/kml/slave_0.kml
  /// Left slave: /var/www/html/kml/slave_2.kml
  /// Right slave: /var/www/html/kml/slave_3.kml
  /// Camera control: echo "flytoview=..." > /tmp/query.txt
  /// Tour control: echo "playtour=TourName" > /tmp/query.txt
}
```

## Navigation Logic

### App Navigation Pattern for LG Apps

```dart
/// Standard LG app navigation structure
class AppRoutes {
  static const String splash = '/';
  static const String home = '/home';
  static const String connection = '/connection';
  static const String settings = '/settings';
  static const String visualization = '/visualization';
  static const String about = '/about';

  static Map<String, WidgetBuilder> get routes => {
    splash: (_) => const SplashScreen(),
    home: (_) => const HomeScreen(),
    connection: (_) => const ConnectionScreen(),
    settings: (_) => const SettingsScreen(),
    visualization: (_) => const VisualizationScreen(),
    about: (_) => const AboutScreen(),
  };
}

/// Standard LG app drawer items
List<DrawerItem> get standardDrawerItems => [
  DrawerItem(icon: Icons.home, title: 'Home', route: AppRoutes.home),
  DrawerItem(icon: Icons.wifi, title: 'Connect to LG', route: AppRoutes.connection),
  DrawerItem(icon: Icons.map, title: 'Visualize', route: AppRoutes.visualization),
  DrawerItem(icon: Icons.settings, title: 'Settings', route: AppRoutes.settings),
  DrawerItem(icon: Icons.info, title: 'About', route: AppRoutes.about),
];
```

## Connection Logic

```dart
/// Standard connection flow for LG apps
class ConnectionLogic {
  /// Step 1: Validate inputs
  static bool validateConnection(String host, String port, String user, String pass) {
    if (host.isEmpty) return false;
    if (int.tryParse(port) == null) return false;
    if (user.isEmpty || pass.isEmpty) return false;
    return true;
  }

  /// Step 2: Test SSH connection
  static Future<bool> testConnection(SSHService ssh) async {
    try {
      final connected = await ssh.connect();
      if (!connected) return false;

      // Verify it's actually an LG rig
      final result = await ssh.execute('echo "lg_ready"');
      return result.contains('lg_ready');
    } catch (_) {
      return false;
    }
  }

  /// Step 3: Get rig info
  static Future<Map<String, dynamic>> getRigInfo(SSHService ssh) async {
    final screens = await ssh.execute('cat /etc/lg-screens 2>/dev/null || echo "3"');
    return {
      'screens': int.tryParse(screens.trim()) ?? 3,
    };
  }
}
```

## Logic Implementation Checklist

- [ ] State management pattern chosen and justified
- [ ] Services follow single-responsibility principle
- [ ] No business logic in widgets
- [ ] Error states handled for every async operation
- [ ] SSH connection lifecycle managed (connect/verify/cleanup/disconnect)
- [ ] KML cleanup called before sending new data
- [ ] Dispose methods clean up all resources
- [ ] `flutter analyze` passes with zero issues

## Guardrail

If the student proposes putting SSH calls or KML generation directly in a widget, **STOP** and activate the **Critical Advisor** (.agent/skills/lg-critical-advisor/SKILL.md). This is a layer boundary violation per `.agent/rules/layer-boundaries.md`.

## ⛔️ Student Interaction — MANDATORY

**After implementing each logic component, STOP and walk through it with the student:**
1. Explain the state management pattern chosen and *why* it was chosen.
2. Trace the data flow: UI event → Provider → Service → SSH/KML → Rig.
3. Ask: *"If connection drops during this operation, what happens? How would you handle reconnection?"*
4. If the student cannot reason about error recovery, link to **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md) for SSH and State Management topics.

**DO NOT move to the next service/provider** until the student confirms understanding of the current one.

## Reference Links

- **Lucia's lg_service.dart (SSH patterns)**: https://github.com/LiquidGalaxyLAB/LG-Master-Web-App
- **Flutter Provider docs**: https://pub.dev/packages/provider
- **Dart concurrency**: https://dart.dev/language/concurrency
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## Handoff

After logic design, use **lg-file-generator** (.agent/skills/lg-file-generator/SKILL.md) to create the files, then **lg-exec** (.agent/skills/lg-exec/SKILL.md) for implementation.

## 🔗 Skill Chain

After the logic layer is designed and the student understands the state management and data flow patterns, **automatically offer the next stage**:

> *"Logic layer is designed! State management, SSH lifecycle, and data pipeline are all mapped out. Let's generate the files and start building. Ready? 📁"*

If student wants file generation → activate `.agent/skills/lg-file-generator/SKILL.md`.
If student wants to jump to execution → activate `.agent/skills/lg-exec/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
