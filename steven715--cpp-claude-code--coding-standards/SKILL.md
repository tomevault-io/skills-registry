---
name: coding-standards
description: Universal coding standards, best practices, and patterns for C++11/14 development. Use when this capability is needed.
metadata:
  author: steven715
---

# C++ Coding Standards & Best Practices

Universal coding standards applicable across all C++11/14 projects.

## Code Quality Principles

### 1. Readability First
- Code is read more than written
- Clear variable and function names
- Self-documenting code preferred over comments
- Consistent formatting

### 2. KISS (Keep It Simple, Stupid)
- Simplest solution that works
- Avoid over-engineering
- No premature optimization
- Easy to understand > clever code

### 3. DRY (Don't Repeat Yourself)
- Extract common logic into functions
- Create reusable components
- Share utilities across modules
- Avoid copy-paste programming

### 4. YAGNI (You Aren't Gonna Need It)
- Don't build features before they're needed
- Avoid speculative generality
- Add complexity only when required
- Start simple, refactor when needed

## C++11/14 Standards

### Variable Naming

```cpp
// Use descriptive names
std::string market_search_query = "election";
bool is_user_authenticated = true;
double total_revenue = 1000.0;

// Use snake_case for variables and functions
int user_count = 0;
std::string file_path;

// Use PascalCase for types and classes
class MarketService;
struct UserData;

// Use UPPER_CASE for constants and macros
constexpr int MAX_BUFFER_SIZE = 1024;
#define DEBUG_MODE 1

// Prefix member variables with m_ or use trailing underscore
class User {
private:
    std::string m_name;      // or name_
    int m_age;               // or age_
};
```

### Function Naming

```cpp
// Use verb-noun pattern
std::vector<Market> fetch_market_data(const std::string& market_id);
double calculate_similarity(const std::vector<double>& a, const std::vector<double>& b);
bool is_valid_email(const std::string& email);

// Use snake_case for functions
void process_user_request();
std::string get_user_name();

// Free functions should be clear about their purpose
bool parse_config_file(const std::string& path, Config& out_config);
```

### RAII Pattern (CRITICAL)

```cpp
// ALWAYS use RAII for resource management
class FileHandle {
public:
    explicit FileHandle(const std::string& path)
        : m_file(std::fopen(path.c_str(), "r")) {
        if (!m_file) {
            throw std::runtime_error("Failed to open file");
        }
    }

    ~FileHandle() {
        if (m_file) {
            std::fclose(m_file);
        }
    }

    // Delete copy operations
    FileHandle(const FileHandle&) = delete;
    FileHandle& operator=(const FileHandle&) = delete;

    // Allow move operations
    FileHandle(FileHandle&& other) noexcept
        : m_file(other.m_file) {
        other.m_file = nullptr;
    }

private:
    FILE* m_file;
};

// Use smart pointers instead of raw pointers
std::unique_ptr<User> user = std::make_unique<User>("John");
std::shared_ptr<Resource> resource = std::make_shared<Resource>();
```

### Smart Pointers

```cpp
// std::unique_ptr for exclusive ownership
std::unique_ptr<Database> db = std::make_unique<Database>();

// std::shared_ptr for shared ownership
std::shared_ptr<Cache> cache = std::make_shared<Cache>();

// std::weak_ptr to break circular references
class Node {
    std::shared_ptr<Node> m_next;
    std::weak_ptr<Node> m_parent;  // Avoid circular reference
};

// NEVER use raw new/delete
// new Resource();           // BAD
// delete resource;          // BAD
auto resource = std::make_unique<Resource>();  // GOOD
```

### Const Correctness

```cpp
// Use const for values that don't change
const int max_size = 100;
const std::string& get_name() const;

// Use constexpr for compile-time constants
constexpr int BUFFER_SIZE = 1024;
constexpr double PI = 3.14159265359;

// Const member functions
class Calculator {
public:
    int get_result() const { return m_result; }
    void set_result(int value) { m_result = value; }
private:
    int m_result;
};

// Const references for read-only parameters
void process(const std::string& input);
void analyze(const std::vector<int>& data);
```

