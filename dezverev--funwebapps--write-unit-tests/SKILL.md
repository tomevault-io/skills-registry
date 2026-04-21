---
name: write-unit-tests
description: Writes comprehensive unit tests using xUnit and Moq. Use when the user wants to create tests, add test coverage, write unit tests, or test new functionality. Always finds root cause of failures and fixes the code, not the tests. Use when this capability is needed.
metadata:
  author: dezverev
---

# Write Unit Tests

Creates comprehensive unit tests in `src/Tests` using xUnit and Moq for mocking.

## Core Principles

### NEVER Cheat on Tests

❌ **Forbidden without deep justification:**
- Disabling or skipping tests
- Weakening assertions to pass
- Removing edge case tests
- Changing expected values to match buggy output
- Using `[Fact(Skip = "...")]` to hide failures

✅ **Always:**
- Find the root cause of test failures
- Fix the source code, not the test
- If test is genuinely wrong, document why with detailed justification

## Project Setup

### Step 1: Create/Verify Test Project

If `src/Tests` project doesn't exist:

```bash
dotnet new xunit -n Tests -o src/Tests
cd src/Tests
dotnet add package Moq
dotnet add package FluentAssertions  # Optional but recommended
dotnet add reference ../dev/{ProjectName}
```

### Step 2: Project Structure

```
src/Tests/
├── Tests.csproj
├── {Feature}/
│   ├── {Class}Tests.cs
│   └── {Class}MockSetup.cs  # If complex mocking needed
└── TestHelpers/
    ├── TestFixtures.cs
    └── MockFactories.cs
```

## Writing Tests

### Naming Convention

```
{MethodName}_{Scenario}_{ExpectedResult}
```

Examples:
- `GetUser_WithValidId_ReturnsUser`
- `GetUser_WithInvalidId_ThrowsNotFoundException`
- `Calculate_WithNegativeInput_ReturnsZero`

### Test Structure (AAA Pattern)

```csharp
[Fact]
public void MethodName_Scenario_ExpectedResult()
{
    // Arrange
    var mockService = new Mock<IService>();
    mockService.Setup(x => x.GetData()).Returns(expectedData);
    var sut = new MyClass(mockService.Object);

    // Act
    var result = sut.MethodUnderTest();

    // Assert
    Assert.Equal(expected, result);
    mockService.Verify(x => x.GetData(), Times.Once);
}
```

### Using Moq

```csharp
// Basic mock
var mock = new Mock<IRepository>();
mock.Setup(x => x.GetById(It.IsAny<int>())).Returns(entity);

// Async methods
mock.Setup(x => x.GetAsync(id)).ReturnsAsync(entity);

// Throwing exceptions
mock.Setup(x => x.Save(null)).Throws<ArgumentNullException>();

// Verifying calls
mock.Verify(x => x.Save(It.IsAny<Entity>()), Times.Once);

// Callback for inspection
mock.Setup(x => x.Save(It.IsAny<Entity>()))
    .Callback<Entity>(e => capturedEntity = e);
```

## Test Coverage Goals

### What to Test

| Priority | What | Coverage Goal |
|----------|------|---------------|
| 1 | Public methods | 100% |
| 2 | Edge cases | All identified |
| 3 | Error paths | All exceptions |
| 4 | Boundary conditions | Min/max/null |
| 5 | Business logic | 100% |

### Required Test Cases

For each public method, test:
- ✅ Happy path (normal operation)
- ✅ Null inputs
- ✅ Empty collections
- ✅ Boundary values (0, -1, max)
- ✅ Invalid inputs
- ✅ Exception scenarios
- ✅ Async behavior (if applicable)

## Handling Test Failures

### Step 1: Understand the Failure

```bash
dotnet test --filter "FullyQualifiedName~FailingTestName" -v detailed
```

Read the full error message and stack trace.

### Step 2: Diagnose Root Cause

