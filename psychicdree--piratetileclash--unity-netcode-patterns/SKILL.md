---
name: unity-netcode-patterns
description: Common Unity Netcode for GameObjects patterns and solutions for Distributed Authority. Use when implementing network features, debugging sync issues, or working with NetworkBehaviour classes. Use when this capability is needed.
metadata:
  author: psychicdree
---

# Unity Netcode Patterns

## Distributed Authority Setup

### NetworkManager Configuration
- Use Distributed Authority topology
- Configure player prefab spawning
- Set up scene management for network

### NetworkBehaviour Basics
```csharp
public class GridManager : NetworkBehaviour
{
    public override void OnNetworkSpawn()
    {
        if (IsServer)
        {
            // Initialize on Session Owner
            InitializeGrid();
        }
    }
    
    public override void OnNetworkDespawn()
    {
        // Cleanup
    }
}
```

## NetworkVariable Patterns

### Simple Value Sync
```csharp
private NetworkVariable<int> health = new NetworkVariable<int>(
    default,
    NetworkVariableReadPermission.Everyone,
    NetworkVariableWritePermission.Server
);

// Update on server
if (IsServer)
{
    health.Value = newHealth;
}

// Read on all clients
void Update()
{
    if (health.Value != _lastHealth)
    {
        UpdateHealthUI(health.Value);
        _lastHealth = health.Value;
    }
}
```

### Struct Sync
```csharp
public struct PlayerStats : INetworkSerializable
{
    public int Health;
    public int Mana;
    
    public void NetworkSerialize<T>(BufferSerializer<T> serializer) where T : IReaderWriter
    {
        serializer.SerializeValue(ref Health);
        serializer.SerializeValue(ref Mana);
    }
}

private NetworkVariable<PlayerStats> stats = new NetworkVariable<PlayerStats>();
```

## NetworkList Patterns

### Collection Sync
```csharp
private NetworkList<TileData> _tileGrid;

public override void OnNetworkSpawn()
{
    _tileGrid = new NetworkList<TileData>();
    
    if (IsServer)
    {
        InitializeGrid();
    }
    
    _tileGrid.OnListChanged += HandleTileGridChanged;
}

private void HandleTileGridChanged(NetworkListEvent<TileData> changeEvent)
{
    switch (changeEvent.Type)
    {
        case NetworkListEvent<TileData>.EventType.Add:
            // New tile added
            break;
        case NetworkListEvent<TileData>.EventType.Remove:
            // Tile removed
            break;
        case NetworkListEvent<TileData>.EventType.Value:
            // Tile value changed
            break;
    }
}
```

## RPC Patterns

### ServerRpc - Client to Server
```csharp
[ServerRpc(RequireOwnership = false)]
public void ClaimTileServerRpc(int x, int y, ServerRpcParams rpcParams = default)
{
    if (!IsServer) return;
    
    ulong clientId = rpcParams.Receive.SenderClientId;
    
    // Validate and process
    if (ValidateTileClaim(x, y, clientId))
    {
        ClaimTile(x, y, clientId);
    }
}
```

### ClientRpc - Server to Clients
```csharp
[ClientRpc]
public void TileDestroyedClientRpc(int x, int y)
{
    // Execute on all clients
    SpawnDestructionParticles(x, y);
    PlayDestructionSound();
}
```

### Targeted ClientRpc
```csharp
[ClientRpc]
public void ShowVictoryClientRpc(ClientRpcParams rpcParams = default)
{
    // Only show to specific client
    UIManager.Instance.ShowVictoryScreen();
}
```

## Ownership Patterns

### Client Owns Character
```csharp
public class PlayerController : NetworkBehaviour
{
    void Update()
    {
        if (!IsOwner) return; // Only owner processes input
        
        HandleInput();
    }
    
    [ServerRpc]
    void MoveServerRpc(Vector3 position)
    {
        // Server validates movement
        transform.position = position;
    }
}
```

### Server Owns Grid
```csharp
public class GridManager : NetworkBehaviour
{
    void Update()
    {
        if (!IsServer) return; // Only server modifies grid
        
        ProcessGridUpdates();
    }
}
```

## Late Join Handling

### Sending State to Late Joiners
```csharp
public override void OnNetworkSpawn()
{
    if (IsServer)
    {
        // Late joiner - send current state
        if (NetworkManager.Singleton.ConnectedClients.Count > 1)
        {
            SendFullGridStateClientRpc();
        }
    }
}

[ClientRpc]
void SendFullGridStateClientRpc()
{
    // Receive full grid state
    InitializeGridFromState();
}
```

## Network Events

### Connection Events
```csharp
public override void OnNetworkSpawn()
{
    if (IsServer)
    {
        NetworkManager.Singleton.OnClientConnectedCallback += OnClientConnected;
        NetworkManager.Singleton.OnClientDisconnectCallback += OnClientDisconnected;
    }
}

void OnClientConnected(ulong clientId)
{
    Debug.Log($"[SessionManager] Client {clientId} connected");
    // Send current game state
}

void OnClientDisconnected(ulong clientId)
{
    Debug.Log($"[SessionManager] Client {clientId} disconnected");
    // Clean up client-specific data
}
```

## Common Solutions

### Synchronized Timers
```csharp
private NetworkVariable<float> turnTimer = new NetworkVariable<float>();

void Update()
{
    if (IsServer)
    {
        turnTimer.Value -= Time.deltaTime;
        if (turnTimer.Value <= 0)
        {
            EndTurn();
        }
    }
}
```

### Authority Validation
```csharp
[ServerRpc(RequireOwnership = false)]
public void PlayCardServerRpc(int cardId, ServerRpcParams rpcParams = default)
{
    if (!IsServer) return;
    
    ulong clientId = rpcParams.Receive.SenderClientId;
    
    // Validate player can play this card
    if (!CanPlayerPlayCard(clientId, cardId))
    {
        Debug.LogWarning($"[CardManager] Invalid card play from {clientId}");
        return;
    }
    
    // Execute card
    ExecuteCard(cardId, clientId);
}
```

## Performance Tips

### Minimize Network Updates
- Update NetworkVariables only when values change
- Batch NetworkList updates when possible
- Use ClientRpc for one-time events, NetworkVariable for frequent updates

### Optimize Message Size
- Keep RPC parameters small
- Use enums instead of strings when possible
- Compress data structures when needed

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/psychicdree) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