### Error Handling

```cpp
// Use exceptions for exceptional cases
void read_file(const std::string& path) {
    std::ifstream file(path);
    if (!file.is_open()) {
        throw std::runtime_error("Failed to open file: " + path);
    }
    // Process file...
}

// Use std::optional for expected missing values (C++14 with library)
#include <experimental/optional>  // or use boost::optional
std::experimental::optional<User> find_user(int id) {
    auto it = users.find(id);
    if (it != users.end()) {
        return it->second;
    }
    return std::experimental::nullopt;
}

// Use error codes for expected failures in performance-critical code
enum class ErrorCode {
    Success,
    FileNotFound,
    PermissionDenied,
    InvalidFormat
};

ErrorCode parse_config(const std::string& path, Config& out) {
    // Implementation
    return ErrorCode::Success;
}
```

### Modern C++11/14 Features

```cpp
// Auto type deduction
auto count = 42;                           // int
auto name = std::string("John");           // std::string
auto ptr = std::make_unique<User>();       // std::unique_ptr<User>

// Range-based for loops
std::vector<int> numbers = {1, 2, 3, 4, 5};
for (const auto& num : numbers) {
    std::cout << num << "\n";
}

// Lambda expressions
auto sum = [](int a, int b) { return a + b; };
std::sort(users.begin(), users.end(),
    [](const User& a, const User& b) {
        return a.name < b.name;
    });

// nullptr instead of NULL
int* ptr = nullptr;

// Override and final
class Base {
public:
    virtual void process() = 0;
};

class Derived : public Base {
public:
    void process() override final {
        // Implementation
    }
};

// Uniform initialization
std::vector<int> vec{1, 2, 3, 4, 5};
User user{"John", 30};

// Move semantics
std::string create_string() {
    std::string result = "Hello";
    return result;  // Move, not copy
}

std::vector<std::unique_ptr<Object>> objects;
objects.push_back(std::move(obj));  // Transfer ownership
```

### STL Container Usage

```cpp
// Choose the right container
std::vector<int> dynamic_array;           // Default choice, cache-friendly
std::array<int, 10> fixed_array;          // Fixed size, stack allocated
std::unordered_map<int, User> fast_lookup;// O(1) average lookup
std::map<int, User> ordered_lookup;       // O(log n) lookup, ordered
std::unordered_set<int> unique_items;     // O(1) membership test

// Reserve capacity when size is known
std::vector<User> users;
users.reserve(1000);  // Avoid reallocations

// Use emplace instead of push when constructing in-place
users.emplace_back("John", 30);  // Construct in-place
// users.push_back(User("John", 30));  // Less efficient

// Prefer algorithms over raw loops
auto it = std::find_if(users.begin(), users.end(),
    [](const User& u) { return u.age > 18; });

std::transform(input.begin(), input.end(), output.begin(),
    [](int x) { return x * 2; });
```

## File Organization

### Project Structure

```
project/
├── CMakeLists.txt              # Root CMake configuration
├── vcpkg.json                  # vcpkg dependencies
├── .clang-format               # Code formatting rules
├── .clang-tidy                 # Static analysis rules
├── include/                    # Public headers
│   └── project/
│       ├── core/
│       │   └── types.hpp
│       └── utils/
│           └── string_utils.hpp
├── src/                        # Implementation files
│   ├── core/
│   │   └── types.cpp
│   └── utils/
│       └── string_utils.cpp
├── tests/                      # Test files
│   ├── CMakeLists.txt
│   ├── core/
│   │   └── types_test.cpp
│   └── utils/
│       └── string_utils_test.cpp
├── examples/                   # Example programs
│   └── basic_usage.cpp
└── docs/                       # Documentation
    └── api.md
```

### Header File Guidelines

```cpp
// file: include/project/user.hpp
#ifndef PROJECT_USER_HPP
#define PROJECT_USER_HPP

#include <string>
#include <memory>

namespace project {

class User {
public:
    explicit User(std::string name);
    ~User();

    // Getters
    const std::string& get_name() const;
    int get_age() const;

    // Setters
    void set_age(int age);

private:
    std::string m_name;
    int m_age;
};

}  // namespace project

#endif  // PROJECT_USER_HPP
```

