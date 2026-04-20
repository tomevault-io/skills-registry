---
name: unity-testing
description: Create Unity Test Framework tests including EditMode tests, PlayMode tests, and performance tests. Use when implementing automated testing, TDD, or quality assurance for Unity projects. Use when this capability is needed.
metadata:
  author: haro7488
---



<!-- Navigation -->
**🏠 [HaroFramework Project](../../MASTER_INDEX.md)** | **📂 [Skill](./)** | **⬆️ [Skill](./)**

---
# Unity Testing Framework Expert

Expert skill for creating comprehensive automated tests using Unity Test Framework (UTF).

## When to Use This Skill

Activate this skill when the user requests:
- Unit tests for game logic
- Integration tests for component interactions
- PlayMode tests for runtime behavior
- Performance and profiling tests
- Test-Driven Development (TDD) setup
- Continuous Integration test automation

## Test Types

### EditMode Tests
- Run in edit mode without entering play mode
- Fast execution
- Test pure C# logic
- No GameObject lifecycle
- Cannot test MonoBehaviour runtime behavior

### PlayMode Tests
- Run in play mode
- Test runtime behavior
- Full GameObject lifecycle
- Can test scene loading, physics, coroutines
- Slower execution

## Test Assembly Setup

### Directory Structure
```
Assets/
  Scripts/
    Runtime/
      HaroFramework.Runtime.asmdef
    Tests/
      EditMode/
        HaroFramework.Tests.EditMode.asmdef
        *Tests.cs
      PlayMode/
        HaroFramework.Tests.PlayMode.asmdef
        *Tests.cs
```

### EditMode Test Assembly
```json
{
    "name": "HaroFramework.Tests.EditMode",
    "rootNamespace": "HaroFramework.Tests",
    "references": [
        "HaroFramework.Runtime",
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner"
    ],
    "includePlatforms": [
        "Editor"
    ],
    "excludePlatforms": [],
    "allowUnsafeCode": false,
    "overrideReferences": true,
    "precompiledReferences": [
        "nunit.framework.dll"
    ],
    "autoReferenced": false,
    "defineConstraints": [
        "UNITY_INCLUDE_TESTS"
    ]
}
```

### PlayMode Test Assembly
```json
{
    "name": "HaroFramework.Tests.PlayMode",
    "rootNamespace": "HaroFramework.Tests",
    "references": [
        "HaroFramework.Runtime",
        "UnityEngine.TestRunner",
        "UnityEditor.TestRunner"
    ],
    "includePlatforms": [],
    "excludePlatforms": [],
    "allowUnsafeCode": false,
    "overrideReferences": true,
    "precompiledReferences": [
        "nunit.framework.dll"
    ],
    "autoReferenced": false,
    "defineConstraints": [
        "UNITY_INCLUDE_TESTS"
    ]
}
```

## EditMode Test Template

```csharp
using NUnit.Framework;
using UnityEngine;

namespace HaroFramework.Tests
{
    /// <summary>
    /// Tests for [ClassName]
    /// </summary>
    public class ClassNameTests
    {
        private ClassName _systemUnderTest;

        [SetUp]
        public void SetUp()
        {
            // Arrange - runs before each test
            _systemUnderTest = new ClassName();
        }

        [TearDown]
        public void TearDown()
        {
            // Cleanup - runs after each test
            _systemUnderTest = null;
        }

        [Test]
        public void Method_Condition_ExpectedResult()
        {
            // Arrange
            var expectedValue = 42;

            // Act
            var result = _systemUnderTest.Method();

            // Assert
            Assert.AreEqual(expectedValue, result);
        }

        [Test]
        public void Method_WithInvalidInput_ThrowsException()
        {
            // Arrange
            var invalidInput = -1;

            // Act & Assert
            Assert.Throws<System.ArgumentException>(() =>
            {
                _systemUnderTest.Method(invalidInput);
            });
        }

        [Test]
        [TestCase(0, 0)]
        [TestCase(1, 1)]
        [TestCase(2, 4)]
        [TestCase(3, 9)]
        public void Method_WithDifferentInputs_ReturnsExpectedValues(int input, int expected)
        {
            // Act
            var result = _systemUnderTest.Square(input);

            // Assert
            Assert.AreEqual(expected, result);
        }
    }
}
```

