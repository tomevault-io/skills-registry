---
name: tdd-workflow
description: Use this skill when writing new features, fixing bugs, or refactoring code. Enforces test-driven development with 80%+ coverage using Google Test and CMake/CTest.
metadata:
  author: steven715
---

# Test-Driven Development Workflow for C++

This skill ensures all C++ code development follows TDD principles with comprehensive test coverage.

## When to Activate

- Writing new features or functionality
- Fixing bugs or issues
- Refactoring existing code
- Adding new classes or modules
- Creating library interfaces

## Core Principles

### 1. Tests BEFORE Code
ALWAYS write tests first, then implement code to make tests pass.

### 2. Coverage Requirements
- Minimum 80% coverage (unit + integration)
- All edge cases covered
- Error scenarios tested
- Boundary conditions verified

### 3. Test Types

#### Unit Tests
- Individual functions and methods
- Class behavior
- Pure functions
- Utility helpers

#### Integration Tests
- Module interactions
- External system mocks
- Database operations
- File I/O operations

## TDD Workflow Steps

### Step 1: Write User Stories
```
As a [role], I want to [action], so that [benefit]

Example:
As a developer, I want to parse configuration files,
so that I can configure the application at runtime.
```

### Step 2: Generate Test Cases
For each user story, create comprehensive test cases:

```cpp
#include <gtest/gtest.h>
#include "config_parser.hpp"

class ConfigParserTest : public ::testing::Test {
protected:
    void SetUp() override {
        // Setup test fixtures
    }

    void TearDown() override {
        // Cleanup
    }
};

TEST_F(ConfigParserTest, ParsesValidConfigFile) {
    ConfigParser parser;
    auto result = parser.parse("config.json");

    ASSERT_TRUE(result.is_ok());
    EXPECT_EQ(result.value().get<int>("port"), 8080);
}

TEST_F(ConfigParserTest, HandlesEmptyFileGracefully) {
    ConfigParser parser;
    auto result = parser.parse("empty.json");

    EXPECT_FALSE(result.is_ok());
    EXPECT_EQ(result.error(), "Empty configuration file");
}

TEST_F(ConfigParserTest, ThrowsOnMissingFile) {
    ConfigParser parser;

    EXPECT_THROW(parser.parse("nonexistent.json"), std::runtime_error);
}

TEST_F(ConfigParserTest, ValidatesRequiredFields) {
    ConfigParser parser;
    auto result = parser.parse("incomplete.json");

    EXPECT_FALSE(result.is_ok());
    EXPECT_THAT(result.error(), ::testing::HasSubstr("missing required field"));
}
```

### Step 3: Run Tests (They Should Fail)
```bash
# Configure and build
cmake --preset debug
cmake --build build/debug

# Run tests
ctest --test-dir build/debug --output-on-failure
# Tests should fail - we haven't implemented yet
```

### Step 4: Implement Code
Write minimal code to make tests pass:

```cpp
// config_parser.hpp
#pragma once

#include <string>
#include "result.hpp"

class ConfigParser {
public:
    Result<Config, std::string> parse(const std::string& path);
};

// config_parser.cpp
#include "config_parser.hpp"
#include <fstream>
#include <nlohmann/json.hpp>

Result<Config, std::string> ConfigParser::parse(const std::string& path) {
    std::ifstream file(path);
    if (!file.is_open()) {
        throw std::runtime_error("Cannot open file: " + path);
    }

    // Implementation here...
}
```

### Step 5: Run Tests Again
```bash
ctest --test-dir build/debug --output-on-failure
# Tests should now pass
```

### Step 6: Refactor
Improve code quality while keeping tests green:
- Remove duplication
- Improve naming
- Optimize performance
- Enhance readability

### Step 7: Verify Coverage
```bash
# Build with coverage
cmake --preset debug -DCODE_COVERAGE=ON
cmake --build build/debug

# Run tests and generate coverage
ctest --test-dir build/debug
gcovr --root . --exclude tests/ --html coverage.html

# Verify 80%+ coverage achieved
```

## Google Test Patterns

### Basic Test Structure (AAA Pattern)

```cpp
TEST(CalculatorTest, AddsPositiveNumbers) {
    // Arrange
    Calculator calc;
    int a = 5;
    int b = 3;

    // Act
    int result = calc.add(a, b);

    // Assert
    EXPECT_EQ(result, 8);
}
```

