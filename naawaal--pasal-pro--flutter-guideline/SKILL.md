---
name: flutter-guideline
description: Enforces Flutter code quality standards including file size limits, SOLID principles, naming conventions, and best practices. Use this as a reference when generating or reviewing Flutter code. Use when this capability is needed.
metadata:
  author: naawaal
---

# 📏 Flutter Code Quality Guidelines (Short Version)

**Quick reference for production-grade Flutter code standards.**

---

## 🎯 File Size Limits

```yaml
Hard Limits:
  - Dart files: 300 lines max
  - Widget files: 250 lines max
  - Functions: 50 lines max
  - Build methods: 30 lines max
  - Test files: 400 lines max

Refactor when:
  - Approaching 250 lines
  - Function >40 lines
  - >3 levels nesting
```

---

## 🏗️ SOLID Principles (Quick Reference)

| Principle                 | Rule                                        | Example                                            |
| ------------------------- | ------------------------------------------- | -------------------------------------------------- |
| **Single Responsibility** | One class = one reason to change            | User class only holds data, not auth/validation    |
| **Open/Closed**           | Open for extension, closed for modification | Use interfaces, not if/else chains                 |
| **Liskov Substitution**   | Subtypes must replace base types            | Square shouldn't extend Rectangle                  |
| **Interface Segregation** | No fat interfaces                           | Split Worker into Workable, Payable, etc.          |
| **Dependency Inversion**  | Depend on abstractions                      | Use ProductRepository interface, not DriftDatabase |

---

## 🏷️ Naming Conventions

```dart
// Files & Folders
user_profile_page.dart          // ✅ snake_case
UserProfilePage.dart            // ❌ Wrong case

// Classes
class UserProfilePage           // ✅ PascalCase
class userProfile               // ❌ Wrong case

// Variables
final userName = 'John';        // ✅ camelCase
bool isLoading = true;          // ✅ Boolean prefix
List<Product> products = [];    // ✅ Plural for lists
final _controller = ...;        // ✅ Private prefix

// Functions
void getUserById(String id)     // ✅ Verb + descriptive
Widget _buildAppBar()           // ✅ Build helper prefix
void onPressed()                // ✅ Callback prefix
```

---

## 📦 Class Member Order

```dart
class MyWidget extends StatefulWidget {
  // 1. Static constants
  static const String routeName = '/home';

  // 2. Static variables
  static final _logger = Logger();

  // 3. Instance variables (public)
  final String title;

  // 4. Instance variables (private)
  final _controller = TextEditingController();

  // 5. Constructor
  const MyWidget({super.key, required this.title});

  // 6. Lifecycle methods
  @override
  void initState() { }

  // 7. Public methods
  void refresh() { }

  // 8. Private methods
  void _loadData() { }

  // 9. Build method
  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: _buildAppBar(),
      body: _buildBody(),
    );
  }

  // 10. Build helpers
  AppBar _buildAppBar() { }
  Widget _buildBody() { }
}
```

---

## ⚡ Performance Rules

```dart
// ✅ Always use const
const Text('Hello')
const EdgeInsets.all(16)
const Icon(Icons.home)

// ✅ Use .builder() for lists
ListView.builder(
  itemCount: items.length,
  itemBuilder: (context, index) => ItemCard(items[index]),
)

// ❌ Never create all items upfront
ListView(
  children: items.map((item) => ItemCard(item)).toList(),
)
```

---

## 🚫 Common Anti-Patterns to Avoid

```dart
// ❌ God Classes
class Manager { } // Too generic, doing too much

// ❌ Magic Numbers
if (age >= 18) { } // Use Constants.legalAge

// ❌ Primitive Obsession
void createUser(String name, String email, String phone, String address)
// Use domain objects instead

// ❌ Long Build Methods
Widget build(BuildContext context) {
  return Scaffold(
    // 80 lines of nested widgets...
  );
}
// Extract to _build methods or separate widgets
```

---

## ✅ Pre-Commit Checklist

```yaml
Quick Checks:
  - [ ] No file >300 lines
  - [ ] No function >50 lines
  - [ ] Build methods <30 lines
  - [ ] Files in snake_case
  - [ ] Classes in PascalCase
  - [ ] const used where possible
  - [ ] ListView.builder() for lists
  - [ ] No magic numbers/strings
  - [ ] Tests passing
```

---

## 🎯 When to Refactor

**Immediate refactoring needed when:**

- File reaches 250 lines
- Function reaches 40 lines
- More than 3 levels of nesting
- More than 5 function parameters
- Same code in 3+ places
- Can't explain class purpose in one sentence

---

## 📚 Widget Extraction Guide

```dart
// ❌ Too nested (>30 lines)
Widget build(BuildContext context) {
  return Card(
    child: Padding(
      child: Column(
        children: [
          Row(children: [/* 20 lines */]),
          Divider(),
          Row(children: [/* 15 lines */]),
        ],
      ),
    ),
  );
}

// ✅ Extracted and clean
Widget build(BuildContext context) {
  return ProductCard(
    product: product,
    onEdit: _onEdit,
    onDelete: _onDelete,
  );
}
```

---

## 🧪 Testing Standards

```dart
// AAA Pattern: Arrange, Act, Assert
test('should return products when database has data', () async {
  // Arrange
  final mockProducts = [Product(id: '1', name: 'Test')];
  when(() => mockDatabase.getProducts())
      .thenAnswer((_) async => mockProducts);

  // Act
  final result = await repository.getProducts();

  // Assert
  expect(result.isRight(), true);
});
```

---

## 💡 Key Principles

1. **Quality over Quantity** - Clean code beats clever code
2. **Team-First** - Code is read 10x more than written
3. **Explicit over Implicit** - Clear intent matters
4. **Refactor Early** - Don't wait for tech debt
5. **Const Everything** - Performance from the start
6. **Extract Aggressively** - Small, focused components

---

**Remember:** These aren't suggestions—they're requirements for production code. When in doubt, refactor!

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/naawaal) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
