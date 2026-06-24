---
name: flutter-desktop
description: Builds desktop applications with Flutter for Windows, macOS, and Linux from a single codebase.
metadata:
  author: ssrjkk
---
# Flutter Desktop
> Build native desktop apps for Windows, macOS, and Linux.
## Quick Start
```bash
flutter create --platforms=windows,macos,linux my_desktop_app
cd my_desktop_app && flutter run -d windows
```
## Window Management
```dart
import 'package:window_manager/window_manager'
void main() async {
  WidgetsFlutterBinding.ensureInitialized(); await windowManager.ensureInitialized()
  await windowManager.setSize(const Size(1280, 800)); await windowManager.setTitle('My App')
  runApp(MyApp())
}
```
## When to Use
- Cross-platform desktop apps; Material Design desktop; Existing Flutter mobile to desktop
## Validation
1. App builds for all platforms; 2. Window size settings apply; 3. Platform channels work

---
> Source: [ssrjkk/claude-skills](https://github.com/ssrjkk/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