### Test Fixtures

```cpp
class DatabaseTest : public ::testing::Test {
protected:
    std::unique_ptr<Database> db;
    std::unique_ptr<UserRepository> repo;

    void SetUp() override {
        db = std::make_unique<Database>(":memory:");
        db->execute("CREATE TABLE users (id INTEGER PRIMARY KEY, name TEXT)");
        repo = std::make_unique<UserRepository>(*db);
    }

    void TearDown() override {
        db.reset();
    }
};

TEST_F(DatabaseTest, InsertsUser) {
    User user{0, "John"};
    repo->create(user);

    auto found = repo->find_by_id(1);
    ASSERT_NE(found, nullptr);
    EXPECT_EQ(found->name, "John");
}

TEST_F(DatabaseTest, ReturnsNullForMissingUser) {
    auto found = repo->find_by_id(999);
    EXPECT_EQ(found, nullptr);
}
```

### Parameterized Tests

```cpp
class PrimeTest : public ::testing::TestWithParam<std::pair<int, bool>> {};

TEST_P(PrimeTest, ChecksPrimality) {
    auto [number, expected] = GetParam();
    EXPECT_EQ(is_prime(number), expected);
}

INSTANTIATE_TEST_SUITE_P(
    PrimeNumbers,
    PrimeTest,
    ::testing::Values(
        std::make_pair(2, true),
        std::make_pair(3, true),
        std::make_pair(4, false),
        std::make_pair(5, true),
        std::make_pair(6, false),
        std::make_pair(7, true),
        std::make_pair(11, true),
        std::make_pair(15, false)
    )
);
```

### Typed Tests

```cpp
template <typename T>
class ContainerTest : public ::testing::Test {
public:
    using ContainerType = T;
};

using ContainerTypes = ::testing::Types<std::vector<int>, std::list<int>, std::deque<int>>;
TYPED_TEST_SUITE(ContainerTest, ContainerTypes);

TYPED_TEST(ContainerTest, StartsEmpty) {
    TypeParam container;
    EXPECT_TRUE(container.empty());
}

TYPED_TEST(ContainerTest, SizeIncreasesOnInsert) {
    TypeParam container;
    container.push_back(42);
    EXPECT_EQ(container.size(), 1);
}
```

### Exception Testing

```cpp
TEST(ParserTest, ThrowsOnInvalidInput) {
    Parser parser;

    EXPECT_THROW(parser.parse(""), std::invalid_argument);
    EXPECT_THROW(parser.parse("{{invalid}}"), ParseError);
}

TEST(ParserTest, ThrowsWithCorrectMessage) {
    Parser parser;

    try {
        parser.parse("bad input");
        FAIL() << "Expected ParseError";
    } catch (const ParseError& e) {
        EXPECT_THAT(e.what(), ::testing::HasSubstr("unexpected token"));
    }
}
```

### Matchers (Google Mock)

```cpp
#include <gmock/gmock.h>

using namespace ::testing;

TEST(StringTest, ContainsSubstring) {
    std::string text = "Hello, World!";

    EXPECT_THAT(text, HasSubstr("World"));
    EXPECT_THAT(text, StartsWith("Hello"));
    EXPECT_THAT(text, EndsWith("!"));
    EXPECT_THAT(text, MatchesRegex("Hello.*!"));
}

TEST(ContainerTest, ContainsElements) {
    std::vector<int> nums = {1, 2, 3, 4, 5};

    EXPECT_THAT(nums, Contains(3));
    EXPECT_THAT(nums, ElementsAre(1, 2, 3, 4, 5));
    EXPECT_THAT(nums, UnorderedElementsAre(5, 4, 3, 2, 1));
    EXPECT_THAT(nums, Each(Gt(0)));
    EXPECT_THAT(nums, SizeIs(5));
}
```

## Mocking with Google Mock

### Interface Mocking

