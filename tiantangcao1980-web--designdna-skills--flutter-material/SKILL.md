---
name: flutter-material
description: Flutter Material 3 — Google's cross-platform UI toolkit with built-in Material 3 components (`package:flutter/material`, bundled with Flutter SDK). Recommended path since `material-components-flutter` was archived in 2023. Covers widget catalog, ThemeData tokens, dynamic color, and Material 3 migration notes. Use when this capability is needed.
metadata:
  author: tiantangcao1980-web
---

# Flutter Material 3 — SDK Built-in UI

> **Source**: [flutter/flutter](https://github.com/flutter/flutter) · Material bundled in SDK
> **Note**: External [material-components-flutter](https://github.com/material-components/material-components-flutter) is **archived 2023-11**; use SDK-built-in package instead
> **Docs**: https://docs.flutter.dev/ui/widgets/material

## 1. When to use

- Cross-platform apps (iOS + Android + Web + Desktop) via Flutter
- Want **Material Design 3** out of the box with zero extra deps
- Want dynamic color (Material You) on Android 12+

## 2. Quick start

Every Flutter project already has Material. No install needed.

```dart
import 'package:flutter/material.dart';

void main() => runApp(const MyApp());

class MyApp extends StatelessWidget {
  const MyApp({super.key});

  @override
  Widget build(BuildContext context) {
    return MaterialApp(
      title: 'My App',
      theme: ThemeData(
        useMaterial3: true,
        colorScheme: ColorScheme.fromSeed(seedColor: Colors.deepPurple),
      ),
      home: const HomeScreen(),
    );
  }
}
```

## 3. Core widgets

**Layout**: `Container` · `Padding` · `Row` · `Column` · `Stack` · `Wrap` · `Flex` · `Flexible` · `Expanded` · `SafeArea` · `Scaffold`

**Navigation**: `AppBar` · `BottomNavigationBar` · `NavigationBar` (M3) · `NavigationRail` · `NavigationDrawer` · `Drawer` · `TabBar` · `TabBarView`

**Input**: `TextField` · `TextFormField` · `Checkbox` · `Radio` · `Switch` · `Slider` · `DropdownButton` · `DatePicker` · `TimePicker`

**Display**: `Text` · `Icon` · `Image` · `CircleAvatar` · `Chip` · `Badge` · `Divider` · `ListTile`

**Feedback**: `CircularProgressIndicator` · `LinearProgressIndicator` · `SnackBar` · `AlertDialog` · `BottomSheet` · `Tooltip`

**Buttons (M3)**: `FilledButton` · `FilledButton.tonal` · `ElevatedButton` · `OutlinedButton` · `TextButton` · `IconButton` · `FloatingActionButton`

**Containers (M3)**: `Card` · `Chip` (Input/Filter/Choice/Action) · `Dialog` · `Banner`

## 4. Usage

### Scaffold + AppBar

```dart
Scaffold(
  appBar: AppBar(
    title: const Text('Home'),
    actions: [
      IconButton(icon: const Icon(Icons.search), onPressed: () {}),
      IconButton(icon: const Icon(Icons.more_vert), onPressed: () {}),
    ],
  ),
  body: const Center(child: Text('Welcome')),
  floatingActionButton: FloatingActionButton(
    onPressed: () {},
    child: const Icon(Icons.add),
  ),
)
```

### Buttons (M3)

```dart
FilledButton(onPressed: save, child: const Text('Save'))
FilledButton.tonal(onPressed: cancel, child: const Text('Cancel'))
ElevatedButton(onPressed: () {}, child: const Text('Elevated'))
OutlinedButton(onPressed: () {}, child: const Text('Outlined'))
TextButton(onPressed: () {}, child: const Text('Text'))
IconButton(icon: const Icon(Icons.delete), onPressed: () {})
```

### Form

```dart
final _formKey = GlobalKey<FormState>();

Form(
  key: _formKey,
  child: Column(
    children: [
      TextFormField(
        decoration: const InputDecoration(labelText: 'Email'),
        validator: (v) => (v ?? '').contains('@') ? null : 'Invalid email',
      ),
      TextFormField(
        decoration: const InputDecoration(labelText: 'Password'),
        obscureText: true,
        validator: (v) => (v ?? '').length >= 6 ? null : 'Min 6 chars',
      ),
      FilledButton(
        onPressed: () {
          if (_formKey.currentState!.validate()) {
            // submit
          }
        },
        child: const Text('Sign in'),
      ),
    ],
  ),
)
```

### ListTile

```dart
ListTile(
  leading: const CircleAvatar(child: Icon(Icons.person)),
  title: const Text('Alice'),
  subtitle: const Text('alice@example.com'),
  trailing: const Icon(Icons.chevron_right),
  onTap: () => Navigator.push(...),
)
```

### Dialog

```dart
showDialog<bool>(
  context: context,
  builder: (ctx) => AlertDialog(
    title: const Text('Delete?'),
    content: const Text('This action cannot be undone.'),
    actions: [
      TextButton(onPressed: () => Navigator.pop(ctx, false), child: const Text('Cancel')),
      FilledButton(onPressed: () => Navigator.pop(ctx, true), child: const Text('Delete')),
    ],
  ),
);
```

### SnackBar

```dart
ScaffoldMessenger.of(context).showSnackBar(
  const SnackBar(
    content: Text('Saved'),
    behavior: SnackBarBehavior.floating,
  ),
);
```

### Bottom sheet

```dart
showModalBottomSheet(
  context: context,
  builder: (ctx) => Column(
    mainAxisSize: MainAxisSize.min,
    children: [
      ListTile(leading: Icon(Icons.edit), title: Text('Edit'), onTap: () {}),
      ListTile(leading: Icon(Icons.delete), title: Text('Delete'), onTap: () {}),
    ],
  ),
);
```

## 5. Theme (Material 3)

```dart
MaterialApp(
  theme: ThemeData(
    useMaterial3: true,  // REQUIRED for M3

    // Generate a full palette from one seed
    colorScheme: ColorScheme.fromSeed(
      seedColor: const Color(0xFFfa2c19),
      brightness: Brightness.light,
    ),

    // Typography
    textTheme: GoogleFonts.interTextTheme(),

    // Component-level overrides
    filledButtonTheme: FilledButtonThemeData(
      style: FilledButton.styleFrom(
        shape: RoundedRectangleBorder(borderRadius: BorderRadius.circular(8)),
      ),
    ),
  ),
  darkTheme: ThemeData(
    useMaterial3: true,
    colorScheme: ColorScheme.fromSeed(
      seedColor: const Color(0xFFfa2c19),
      brightness: Brightness.dark,
    ),
  ),
  themeMode: ThemeMode.system,
)
```

### Dynamic color (Material You, Android 12+)

```bash
flutter pub add dynamic_color
```

```dart
import 'package:dynamic_color/dynamic_color.dart';

DynamicColorBuilder(
  builder: (light, dark) {
    return MaterialApp(
      theme: ThemeData.from(colorScheme: light ?? ColorScheme.fromSeed(seedColor: Colors.purple)),
      darkTheme: ThemeData.from(colorScheme: dark ?? ColorScheme.fromSeed(seedColor: Colors.purple, brightness: Brightness.dark)),
    );
  },
)
```

## 6. M2 → M3 migration

- Set `useMaterial3: true` in ThemeData
- Replace `primarySwatch` with `colorScheme: ColorScheme.fromSeed(...)`
- `BottomNavigationBar` → `NavigationBar` (M3 default)
- `Drawer` → `NavigationDrawer` (M3)
- `RaisedButton` → `ElevatedButton` (already deprecated in M2)

## 7. Platform adaptation

Flutter Material mimics Material on both iOS and Android. For **Apple-native look on iOS**, use Cupertino widgets:

```dart
import 'package:flutter/cupertino.dart';

CupertinoButton(onPressed: () {}, child: const Text('iOS style'))
```

Or mix via `Platform.isIOS`:

```dart
Theme.of(context).platform == TargetPlatform.iOS
  ? CupertinoButton(...)
  : ElevatedButton(...)
```

## 8. BANNED

- ❌ NEVER use external `material-components-flutter` — archived 2023. Use SDK built-in.
- ❌ NEVER start new projects with `useMaterial3: false` (defaults to M3 anyway in recent Flutter)
- ❌ NEVER use `primaryColor` / `accentColor` — use `colorScheme.primary` / `colorScheme.secondary`
- ❌ NEVER use deprecated M2 widgets (RaisedButton, FlatButton) — they've been removed
- ❌ NEVER hardcode colors in widgets — always `Theme.of(context).colorScheme.primary`
- ❌ NEVER forget `MaterialApp` at root — Material widgets will throw without it
- ❌ NEVER use `print` for errors in production — use `debugPrint` or logging
- ❌ NEVER mix Material + Cupertino without platform check

## 9. Pre-flight checklist

```
- [ ] Flutter SDK 3.19+ (M3 is default)
- [ ] MaterialApp wraps everything
- [ ] useMaterial3: true in ThemeData
- [ ] ColorScheme.fromSeed for brand
- [ ] GoogleFonts or asset-based font in textTheme
- [ ] Dark theme configured
- [ ] themeMode set (system | light | dark)
- [ ] Platform adaptation (Cupertino for iOS if needed)
- [ ] SafeArea used around full-screen content
- [ ] Hero / shared element transitions where appropriate
```

## 10. Dial fit

formality: 6-7 · motion: 6-7 (M3 has richer motion) · density: 5 · warmth: 5-7 (M3 dynamic) · contrast: 6

## 11. Alternatives (on Flutter)

| Need | Alternative |
|---|---|
| Tencent design language | [TDesign Flutter](https://github.com/Tencent/tdesign-flutter) — see skill |
| iOS-native look | Cupertino widgets |
| Highly customized design system | Build on top of Material with custom theme + override widgets |

---
> Source: [tiantangcao1980-web/DesignDNA-Skills](https://github.com/tiantangcao1980-web/DesignDNA-Skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
