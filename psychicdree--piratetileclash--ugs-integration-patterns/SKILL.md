---
name: ugs-integration-patterns
description: Unity Gaming Services integration patterns, service abstraction, and UGS API usage. Use when integrating UGS services, implementing authentication, Cloud Save, or multiplayer features. Use when this capability is needed.
metadata:
  author: psychicdree
---

# UGS Integration Patterns

## Service Interface Pattern

### Define Interface
```csharp
// Systems/IAuthenticationService.cs
public interface IAuthenticationService
{
    bool IsLoggedIn { get; }
    string PlayerId { get; }
    UniTask LoginAsync();
    UniTask LogoutAsync();
}
```

### Implement Service
```csharp
// Systems/AuthenticationServices.cs
public class UnityAuthenticationService : IAuthenticationService
{
    public bool IsLoggedIn => AuthenticationService.Instance.IsSignedIn;
    public string PlayerId => AuthenticationService.Instance.PlayerId;
    
    public async UniTask LoginAsync()
    {
        await AuthenticationService.Instance.SignInAnonymouslyAsync();
    }
    
    public async UniTask LogoutAsync()
    {
        await AuthenticationService.Instance.SignOutAsync();
    }
}
```

### Register Service
```csharp
// Boot/GameInitializer.cs
ServiceLocator.Global.Register<IAuthenticationService>(
    new UnityAuthenticationService()
);
```

### Use Service
```csharp
var authService = ServiceLocator.Global.Get<IAuthenticationService>();
if (!authService.IsLoggedIn)
{
    await authService.LoginAsync();
}
```

## Cloud Save Pattern

### Save Data
```csharp
public async UniTask SavePlayerDataAsync(PlayerData data)
{
    try
    {
        var saveData = new Dictionary<string, object>
        {
            { "PLAYER_DATA", data }
        };
        
        await CloudSaveService.Instance.Data.Player.SaveAsync(saveData);
        Debug.Log("[DataManager] Player data saved");
    }
    catch (Exception e)
    {
        Debug.LogError($"[DataManager] Save failed: {e.Message}");
    }
}
```

### Load Data
```csharp
public async UniTask<PlayerData> LoadPlayerDataAsync()
{
    try
    {
        var keys = new HashSet<string> { "PLAYER_DATA" };
        var data = await CloudSaveService.Instance.Data.Player.LoadAsync(keys);
        
        if (data.TryGetValue("PLAYER_DATA", out var item))
        {
            return item.Value.GetAs<PlayerData>();
        }
        
        return new PlayerData(); // Default
    }
    catch (Exception e)
    {
        Debug.LogError($"[DataManager] Load failed: {e.Message}");
        return new PlayerData();
    }
}
```

## Multiplayer Sessions Pattern

### Create Session
```csharp
public async UniTask<string> CreateSessionAsync(int maxPlayers)
{
    try
    {
        var sessionRequest = new SessionRequest
        {
            MaxPlayers = maxPlayers
        };
        
        var session = await MultiplayerService.Instance.CreateSessionAsync(sessionRequest);
        return session.SessionId;
    }
    catch (Exception e)
    {
        Debug.LogError($"[SessionManager] Create session failed: {e.Message}");
        throw;
    }
}
```

### Join Session
```csharp
public async UniTask JoinSessionAsync(string sessionId)
{
    try
    {
        await MultiplayerService.Instance.JoinSessionAsync(sessionId);
        Debug.Log($"[SessionManager] Joined session: {sessionId}");
    }
    catch (Exception e)
    {
        Debug.LogError($"[SessionManager] Join failed: {e.Message}");
        throw;
    }
}
```

## Lobby Pattern

### Create Lobby
```csharp
public async UniTask<string> CreateLobbyAsync(int maxPlayers)
{
    try
    {
        var lobbyRequest = new CreateLobbyRequest
        {
            MaxPlayers = maxPlayers,
            IsPrivate = false
        };
        
        var lobby = await LobbyService.Instance.CreateLobbyAsync(lobbyRequest);
        return lobby.LobbyCode;
    }
    catch (Exception e)
    {
        Debug.LogError($"[LobbyManager] Create lobby failed: {e.Message}");
        throw;
    }
}
```

