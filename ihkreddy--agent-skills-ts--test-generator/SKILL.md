---
name: test-generator
description: Generates unit tests for code. Analyzes functions/classes and creates comprehensive test cases. Use when users want tests, mention "write tests", "add unit tests", "test coverage", or TDD workflows.
license: MIT
metadata:
  author: IHKREDDY
  version: "1.0"
  category: quality
compatibility: Supports xUnit (.NET), Jest (JS/TS), pytest (Python)
---

# Test Generator Skill

## 🎯 Use Case Demo

### Scenario
*Developer Mike just wrote a new BookingService with 5 methods. He needs unit tests but testing takes forever to write.*

**Before (Manual):**
```csharp
// Spend 30 minutes writing one test...
[Fact]
public void CreateBooking_ValidData_ReturnsBooking()
{
    // Arrange... Act... Assert... 😴
}
```

**After (With Skill):**

"Generate tests for BookingService.cs"

```csharp
public class BookingServiceTests
{
    private readonly Mock<IFlightRepository> _flightRepo;
    private readonly Mock<IBookingRepository> _bookingRepo;
    private readonly BookingService _sut;

    public BookingServiceTests()
    {
        _flightRepo = new Mock<IFlightRepository>();
        _bookingRepo = new Mock<IBookingRepository>();
        _sut = new BookingService(_flightRepo.Object, _bookingRepo.Object);
    }

    [Fact]
    public void CreateBooking_WithValidFlight_ReturnsNewBooking()
    {
        // Arrange
        var flight = new Flight { Id = 1, AvailableSeats = 10 };
        _flightRepo.Setup(r => r.GetById(1)).Returns(flight);
        
        // Act
        var result = _sut.CreateBooking(1, "John Doe", "john@example.com");
        
        // Assert
        Assert.NotNull(result);
        Assert.Equal("John Doe", result.PassengerName);
    }

    [Fact]
    public void CreateBooking_FlightNotFound_ThrowsException()
    {
        // Arrange
        _flightRepo.Setup(r => r.GetById(99)).Returns((Flight)null);
        
        // Act & Assert
        Assert.Throws<FlightNotFoundException>(() => 
            _sut.CreateBooking(99, "John", "john@example.com"));
    }

    [Theory]
    [InlineData("")]
    [InlineData(null)]
    [InlineData("   ")]
    public void CreateBooking_InvalidPassengerName_ThrowsValidationException(string name)
    {
        // Arrange & Act & Assert
        Assert.Throws<ValidationException>(() => 
            _sut.CreateBooking(1, name, "john@example.com"));
    }

    // ... 10 more comprehensive tests generated
}
```

### Time Saved: 2-3 hours per service class

---

## When to Use This Skill

- After writing new code
- When improving test coverage
- Before submitting PRs
- For TDD (write tests first)

## Test Types Generated

| Type | Purpose | Example |
|------|---------|---------|
| **Happy Path** | Normal operation | Valid input → expected output |
| **Edge Cases** | Boundary conditions | Empty, null, max values |
| **Error Cases** | Exception handling | Invalid input → proper exception |
| **Integration** | Component interaction | Service → Repository → DB |

## Agent Instructions

When user asks for tests:

1. **Read the source file** to understand:
   - Class structure and dependencies
   - Public methods to test
   - Input/output types
   - Possible exceptions

2. **Identify test scenarios**:
   - Happy path for each method
   - Null/empty inputs
   - Boundary values
   - Exception conditions

3. **Generate test class**:
   - Setup with mocks
   - Arrange-Act-Assert pattern
   - Descriptive test names
   - Theory/InlineData for parameterized tests

4. **Match project framework**:
   - .NET → xUnit + Moq
   - Node.js → Jest
   - Python → pytest

### Example Prompts

User: "Write tests for BookingService"
→ Generate comprehensive test file

User: "Add edge case tests for CreateBooking method"
→ Focus on specific method edge cases

User: "What scenarios should I test?"
→ List test cases without code

## Demo Script

```bash
# 1. Show existing service
cat Services/BookingService.cs

# 2. Ask agent: "Generate unit tests for BookingService"

# 3. Agent creates BookingServiceTests.cs with:
#    - 15+ test methods
#    - Mocked dependencies
#    - Edge cases covered

# 4. Run tests
dotnet test
```

## Benefits

| Metric | Before | After | Improvement |
|--------|--------|-------|-------------|
| Test writing time | 2-3 hours | 5 minutes | ⬇️ 95% |
| Test coverage | 30-50% | 80-90% | ⬆️ 2x |
| Edge cases found | Often missed | Comprehensive | ✅ Complete |
| TDD adoption | Difficult | Easy | ✅ Enabled |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ihkreddy) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