### Implementation File Guidelines

```cpp
// file: src/user.cpp
#include "project/user.hpp"

#include <stdexcept>

namespace project {

User::User(std::string name)
    : m_name(std::move(name))
    , m_age(0) {
    if (m_name.empty()) {
        throw std::invalid_argument("Name cannot be empty");
    }
}

User::~User() = default;

const std::string& User::get_name() const {
    return m_name;
}

int User::get_age() const {
    return m_age;
}

void User::set_age(int age) {
    if (age < 0) {
        throw std::invalid_argument("Age cannot be negative");
    }
    m_age = age;
}

}  // namespace project
```

### File Naming

```
include/project/user_service.hpp    # snake_case for files
src/user_service.cpp                # Match header name
tests/user_service_test.cpp         # _test suffix for tests
```

## Comments & Documentation

### When to Comment

```cpp
// Explain WHY, not WHAT
// Use exponential backoff to avoid overwhelming the API during outages
const int delay = std::min(1000 * static_cast<int>(std::pow(2, retry_count)), 30000);

// Document non-obvious behavior
// Thread-safe: protected by m_mutex
void add_item(const Item& item);

// Don't state the obvious
// count++;  // Increment counter - BAD
count++;     // No comment needed
```

### Doxygen for Public APIs

```cpp
/**
 * @brief Searches markets using semantic similarity.
 *
 * @param query Natural language search query
 * @param limit Maximum number of results (default: 10)
 * @return Vector of markets sorted by similarity score
 * @throws std::runtime_error If search service is unavailable
 *
 * @example
 * @code
 * auto results = search_markets("election", 5);
 * std::cout << results[0].name << std::endl;
 * @endcode
 */
std::vector<Market> search_markets(const std::string& query, int limit = 10);
```

## Performance Best Practices

### Pass by Reference

```cpp
// Pass large objects by const reference
void process(const std::vector<int>& data);
void analyze(const std::string& text);

// Pass small objects by value
void calculate(int value);
void set_flag(bool enabled);

// Return by value (move semantics will optimize)
std::vector<int> get_data() {
    std::vector<int> result;
    // Fill result...
    return result;  // Moved, not copied
}
```

### Avoid Unnecessary Copies

```cpp
// Use references in range-based for
for (const auto& item : items) {  // No copy
    process(item);
}

// Use emplace instead of push
container.emplace_back(arg1, arg2);  // Construct in-place

// Move when transferring ownership
std::vector<std::string> result;
result.push_back(std::move(temp_string));  // Move, not copy
```

### Reserve Container Capacity

```cpp
// Pre-allocate when size is known
std::vector<User> users;
users.reserve(expected_count);

for (int i = 0; i < expected_count; ++i) {
    users.emplace_back(/* ... */);
}
```

## Code Smell Detection

Watch for these anti-patterns:

### 1. Long Functions
```cpp
// Split into smaller functions
void process_data() {
    auto validated = validate_input();
    auto transformed = transform_data(validated);
    save_result(transformed);
}
```

### 2. Deep Nesting
```cpp
// Use early returns
if (!user) return;
if (!user->is_admin()) return;
if (!market) return;
if (!market->is_active()) return;

// Do something
```

### 3. Magic Numbers
```cpp
// Use named constants
constexpr int MAX_RETRIES = 3;
constexpr int DEBOUNCE_DELAY_MS = 500;

if (retry_count > MAX_RETRIES) { }
```

### 4. Raw Pointers for Ownership
```cpp
// Use smart pointers
auto resource = std::make_unique<Resource>();  // Clear ownership

// Raw pointers only for non-owning references
void process(Resource* resource);  // Borrows, doesn't own
```

### 5. Manual Memory Management
```cpp
// Never do this
Resource* r = new Resource();
delete r;

// Always use RAII or smart pointers
auto r = std::make_unique<Resource>();
```

**Remember**: Code quality is not negotiable. Clear, maintainable code enables rapid development and confident refactoring.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/steven715) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