### Join Lobby by Code
```csharp
public async UniTask JoinLobbyByCodeAsync(string code)
{
    try
    {
        var joinRequest = new JoinLobbyByCodeRequest { LobbyCode = code };
        var lobby = await LobbyService.Instance.JoinLobbyByCodeAsync(joinRequest);
        Debug.Log($"[LobbyManager] Joined lobby: {lobby.LobbyId}");
    }
    catch (Exception e)
    {
        Debug.LogError($"[LobbyManager] Join failed: {e.Message}");
        throw;
    }
}
```

## Friends Service Pattern

### Get Friends List
```csharp
public async UniTask<List<Friend>> GetFriendsAsync()
{
    try
    {
        var friends = await FriendsService.Instance.GetFriendsAsync();
        return friends.ToList();
    }
    catch (Exception e)
    {
        Debug.LogError($"[FriendsManager] Get friends failed: {e.Message}");
        return new List<Friend>();
    }
}
```

### Send Friend Request
```csharp
public async UniTask SendFriendRequestAsync(string playerId)
{
    try
    {
        var request = new SendFriendRequestRequest { PlayerId = playerId };
        await FriendsService.Instance.SendFriendRequestAsync(request);
        Debug.Log($"[FriendsManager] Friend request sent to {playerId}");
    }
    catch (Exception e)
    {
        Debug.LogError($"[FriendsManager] Send request failed: {e.Message}");
    }
}
```

## Error Handling Pattern

### Standard Error Handling
```csharp
public async UniTask<T> ExecuteUGSCallAsync<T>(Func<UniTask<T>> operation, T defaultValue)
{
    try
    {
        return await operation();
    }
    catch (RequestFailedException e)
    {
        Debug.LogError($"[ServiceName] UGS request failed: {e.Message}");
        Debug.LogError($"Error Code: {e.ErrorCode}, Status: {e.HttpStatusCode}");
        return defaultValue;
    }
    catch (Exception e)
    {
        Debug.LogError($"[ServiceName] Unexpected error: {e.Message}");
        return defaultValue;
    }
}
```

## Initialization Pattern

### Service Initialization
```csharp
public async UniTask InitializeAsync()
{
    try
    {
        if (UnityServices.State == ServicesInitializationState.Uninitialized)
        {
            await UnityServices.InitializeAsync();
        }
        
        // Initialize services
        await InitializeAuthenticationAsync();
        await InitializeCloudSaveAsync();
        // ...
        
        Debug.Log("[GamingServices] All services initialized");
    }
    catch (Exception e)
    {
        Debug.LogError($"[GamingServices] Initialization failed: {e.Message}");
        throw;
    }
}
```

## Platform-Specific Patterns

### Android - Google Play Games
```csharp
#if UNITY_ANDROID && !UNITY_EDITOR
public class GPGSAuthenticationService : IAuthenticationService
{
    public async UniTask LoginAsync()
    {
        await AuthenticationService.Instance.SignInWithGooglePlayGamesAsync();
    }
}
#endif
```

### Default - Anonymous
```csharp
public class UnityAuthenticationService : IAuthenticationService
{
    public async UniTask LoginAsync()
    {
        await AuthenticationService.Instance.SignInAnonymouslyAsync();
    }
}
```

## Service Availability Pattern

### Check Before Use
```csharp
public async UniTask SaveDataAsync()
{
    if (_gamingServices?.CloudSave == null)
    {
        Debug.LogWarning("[DataManager] CloudSave not available");
        return;
    }
    
    try
    {
        await _gamingServices.CloudSave.Data.Player.SaveAsync(data);
    }
    catch (Exception e)
    {
        Debug.LogError($"[DataManager] Save failed: {e.Message}");
    }
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/psychicdree) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
