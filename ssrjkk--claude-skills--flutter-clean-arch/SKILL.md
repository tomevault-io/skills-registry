---
name: flutter-clean-arch
description: Creates Flutter applications with Clean Architecture and layer separation. Use for scalable mobile applications.
metadata:
  author: ssrjkk
---
# Flutter Clean Architecture

> Flutter apps with Clean Architecture separation of concerns.

## 🚀 Quick Start
```dart
// domain/entities/user.dart
class User {
    final String id;
    final String name;
    User(this.id, this.name);
}

// presentation/pages/user_page.dart
class UserPage extends StatelessWidget {
    @override
    Widget build(BuildContext context) {
        return Scaffold(body: Center(child: Text('User Page')));
    }
}
```

## 📋 When to Use
- ✅ Scalable Flutter applications
- ✅ Need clear layer separation (domain, data, presentation)
- ❌ Not for simple prototypes

## 🔧 Step-by-Step Instructions
1. Create Flutter project: `flutter create my_app`
2. Organize folders: `domain/`, `data/`, `presentation/`
3. Define entities and use cases
4. Run: `flutter run`

## 📦 Dependencies
```bash
flutter create my_app
```

## 🧪 Examples
Input: Navigation to UserPage → Output: User page displayed

## 🔗 Resources
- [Flutter Docs](https://flutter.dev/docs)
- [Examples](./examples/)

## ✅ Validation
1. Project compiles without errors
2. Layers properly isolated
3. Navigation works between pages

---
> Source: [ssrjkk/claude-skills](https://github.com/ssrjkk/claude-skills) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-16 -->