1. **Is the test correct?**
   - Does it test the right behavior?
   - Are assertions accurate?

2. **Is the code correct?**
   - Does it handle this case?
   - Is there a bug?

3. **Debug if needed**
   - Add logging to understand flow
   - Step through with debugger

### Step 3: Fix the CODE (Not the Test)

```csharp
// ❌ WRONG - Changing test to pass
[Fact]
public void GetUser_WithInvalidId_ReturnsNull()  // Changed from ThrowsException
{
    var result = sut.GetUser(-1);
    Assert.Null(result);  // Weakened assertion
}

// ✅ RIGHT - Fix the source code
// In UserService.cs:
public User GetUser(int id)
{
    if (id <= 0)
        throw new ArgumentException("Invalid user ID", nameof(id));
    // ... rest of implementation
}
```

### When Test Changes ARE Justified

Only change a test if:

1. **Requirements changed** - Document the change
2. **Test was genuinely wrong** - Explain the error
3. **Better assertion exists** - Improves clarity

**Required justification format:**

```csharp
/// <summary>
/// JUSTIFICATION FOR TEST CHANGE:
/// - Original: Expected ArgumentException for negative values
/// - Changed to: Returns null for negative values
/// - Reason: Product decision - graceful degradation preferred
/// - Approved by: [stakeholder]
/// - Date: [date]
/// </summary>
[Fact]
public void GetUser_WithNegativeId_ReturnsNull()
```

## Workflow

### Step 1: Analyze Code to Test

Read the source file and identify:
- All public methods
- Dependencies to mock
- Edge cases
- Error conditions

### Step 2: Check Memory/Docs

```
search_nodes → Find testing patterns for similar code
query-docs → Get xUnit/Moq best practices if needed
```

### Step 3: Write Tests

Create test file mirroring source structure:
- `src/dev/Services/UserService.cs` → `src/Tests/Services/UserServiceTests.cs`

### Step 4: Run and Verify

```bash
dotnet test src/Tests/
```

### Step 5: Fix Failures Properly

If tests fail:
1. Diagnose root cause
2. Fix the SOURCE code
3. Re-run tests
4. Only proceed when all pass

## Example Output

```csharp
using Moq;
using Xunit;

namespace UnitTests.Services;

public class UserServiceTests
{
    private readonly Mock<IUserRepository> _mockRepo;
    private readonly Mock<ILogger<UserService>> _mockLogger;
    private readonly UserService _sut;

    public UserServiceTests()
    {
        _mockRepo = new Mock<IUserRepository>();
        _mockLogger = new Mock<ILogger<UserService>>();
        _sut = new UserService(_mockRepo.Object, _mockLogger.Object);
    }

    [Fact]
    public async Task GetUserAsync_WithValidId_ReturnsUser()
    {
        // Arrange
        var expectedUser = new User { Id = 1, Name = "Test" };
        _mockRepo.Setup(x => x.GetByIdAsync(1))
            .ReturnsAsync(expectedUser);

        // Act
        var result = await _sut.GetUserAsync(1);

        // Assert
        Assert.NotNull(result);
        Assert.Equal(expectedUser.Id, result.Id);
    }

    [Fact]
    public async Task GetUserAsync_WithInvalidId_ThrowsArgumentException()
    {
        // Arrange & Act & Assert
        await Assert.ThrowsAsync<ArgumentException>(
            () => _sut.GetUserAsync(-1));
    }

    [Fact]
    public async Task GetUserAsync_WhenNotFound_ThrowsNotFoundException()
    {
        // Arrange
        _mockRepo.Setup(x => x.GetByIdAsync(999))
            .ReturnsAsync((User)null);

        // Act & Assert
        await Assert.ThrowsAsync<NotFoundException>(
            () => _sut.GetUserAsync(999));
    }
}
```

## Additional Resources

For common mocking patterns, see [moq-patterns.md](moq-patterns.md)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/dezverev) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
