---
name: pirate-tile-clash-codebase
description: Understands the Pirate Tile Clash Unity project architecture, code structure, and development plan. Use when working on this codebase, implementing features, or when the user references Assets/Game/Scripts or the dev plan. Use when this capability is needed.
metadata:
  author: psychicdree
---

# Pirate Tile Clash Codebase Guide

## Quick Reference

**Project**: Fast-paced 1v1 turn-based tactical duel on a destructible 5×5 grid  
**Architecture**: Multi-scene Unity with Distributed Authority multiplayer  
**Dev Plan**: `Assets/Game/Docs/Pirate Tile Clash Dev Plan.html`  
**Code Root**: `Assets/Game/Scripts/`

## Code Architecture

### Folder Structure

```
Assets/Game/Scripts/
├── Boot/              # SC_Boot startup - one-time initialization
├── Core/              # Core systems (Managers, Player, Extensions)
│   ├── Managers/      # AudioManager, DataManager, SessionManager, etc.
│   ├── Player/        # UnityClient, Hero, HealthComponent, ManaComponent
│   └── Extensions/    # GameObjectExtensions and utilities
├── Data/              # PlayerData and ScriptableObjects
├── Gameplay/          # SC_GameplayBase - GridManager, CardManager, PlayerController
├── Integrations/      # Third-party SDKs (Firebase, UnityGamingServices)
├── Systems/           # Service interfaces (Authentication, Gaming, Lobby, Matchmaker)
├── UI/                # SC_AppShell + Overlays - UIManager, HUDManager
└── Utilities/         # SceneManagement, ServiceLocator, StatsModifier
```

### Key Architectural Patterns

#### 1. Service Locator Pattern
- Use `UnityServiceLocator` for dependency injection
- Register services in `GameInitializer.cs` (Boot scene)
- Access via `ServiceLocator.Global.Get<T>()`

```csharp
// ✅ GOOD - Register in GameInitializer
ServiceLocator.Global.Register<IGamingServices>(new UnityGamingServices());

// ✅ GOOD - Access via ServiceLocator
var gamingServices = ServiceLocator.Global.Get<IGamingServices>();
```

#### 2. Manager Pattern
- Managers are singletons with `DontDestroyOnLoad`
- Located in `Core/Managers/` or scene-specific locations
- Examples: `DataManager`, `AudioManager`, `SessionManager`, `FriendsManager`

```csharp
// ✅ GOOD - Singleton pattern
public class DataManager : MonoBehaviour
{
    public static DataManager Instance { get; private set; }
    
    private void Awake()
    {
        if (Instance != null && Instance != this)
        {
            Destroy(gameObject);
            return;
        }
        Instance = this;
        DontDestroyOnLoad(gameObject);
    }
}
```

#### 3. Scene Management
- Multi-scene architecture using Scene Groups
- Scene Groups defined in `Utilities/SceneManagement/SceneGroups/`
- Use `SceneGroupManager` to load/unload scene groups

**Scene Hierarchy:**
- `SC_Boot` - One-time initialization, unloads after systems ready
- `SC_PersistentSystems` - Global managers (persists across gameplay)
- `SC_AppShell` - Main menu UI (persists)
- `SC_GameplayBase` - NetworkManager, GridManager, CardManager
- `SC_World_Gameplay` - 5×5 tile grid, environment
- `SC_Overlay_HUD` - In-game HUD
- `SC_Overlay_Popups` - Victory/Defeat screens

#### 4. Unity Gaming Services Integration
- All UGS services accessed via `IGamingServices` interface
- Authentication: Anonymous or Google Play Games
- Cloud Save: Player data persistence
- Multiplayer: Session management via `SessionManager`
- Lobbies: Friend matching via `LobbyManager`

```csharp
// ✅ GOOD - Use interface abstraction
private IGamingServices _gamingServices;
_gamingServices = ServiceLocator.Global.Get<IGamingServices>();
await _gamingServices.Auth.LoginAsync();
```

