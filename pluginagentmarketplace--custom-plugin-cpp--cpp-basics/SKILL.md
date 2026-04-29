---
name: cpp-basics
description: Topic to teach or practice Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# C++ Basics Skill

**Production-Grade Learning Skill** | Foundation Building

Master the essential building blocks of C++ programming.

---

## Topics Covered

### 1. Variables & Data Types

```cpp
#include <iostream>
#include <cstdint>  // Fixed-width integers

int main() {
    // ─────────────────────────────────────────────────
    // Integer Types (with guaranteed sizes)
    // ─────────────────────────────────────────────────
    int8_t   tiny   = 127;                  // 1 byte, -128 to 127
    int16_t  small  = 32767;                // 2 bytes
    int32_t  normal = 2147483647;           // 4 bytes
    int64_t  large  = 9223372036854775807;  // 8 bytes

    // Unsigned variants
    uint8_t  ubyte  = 255;                  // 0 to 255
    uint32_t ucount = 4294967295;           // 0 to 4B

    // ─────────────────────────────────────────────────
    // Floating Point
    // ─────────────────────────────────────────────────
    float  f = 3.14f;           // ~7 decimal digits
    double d = 3.14159265358979; // ~15 decimal digits

    // ─────────────────────────────────────────────────
    // Character & Boolean
    // ─────────────────────────────────────────────────
    char   c = 'A';       // ASCII character
    bool   b = true;      // true or false

    // ─────────────────────────────────────────────────
    // Modern C++ Initialization (prefer brace init)
    // ─────────────────────────────────────────────────
    int x{42};            // Brace initialization
    auto y = 3.14;        // Type deduction (double)
    auto z{10};           // int

    return 0;
}
```

### 2. Operators

| Category | Operators | Example |
|----------|-----------|---------|
| Arithmetic | `+ - * / %` | `10 / 3 = 3` |
| Comparison | `== != < > <= >=` | `5 == 5` → `true` |
| Logical | `&& \|\| !` | `true && false` → `false` |
| Bitwise | `& \| ^ ~ << >>` | `0xFF & 0x0F` → `0x0F` |
| Assignment | `= += -= *= /=` | `x += 5` |
| Increment | `++ --` | `++i` (prefer prefix) |

### 3. Control Flow

```cpp
// ─────────────────────────────────────────────────────
// Conditionals
// ─────────────────────────────────────────────────────
if (score >= 90) {
    grade = 'A';
} else if (score >= 80) {
    grade = 'B';
} else {
    grade = 'F';
}

// Switch with C++17 init
switch (int x = getValue(); x) {
    case 1:  std::cout << "One\n";  break;
    case 2:  std::cout << "Two\n";  break;
    default: std::cout << "Other\n"; break;
}

// ─────────────────────────────────────────────────────
// Loops
// ─────────────────────────────────────────────────────
// For loop (prefer prefix ++)
for (int i = 0; i < n; ++i) { }

// Range-based for (C++11)
for (const auto& item : container) { }

// While
while (condition) { }

// Do-while (runs at least once)
do { } while (condition);
```

### 4. Functions

```cpp
// ─────────────────────────────────────────────────────
// Function declaration
// ─────────────────────────────────────────────────────
int add(int a, int b);               // Declaration
void greet(std::string_view name);   // string_view for efficiency

// ─────────────────────────────────────────────────────
// Definition with default parameter
// ─────────────────────────────────────────────────────
int add(int a, int b) {
    return a + b;
}

void greet(std::string_view name = "World") {
    std::cout << "Hello, " << name << "!\n";
}

// ─────────────────────────────────────────────────────
// Pass by reference (for modification or large objects)
// ─────────────────────────────────────────────────────
void increment(int& value) {
    ++value;
}

void process(const std::vector<int>& data) {  // const ref for read-only
    for (int x : data) { /* ... */ }
}

// ─────────────────────────────────────────────────────
// Function overloading
// ─────────────────────────────────────────────────────
int max(int a, int b) { return (a > b) ? a : b; }
double max(double a, double b) { return (a > b) ? a : b; }
```

---

## Troubleshooting

### Common Errors

| Error | Cause | Fix |
|-------|-------|-----|
| `undefined reference` | Missing function definition | Add function body |
| `expected ';'` | Missing semicolon | Check previous line |
| `undeclared identifier` | Variable not declared | Declare before use |
| `narrowing conversion` | Data loss in brace init | Use explicit cast |

### Decision Tree

```
Compilation error?
├── "undefined reference"
│   └── Function declared but not defined → Add definition
├── "expected ';'"
│   └── Missing semicolon → Check line above error
├── "undeclared identifier"
│   └── Variable not in scope → Declare or check spelling
└── "no matching function"
    └── Wrong argument types → Match function signature
```

---

## Unit Test Template

```cpp
#include <cassert>

void test_basics() {
    // Test integer operations
    assert(add(2, 3) == 5);
    assert(add(-1, 1) == 0);

    // Test floating point (with tolerance)
    constexpr double epsilon = 1e-9;
    assert(std::abs(calculateArea(2.0) - 12.566370614) < epsilon);

    // Test boolean logic
    assert((true && false) == false);
    assert((true || false) == true);

    std::cout << "All tests passed!\n";
}
```

---

## Learning Path

```
Week 1: Foundation
├── Day 1-2: Variables & data types
├── Day 3-4: Operators
├── Day 5-6: Control flow
└── Day 7: Practice exercises

Week 2: Functions & Arrays
├── Day 1-2: Functions basics
├── Day 3-4: Arrays & pointers
├── Day 5-6: String handling
└── Day 7: Mini project
```

---

## References

- [C++ Reference](https://en.cppreference.com/w/)
- [Learn C++](https://www.learncpp.com/)
- [C++ Core Guidelines](https://isocpp.github.io/CppCoreGuidelines/)

---

*C++ Plugin v3.0.0 - Production-Grade Learning Skill*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-12 -->