```cpp
// Interface
class IDatabase {
public:
    virtual ~IDatabase() = default;
    virtual std::vector<User> find_all() = 0;
    virtual std::unique_ptr<User> find_by_id(int id) = 0;
    virtual void save(const User& user) = 0;
};

// Mock
class MockDatabase : public IDatabase {
public:
    MOCK_METHOD(std::vector<User>, find_all, (), (override));
    MOCK_METHOD(std::unique_ptr<User>, find_by_id, (int id), (override));
    MOCK_METHOD(void, save, (const User& user), (override));
};

// Test using mock
TEST(UserServiceTest, ReturnsAllUsers) {
    MockDatabase mock_db;
    UserService service(mock_db);

    std::vector<User> expected_users = {
        {1, "Alice"},
        {2, "Bob"}
    };

    EXPECT_CALL(mock_db, find_all())
        .WillOnce(Return(expected_users));

    auto users = service.get_all_users();

    EXPECT_EQ(users.size(), 2);
    EXPECT_EQ(users[0].name, "Alice");
}

TEST(UserServiceTest, HandlesNotFoundUser) {
    MockDatabase mock_db;
    UserService service(mock_db);

    EXPECT_CALL(mock_db, find_by_id(999))
        .WillOnce(Return(nullptr));

    auto user = service.get_user(999);

    EXPECT_EQ(user, nullptr);
}
```

### Verifying Call Order

```cpp
TEST(WorkflowTest, ExecutesInCorrectOrder) {
    MockLogger mock_logger;
    MockDatabase mock_db;
    Workflow workflow(mock_logger, mock_db);

    {
        InSequence seq;

        EXPECT_CALL(mock_logger, log("Starting workflow"));
        EXPECT_CALL(mock_db, begin_transaction());
        EXPECT_CALL(mock_db, execute(_)).Times(AtLeast(1));
        EXPECT_CALL(mock_db, commit());
        EXPECT_CALL(mock_logger, log("Workflow completed"));
    }

    workflow.run();
}
```

## CMake Test Configuration

### CMakeLists.txt for Tests

```cmake
# tests/CMakeLists.txt
include(FetchContent)

FetchContent_Declare(
    googletest
    GIT_REPOSITORY https://github.com/google/googletest.git
    GIT_TAG v1.14.0
)
FetchContent_MakeAvailable(googletest)

# Or use vcpkg
# find_package(GTest CONFIG REQUIRED)

enable_testing()

add_executable(unit_tests
    core/calculator_test.cpp
    core/parser_test.cpp
    utils/string_utils_test.cpp
)

target_link_libraries(unit_tests
    PRIVATE
        ${PROJECT_NAME}_lib
        GTest::gtest_main
        GTest::gmock
)

include(GoogleTest)
gtest_discover_tests(unit_tests)
```

### vcpkg.json for Testing

```json
{
    "name": "my-project",
    "version": "1.0.0",
    "dependencies": [
        "fmt",
        "spdlog"
    ],
    "dev-dependencies": [
        "gtest"
    ]
}
```

## Test File Organization

```
project/
├── CMakeLists.txt
├── include/
│   └── project/
│       ├── calculator.hpp
│       └── parser.hpp
├── src/
│   ├── calculator.cpp
│   └── parser.cpp
└── tests/
    ├── CMakeLists.txt
    ├── test_main.cpp           # Custom main (optional)
    ├── core/
    │   ├── calculator_test.cpp
    │   └── parser_test.cpp
    ├── utils/
    │   └── string_utils_test.cpp
    └── fixtures/
        ├── test_data.json
        └── sample_config.json
```

### Test Main (Optional Custom Main)

```cpp
// tests/test_main.cpp
#include <gtest/gtest.h>

int main(int argc, char** argv) {
    ::testing::InitGoogleTest(&argc, argv);

    // Custom setup if needed
    // e.g., initialize logging

    return RUN_ALL_TESTS();
}
```

## Code Coverage with gcov/lcov

### CMake Coverage Configuration

```cmake
option(CODE_COVERAGE "Enable coverage reporting" OFF)

if(CODE_COVERAGE AND CMAKE_CXX_COMPILER_ID MATCHES "GNU|Clang")
    target_compile_options(${PROJECT_NAME}_lib PUBLIC --coverage -O0 -g)
    target_link_options(${PROJECT_NAME}_lib PUBLIC --coverage)
endif()
```

### Generate Coverage Report

```bash
# Build with coverage
cmake -B build -DCODE_COVERAGE=ON
cmake --build build

# Run tests
cd build && ctest

# Generate HTML report
gcovr --root .. --exclude '../tests/' --html-details coverage.html

# Or use lcov
lcov --capture --directory . --output-file coverage.info
lcov --remove coverage.info '/usr/*' --output-file coverage.info
lcov --remove coverage.info '*/tests/*' --output-file coverage.info
genhtml coverage.info --output-directory coverage_report
```