## PlayMode Test Template

```csharp
using System.Collections;
using NUnit.Framework;
using UnityEngine;
using UnityEngine.TestTools;
using UnityEngine.SceneManagement;

namespace HaroFramework.Tests
{
    /// <summary>
    /// PlayMode tests for [ComponentName]
    /// </summary>
    public class ComponentNamePlayTests
    {
        private GameObject _gameObject;
        private ComponentName _component;

        [SetUp]
        public void SetUp()
        {
            // Create GameObject with component
            _gameObject = new GameObject("Test Object");
            _component = _gameObject.AddComponent<ComponentName>();
        }

        [TearDown]
        public void TearDown()
        {
            // Cleanup
            if (_gameObject != null)
                Object.Destroy(_gameObject);
        }

        [UnityTest]
        public IEnumerator Component_OnStart_InitializesCorrectly()
        {
            // Arrange - component created in SetUp

            // Act - wait for Start to be called
            yield return null;

            // Assert
            Assert.IsNotNull(_component);
            Assert.IsTrue(_component.IsInitialized);
        }

        [UnityTest]
        public IEnumerator Method_WithCoroutine_CompletesSuccessfully()
        {
            // Arrange
            var expectedValue = 10;

            // Act
            yield return _component.CoroutineMethod();

            // Assert
            Assert.AreEqual(expectedValue, _component.Value);
        }

        [UnityTest]
        public IEnumerator Component_AfterDelay_ChangesState()
        {
            // Arrange
            var initialState = _component.State;

            // Act - wait for 1 second
            yield return new WaitForSeconds(1.0f);

            // Assert
            Assert.AreNotEqual(initialState, _component.State);
        }

        [UnityTest]
        public IEnumerator Scene_LoadsCorrectly()
        {
            // Act
            var loadOperation = SceneManager.LoadSceneAsync("TestScene");
            yield return loadOperation;

            // Assert
            Assert.IsTrue(loadOperation.isDone);
            Assert.AreEqual("TestScene", SceneManager.GetActiveScene().name);
        }
    }
}
```

## Common Assert Methods

```csharp
// Equality
Assert.AreEqual(expected, actual);
Assert.AreNotEqual(notExpected, actual);

// Object reference
Assert.IsNull(obj);
Assert.IsNotNull(obj);
Assert.AreSame(expected, actual); // Same reference
Assert.AreNotSame(notExpected, actual);

// Boolean
Assert.IsTrue(condition);
Assert.IsFalse(condition);

// Numeric comparison
Assert.Greater(actual, expected);
Assert.GreaterOrEqual(actual, expected);
Assert.Less(actual, expected);
Assert.LessOrEqual(actual, expected);

// Float/Vector comparison (with tolerance)
Assert.AreEqual(expected, actual, 0.001f);
Assert.That(vector1, Is.EqualTo(vector2).Using(Vector3EqualityComparer.Instance));

// Exceptions
Assert.Throws<ExceptionType>(() => MethodThatThrows());
Assert.DoesNotThrow(() => SafeMethod());

// Collections
Assert.Contains(item, collection);
CollectionAssert.AreEqual(expectedCollection, actualCollection);
CollectionAssert.Contains(collection, item);
```

## Test Attributes

```csharp
[Test] // Standard test method
[UnityTest] // Returns IEnumerator for PlayMode tests
[TestCase(1, 2, 3)] // Parameterized test
[TestCase(4, 5, 9)]
[Repeat(10)] // Run test multiple times
[Timeout(5000)] // Test timeout in milliseconds
[Ignore("Reason")] // Skip this test
[Category("Performance")] // Organize tests
[Order(1)] // Control test execution order

// Setup and teardown
[OneTimeSetUp] // Runs once before all tests in class
[OneTimeTearDown] // Runs once after all tests in class
[SetUp] // Runs before each test
[TearDown] // Runs after each test
```

## Performance Testing