#### 5. Network Architecture
- **Topology:** Distributed Authority
- **Ownership:** Session Owner validates tiles; clients control own character
- **Synchronization:** `NetworkList<TileData>` for grid, `NetworkVariables` for stats
- Use `NetworkBehaviour` for networked components

#### 6. Async/Await Pattern
- Use `UniTask` (Cysharp.Threading.Tasks) for async operations
- All UGS calls should be async
- Proper error handling with try-catch

```csharp
// ✅ GOOD - Async pattern with error handling
public async UniTask LoadDataAsync()
{
    try
    {
        var data = await _gamingServices.CloudSave.Data.Player.LoadAsync(...);
        // Process data
    }
    catch (Exception e)
    {
        Debug.LogError($"[ClassName] Failed to load: {e.Message}");
    }
}
```

## Development Plan Context

### Game Loop
1. Two pirates spawn on opposite corners of 5×5 grid
2. Each turn: Select 1 power card (heal, damage, trap, movement)
3. Move adjacent or use card ability
4. Simultaneous action resolution
5. Repeat until: center chest reached OR opponent defeated OR timer expires

### Key Systems

#### Grid System (`GridManager.cs`)
- Manages 5×5 tile grid state
- Uses `NetworkList<TileData>` for synchronization
- Validates tile claims (authority-based)
- Updates material states (Green→Yellow→Red)
- Spawns destruction particles on tile removal

#### Card System (`CardManager.cs`)
- 15 power cards total
- Card types: Healing, Offensive, Movement, Utility
- Mana cost system
- VFX/SFX triggering on card play
- Simultaneous turn resolution

#### Player System (`UnityClient.cs`)
- Central player data management
- ELO-based matchmaking
- Player data persistence via Cloud Save
- Lobby and matchmaking integration

## Coding Standards

### Namespaces
- Use `Game.*` namespace structure matching folder hierarchy
- Examples: `Game.Boot`, `Game.Core`, `Game.Managers`, `Game.UI`, `Game.Systems`

### Naming Conventions
- **Classes:** PascalCase (e.g., `GridManager`, `PlayerController`)
- **Methods:** PascalCase (e.g., `LoadDataAsync`, `CreateOrJoinSessionAsync`)
- **Private fields:** camelCase with underscore prefix (e.g., `_gamingServices`, `_lobbyService`)
- **Public properties:** PascalCase (e.g., `Instance`, `PlayerData`)

### Documentation
- Use XML documentation comments for public APIs
- Include `<summary>` tags explaining purpose
- Document responsibilities in class-level comments

```csharp
/// <summary>
/// Manages persistent game data across sessions.
/// - Handles loading and saving of player profiles.
/// - Manages local settings and preferences.
/// </summary>
public class DataManager : MonoBehaviour
{
    // ...
}
```

### Error Handling
- Always wrap async UGS calls in try-catch
- Log errors with class name prefix: `[ClassName] Error message`
- Return default values on failure (e.g., `new PlayerData()`)

### Debug Logging
- Use consistent format: `[ClassName] Message`
- Use `Debug.Log` for info, `Debug.LogError` for errors
- Include context in error messages

## When Adding New Code

### New Manager
1. Create in `Core/Managers/` or appropriate scene folder
2. Implement singleton pattern with `DontDestroyOnLoad`
3. Register with ServiceLocator if needed
4. Add XML documentation

### New Gameplay System
1. Create in `Gameplay/` folder
2. Inherit from `NetworkBehaviour` if networked
3. Use `NetworkList` or `NetworkVariable` for sync
4. Follow Distributed Authority patterns

### New UI Component
1. Create in `UI/` folder
2. Integrate with `UIManager` or `HUDManager`
3. Use scene-appropriate overlay (HUD vs Popups)

### New Service Integration
1. Create interface in `Systems/` folder
2. Implement in `Integrations/` or `Systems/` folder
3. Register in `GameInitializer.cs`
4. Access via ServiceLocator

## References

- **Dev Plan:** `Assets/Game/Docs/Pirate Tile Clash Dev Plan.html`
- **Code Architecture:** `Assets/Game/Scripts/`
- **Scene Management:** `Assets/Game/Scripts/Utilities/SceneManagement/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/psychicdree) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