### Coverage Thresholds

```bash
# Check coverage meets threshold
gcovr --root . --exclude tests/ --fail-under-line 80
```

## Common Testing Mistakes to Avoid

### Test Implementation Details
```cpp
// WRONG: Testing private implementation
EXPECT_EQ(cache.m_internal_map.size(), 5);

// CORRECT: Test observable behavior
cache.put("key", "value");
EXPECT_EQ(cache.get("key"), "value");
```

### No Test Isolation
```cpp
// WRONG: Tests depend on each other
static int shared_counter = 0;

TEST(CounterTest, Increments) {
    shared_counter++;
    EXPECT_EQ(shared_counter, 1);
}

TEST(CounterTest, IncrementsAgain) {
    shared_counter++;
    EXPECT_EQ(shared_counter, 2);  // Depends on previous test!
}

// CORRECT: Independent tests
TEST(CounterTest, Increments) {
    int counter = 0;
    counter++;
    EXPECT_EQ(counter, 1);
}
```

### Brittle Tests
```cpp
// WRONG: Depends on exact error message
EXPECT_EQ(result.error(), "Error: File not found at path /tmp/test.txt");

// CORRECT: Check essential information
EXPECT_THAT(result.error(), HasSubstr("File not found"));
```

### Testing Too Much at Once
```cpp
// WRONG: One test doing too much
TEST(UserTest, Everything) {
    User user("John");
    EXPECT_EQ(user.name(), "John");
    user.set_age(30);
    EXPECT_EQ(user.age(), 30);
    user.add_friend(other_user);
    EXPECT_EQ(user.friends().size(), 1);
    // ... 50 more assertions
}

// CORRECT: Focused tests
TEST(UserTest, HasNameAfterConstruction) {
    User user("John");
    EXPECT_EQ(user.name(), "John");
}

TEST(UserTest, AgeDefaultsToZero) {
    User user("John");
    EXPECT_EQ(user.age(), 0);
}

TEST(UserTest, CanAddFriend) {
    User user("John");
    User friend_user("Jane");
    user.add_friend(friend_user);
    EXPECT_EQ(user.friends().size(), 1);
}
```

## Continuous Testing

### Watch Mode with entr

```bash
# Auto-run tests on file changes
find src include tests -name '*.cpp' -o -name '*.hpp' | \
    entr -c sh -c 'cmake --build build && ctest --test-dir build'
```

### Pre-Commit Hook

```bash
#!/bin/bash
# .git/hooks/pre-commit

cmake --build build --target unit_tests
ctest --test-dir build --output-on-failure

if [ $? -ne 0 ]; then
    echo "Tests failed. Commit aborted."
    exit 1
fi
```

### CI/CD Integration (GitHub Actions)

```yaml
name: C++ CI

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest

    steps:
      - uses: actions/checkout@v4

      - name: Install vcpkg
        run: |
          git clone https://github.com/microsoft/vcpkg.git
          ./vcpkg/bootstrap-vcpkg.sh

      - name: Configure
        run: cmake --preset debug

      - name: Build
        run: cmake --build build/debug

      - name: Test
        run: ctest --test-dir build/debug --output-on-failure

      - name: Coverage
        run: |
          cmake --preset debug -DCODE_COVERAGE=ON
          cmake --build build/debug
          ctest --test-dir build/debug
          gcovr --root . --exclude tests/ --fail-under-line 80
```

## Best Practices

1. **Write Tests First** - Always TDD
2. **One Concept Per Test** - Focus on single behavior
3. **Descriptive Test Names** - Explain what's tested
4. **Arrange-Act-Assert** - Clear test structure
5. **Mock External Dependencies** - Isolate unit tests
6. **Test Edge Cases** - Null, empty, boundary values
7. **Test Error Paths** - Not just happy paths
8. **Keep Tests Fast** - Unit tests < 10ms each
9. **Clean Up After Tests** - Use fixtures properly
10. **Review Coverage Reports** - Identify gaps

## Success Metrics

- 80%+ code coverage achieved
- All tests passing (green)
- No skipped or disabled tests
- Fast test execution (< 10s for unit tests)
- Tests catch bugs before production
- Clear test failure messages

---

**Remember**: Tests are not optional. They are the safety net that enables confident refactoring, rapid development, and production reliability.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/steven715) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