```csharp
using Unity.PerformanceTesting;

public class PerformanceTests
{
    [Test, Performance]
    public void Method_Performance_MeetsTargets()
    {
        Measure.Method(() =>
        {
            // Code to measure
            _system.ExpensiveMethod();
        })
        .WarmupCount(10)
        .MeasurementCount(100)
        .IterationsPerMeasurement(5)
        .GC()
        .Run();
    }

    [UnityTest, Performance]
    public IEnumerator GameObject_Instantiation_Performance()
    {
        using (Measure.Frames().Scope())
        {
            for (int i = 0; i < 100; i++)
            {
                var go = Object.Instantiate(prefab);
                yield return null;
                Object.Destroy(go);
            }
        }
    }
}
```

## Test Helpers

```csharp
namespace HaroFramework.Tests
{
    public static class TestHelpers
    {
        // Create GameObject with component
        public static T CreateComponent<T>() where T : Component
        {
            var go = new GameObject(typeof(T).Name);
            return go.AddComponent<T>();
        }

        // Load test prefab
        public static GameObject LoadTestPrefab(string name)
        {
            return Resources.Load<GameObject>($"TestPrefabs/{name}");
        }

        // Wait for condition
        public static IEnumerator WaitForCondition(System.Func<bool> condition, float timeout = 5f)
        {
            float elapsed = 0f;
            while (!condition() && elapsed < timeout)
            {
                elapsed += Time.deltaTime;
                yield return null;
            }

            if (elapsed >= timeout)
                throw new System.TimeoutException("Condition not met within timeout");
        }

        // Assert Vector3 approximately equal
        public static void AssertVector3Equal(Vector3 expected, Vector3 actual, float tolerance = 0.001f)
        {
            Assert.AreEqual(expected.x, actual.x, tolerance);
            Assert.AreEqual(expected.y, actual.y, tolerance);
            Assert.AreEqual(expected.z, actual.z, tolerance);
        }
    }
}
```

## Test-Driven Development (TDD)

### Red-Green-Refactor Cycle

1. **Red**: Write a failing test
```csharp
[Test]
public void Player_TakesDamage_ReducesHealth()
{
    var player = new Player { Health = 100 };
    player.TakeDamage(20);
    Assert.AreEqual(80, player.Health);
}
```

2. **Green**: Write minimal code to pass
```csharp
public class Player
{
    public int Health { get; set; }

    public void TakeDamage(int damage)
    {
        Health -= damage;
    }
}
```

3. **Refactor**: Improve code quality
```csharp
public void TakeDamage(int damage)
{
    if (damage < 0)
        throw new ArgumentException("Damage cannot be negative");

    Health = Mathf.Max(0, Health - damage);
}
```

## Mocking and Test Doubles

```csharp
// Interface for dependency injection
public interface IDamageCalculator
{
    int CalculateDamage(int baseDamage, float multiplier);
}

// Mock implementation for testing
public class MockDamageCalculator : IDamageCalculator
{
    public int CalculateDamage(int baseDamage, float multiplier)
    {
        return baseDamage; // Simplified for testing
    }
}

// Test with mock
[Test]
public void Player_WithMockCalculator_UsesMockDamage()
{
    var mockCalculator = new MockDamageCalculator();
    var player = new Player(mockCalculator);

    player.TakeDamage(20);

    Assert.AreEqual(80, player.Health);
}
```

## Continuous Integration

```csharp
// Command line test execution
// Run from command line or CI/CD pipeline
// unity -runTests -batchmode -projectPath /path/to/project
//      -testResults /path/to/results.xml
//      -testPlatform EditMode
```

## Best Practices

1. **Test Naming**: `MethodName_StateUnderTest_ExpectedBehavior`
2. **AAA Pattern**: Arrange, Act, Assert
3. **One Assert Per Test**: Focus on single behavior
4. **Test Independence**: Tests should not depend on each other
5. **Fast Tests**: Keep EditMode tests fast, use PlayMode only when necessary
6. **Test Coverage**: Aim for >80% code coverage on critical paths
7. **Clean Tests**: Use SetUp/TearDown for common initialization
8. **Readable Tests**: Tests are documentation

## Questions to Ask

Before writing tests:
1. What behavior needs to be tested?
2. EditMode or PlayMode test?
3. What are the edge cases?
4. What dependencies need mocking?
5. What's the expected code coverage?

## Output Format

1. Create test files in appropriate Tests/ directory
2. Set up test assembly definitions if needed
3. Include complete, runnable tests
4. Follow naming conventions
5. Add comments explaining test purpose
6. Provide instructions for running tests

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/haro7488) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
