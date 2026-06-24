---
name: lg-ssh-controller
description: Guides SSH command implementation for LG rig communication — connection setup, command execution, file transfer, error recovery, and multi-screen management via dartssh2. Use when this capability is needed.
metadata:
  author: ashishyesale7
---

# LG SSH Controller

Guides implementation of SSH-based communication between the Flutter controller app and the Liquid Galaxy rig. Every LG operation — KML display, camera control, rig management — flows through SSH.

**Announce:** "SSH Controller activated. Setting up secure communication with the LG rig."

## When to Invoke
- During `lg-logic-builder` when implementing the SSH service layer.
- When a student needs to add new SSH commands for rig interaction.
- When debugging SSH connection or command execution issues.

## Core SSH Commands

| Command | Purpose | Target | Dart Method |
|---------|---------|--------|-------------|
| `echo "flytoview=<LookAt>..." > /tmp/query.txt` | Fly camera to location | Master | `flyTo(lat, lon, alt, tilt, heading)` |
| `echo "search=..." > /tmp/query.txt` | Search for place | Master | `search(query)` |
| KML upload via SFTP | Display geographic data | Master | `sendKML(kmlString)` |
| `echo "..." > /var/www/html/kmls.txt` | Load KML files via NetworkLink | Master | `loadKML(filename)` |
| `echo "" > /var/www/html/kml/slave_<n>.kml` | Clear KML on slave screen | Slave N | `clearKML(slaveN)` |
| `echo '<ScreenOverlay>...' > /var/www/html/kml/slave_<n>.kml` | Send logo overlay to slave | Slave N | `sendLogo(slaveN, kml)` |
| `sshpass -p lg ssh lg@lg<n> "killall googleearth-bin; ..."` | Relaunch Google Earth | All | `relaunchGE()` |
| `sshpass -p lg ssh lg@lg<n> "sudo reboot"` | Reboot machine | All | `reboot()` |

## Connection Parameters

```dart
/// Default LG rig connection settings
class LGConnection {
  static const String defaultHost = '192.168.56.101';
  static const int defaultPort = 22;
  static const String defaultUser = 'lg';
  static const String defaultPassword = 'lg';
  static const int defaultScreens = 3;
}
```

**IMPORTANT**: These defaults are for the **LG Docker/VM test environment**. Real rig IPs vary. Always use `config.dart` or the Connection Settings screen to make these configurable.

## LG Rig Topology (3-screen default)

```
┌─────────┐  ┌─────────┐  ┌─────────┐
│  lg3    │  │  lg1    │  │  lg2    │
│  LEFT   │  │ MASTER  │  │  RIGHT  │
└─────────┘  └─────────┘  └─────────┘
```

- **lg1 (Master)**: Runs Google Earth primary instance. Receives commands via SSH.
- **lg2 (Right slave)**: Typically displays the app logo via ScreenOverlay.
- **lg3 (Left slave)**: Additional context/placemarks.
- **Slave KML paths**: `/var/www/html/kml/slave_<n>.kml` where n = screen number.

### Screen Number Calculation

```dart
/// Calculate right-most slave screen number for logo placement
int get rightMostSlave => (totalScreens ~/ 2) + 1;

/// Calculate left-most slave
int get leftMostSlave => totalScreens;

/// Calculate all slave numbers (excluding master = 1)
List<int> get slaveNumbers => List.generate(totalScreens - 1, (i) => i + 2);
```

## SSH Service Implementation Pattern

```dart
import 'package:dartssh2/dartssh2.dart';

class SSHService extends ChangeNotifier {
  SSHClient? _client;
  bool _connected = false;

  bool get connected => _connected;

  /// Connect to the LG master node
  Future<bool> connect({
    required String host,
    required int port,
    required String username,
    required String password,
  }) async {
    try {
      final socket = await SSHSocket.connect(
        host, port,
        timeout: const Duration(seconds: 10),
      );
      _client = SSHClient(
        socket,
        username: username,
        onPasswordRequest: () => password,
      );
      _connected = true;
      notifyListeners();
      return true;
    } catch (e) {
      _connected = false;
      notifyListeners();
      return false;
    }
  }

  /// Execute a command on the LG rig
  Future<String> execute(String command) async {
    if (_client == null) throw Exception('Not connected to LG rig');
    final result = await _client!.run(command);
    return utf8.decode(result);
  }

  /// Disconnect and clean up
  void disconnect() {
    _client?.close();
    _client = null;
    _connected = false;
    notifyListeners();
  }

  @override
  void dispose() {
    disconnect();
    super.dispose();
  }
}
```

## Error Recovery

| Failure | Recovery Strategy |
|---------|-------------------|
| Connection timeout | Auto-reconnect with exponential backoff (1s, 2s, 4s, max 16s) |
| Authentication failure | Show error + open Connection Settings screen |
| Command timeout | Retry once after 5s, then report error to UI |
| SFTP failure | Fallback to `echo '...' >` command for small payloads |
| Rig unreachable | Verify network, show "Check that rig is on the same WiFi" |

## ⛔️ Student Interaction — MANDATORY

**After implementing the SSH service, STOP and walk through with the student:**
1. Explain the connection lifecycle: connect → verify → execute → cleanup → disconnect.
2. Show what happens on the **rig side** when each command runs (file written, GE reads it).
3. Ask: *"What happens if the app is closed without calling disconnect()? Why do we need dispose()?"*
4. If the student cannot trace the SSH flow, link to **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md) — SSH & LG Communication topic.

**DO NOT move to KML commands** until the student understands the connection lifecycle.

## Reference Links

- **Lucia's SSHService implementation**: https://github.com/LiquidGalaxyLAB/LG-Master-Web-App
- **dartssh2 package**: https://pub.dev/packages/dartssh2
- **LG SSH command reference**: https://github.com/LiquidGalaxyLAB/liquid-galaxy/wiki
- **LG Rig setup scripts**: https://github.com/LiquidGalaxyLAB/liquid-galaxy
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## Handoff

After SSH service implementation → **lg-kml-writer** (.agent/skills/lg-kml-writer/SKILL.md) for KML generation, then **lg-code-reviewer** (.agent/skills/lg-code-reviewer/SKILL.md) for quality check.

## 🔗 Skill Chain

After the SSH service is implemented and the student understands the connection lifecycle, **automatically offer the next stage**:

> *"SSH communication is wired up! The app can now talk to the LG rig securely. Let's get this reviewed for error handling and resource cleanup. Ready for the Code Review? 🔍"*

If student says "ready" → activate `.agent/skills/lg-code-reviewer/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
