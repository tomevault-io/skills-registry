---
name: testing-patterns
description: Testing patterns, test case creation, and QA strategies for Pirate Tile Clash. Use when creating tests, debugging issues, or ensuring code quality. Use when this capability is needed.
metadata:
  author: psychicdree
---

# Testing Patterns

## Unit Test Patterns

### MonoBehaviour Testing
```csharp
[TestFixture]
public class GridManagerTests
{
    private GameObject _gameObject;
    private GridManager _gridManager;
    
    [SetUp]
    public void Setup()
    {
        _gameObject = new GameObject();
        _gridManager = _gameObject.AddComponent<GridManager>();
    }
    
    [TearDown]
    public void TearDown()
    {
        Object.DestroyImmediate(_gameObject);
    }
    
    [Test]
    public void InitializeGrid_Creates5x5Grid()
    {
        _gridManager.InitializeGrid();
        
        Assert.AreEqual(25, _gridManager.TileCount);
    }
}
```

### Async Method Testing
```csharp
[UnityTest]
public IEnumerator LoadDataAsync_LoadsPlayerData()
{
    var dataManager = new DataManager();
    
    yield return dataManager.LoadDataAsync().ToCoroutine();
    
    Assert.IsNotNull(dataManager.PlayerData);
}
```

## Integration Test Patterns

### Network Integration Test
```csharp
[UnityTest]
public IEnumerator TwoClients_CanClaimDifferentTiles()
{
    // Setup two network instances
    var client1 = SetupNetworkClient("Client1");
    var client2 = SetupNetworkClient("Client2");
    
    yield return new WaitForSeconds(1f); // Wait for connection
    
    // Client 1 claims tile (0, 0)
    client1.GridManager.RequestClaimTile(0, 0);
    yield return new WaitForSeconds(0.5f);
    
    // Client 2 claims tile (4, 4)
    client2.GridManager.RequestClaimTile(4, 4);
    yield return new WaitForSeconds(0.5f);
    
    // Verify both tiles claimed
    Assert.IsTrue(client1.GridManager.IsTileClaimed(0, 0));
    Assert.IsTrue(client2.GridManager.IsTileClaimed(4, 4));
}
```

## Test Case Structure

### Standard Test Case
```csharp
[Test]
public void PlayCard_InsufficientMana_ReturnsFalse()
{
    // Arrange
    var cardManager = new CardManager();
    var card = new CardData { ManaCost = 5 };
    var playerMana = 3;
    
    // Act
    var result = cardManager.ValidateCard(card, playerMana);
    
    // Assert
    Assert.IsFalse(result);
}
```

### Edge Case Testing
```csharp
[Test]
public void ClaimTile_InvalidCoordinates_ReturnsFalse()
{
    // Test boundary conditions
    Assert.IsFalse(_gridManager.RequestClaimTile(-1, 0)); // Negative X
    Assert.IsFalse(_gridManager.RequestClaimTile(0, -1)); // Negative Y
    Assert.IsFalse(_gridManager.RequestClaimTile(5, 0));  // Out of bounds X
    Assert.IsFalse(_gridManager.RequestClaimTile(0, 5));  // Out of bounds Y
}
```

## Network Test Scenarios

### Simultaneous Actions
```csharp
[UnityTest]
public IEnumerator SimultaneousTileClaims_OneSucceeds()
{
    var client1 = SetupClient();
    var client2 = SetupClient();
    
    yield return new WaitForSeconds(1f);
    
    // Both try to claim same tile simultaneously
    client1.GridManager.RequestClaimTile(2, 2);
    client2.GridManager.RequestClaimTile(2, 2);
    
    yield return new WaitForSeconds(1f);
    
    // Only one should succeed (authority decides)
    var client1Owns = client1.GridManager.IsTileOwnedBy(2, 2, client1.ClientId);
    var client2Owns = client2.GridManager.IsTileOwnedBy(2, 2, client2.ClientId);
    
    Assert.IsTrue(client1Owns ^ client2Owns); // XOR - exactly one true
}
```

### Late Join Test
```csharp
[UnityTest]
public IEnumerator LateJoiningPlayer_ReceivesGridState()
{
    // Start match with one player
    var host = SetupHost();
    host.GridManager.RequestClaimTile(0, 0);
    yield return new WaitForSeconds(1f);
    
    // Second player joins
    var lateJoiner = SetupClient();
    yield return new WaitForSeconds(2f);
    
    // Late joiner should see claimed tile
    Assert.IsTrue(lateJoiner.GridManager.IsTileClaimed(0, 0));
}
```

## Performance Test Patterns

### Frame Rate Test
```csharp
[UnityTest]
public IEnumerator CardEffect_Maintains60FPS()
{
    var cardManager = SetupCardManager();
    
    var frameTimes = new List<float>();
    var startTime = Time.time;
    
    while (Time.time - startTime < 5f)
    {
        frameTimes.Add(Time.deltaTime);
        yield return null;
    }
    
    var avgFrameTime = frameTimes.Average();
    var avgFPS = 1f / avgFrameTime;
    
    Assert.GreaterOrEqual(avgFPS, 55f); // Allow some variance
}
```

### Memory Test
```csharp
[UnityTest]
public IEnumerator LongPlaySession_NoMemoryLeaks()
{
    var initialMemory = GC.GetTotalMemory(false);
    
    // Simulate long play session
    for (int i = 0; i < 100; i++)
    {
        PlayCard();
        yield return new WaitForSeconds(0.1f);
    }
    
    GC.Collect();
    var finalMemory = GC.GetTotalMemory(false);
    var memoryIncrease = finalMemory - initialMemory;
    
    // Memory increase should be reasonable (< 10MB)
    Assert.Less(memoryIncrease, 10 * 1024 * 1024);
}
```

## Test Utilities

### Test Helpers
```csharp
public static class TestHelpers
{
    public static NetworkClient SetupNetworkClient(string name)
    {
        var go = new GameObject(name);
        var client = go.AddComponent<NetworkClient>();
        // Setup network client
        return client;
    }
    
    public static CardData CreateTestCard(int manaCost)
    {
        return new CardData
        {
            Id = 1,
            Name = "Test Card",
            ManaCost = manaCost,
            Type = CardType.Offensive
        };
    }
}
```

## Test Organization

### Test Categories
```csharp
[Category("Gameplay")]
[Test]
public void GridSystem_WorksCorrectly() { }

[Category("Network")]
[Test]
public void NetworkSync_WorksCorrectly() { }

[Category("Performance")]
[Test]
public void FrameRate_MaintainsTarget() { }
```

## Mock Patterns

### Mock UGS Service
```csharp
public class MockGamingServices : IGamingServices
{
    public bool IsLoggedIn { get; set; } = true;
    public PlayerData MockPlayerData { get; set; }
    
    public async UniTask<PlayerData> LoadDataAsync()
    {
        await UniTask.Delay(100); // Simulate network delay
        return MockPlayerData ?? new PlayerData();
    }
}
```

## Best Practices

### Test Naming
- Use descriptive names: `MethodName_Scenario_ExpectedResult`
- Include context in test name
- Group related tests

### Test Isolation
- Each test should be independent
- Clean up after tests
- Don't rely on test execution order

### Test Coverage
- Test happy paths
- Test error cases
- Test edge cases
- Test boundary conditions

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/psychicdree) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
