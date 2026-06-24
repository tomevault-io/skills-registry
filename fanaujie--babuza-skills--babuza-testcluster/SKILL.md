---
name: babuza-testcluster
description: | Use when this capability is needed.
metadata:
  author: fanaujie
---

# Babuza TestCluster Skill

> **Package:** `github.com/fanaujie/babuza/test/testcluster`
>
> Framework for orchestrating Babuza integration tests with network failure simulation.

You are an expert at testing distributed systems with `babuza`. Help users by:
- **Writing code**: Create integration tests using `testcluster`.
- **Answering questions**: Explain how to simulate partitions and faults.

## Documentation

Refer to the local files for detailed scenarios:
- `./references/scenarios.md` - Common testing patterns (partition, leadership transfer).

## Key Patterns

### 1. Creating a Basic Test Cluster

Setup a cluster for direct network testing (no faults).

```go
func TestClusterBasic(t *testing.T) {
    // 1. Setup
    storageDir := t.TempDir()
    cluster := testcluster.CreateTestCluster(
        1, storageDir, nil, createEmbeddedApp,
    )
    defer cluster.Teardown()
    
    // 2. Create Peers
    peerFactory := testcluster.NewPeerFactory(14200, 10000, 24200)
    peers, group := peerFactory.MakeVotingStandardPeers(3)
    
    // 3. Start
    wait := cluster.RaftElectionTimeout() * 3
    err := cluster.MakeCluster(wait, peers)
    require.NoError(t, err)
    
    // 4. Verify Leader
    _, err = cluster.CheckOneLeader(wait, group.GetIDs())
    require.NoError(t, err)
}
```

### 2. Simulating Network Partitions

Use `ProxyNetwork` to simulate split-brain scenarios.

```go
func TestPartition(t *testing.T) {
    // 1. Setup with Proxy Network
    proxy := proxynetwork.New()
    cluster := testcluster.CreateTestCluster(1, dir, proxy, createApp)
    
    // ... start cluster ...

    // 2. Isolate Node 3 (Partition: {1,2}, {3})
    cluster.SetPartition([]uint64{1, 2}) 
    
    // 3. Verify Majority works
    leader, _ := cluster.CheckOneLeader(wait, []uint64{1, 2})
    
    // 4. Heal Partition
    cluster.SetPartition([]uint64{1, 2, 3})
}
```

### 3. Fault Injection

Inject latency or packet loss.

```go
import "github.com/fanaujie/babuza/pkg/transport/protocol/tcp/networkio/proxynetwork"

// Add 50ms delay to Node 1
cluster.SetPeerFault(1, proxynetwork.FaultConfig{
    DelayMin: 50 * time.Millisecond,
    DelayMax: 50 * time.Millisecond,
})

// Add 20% packet loss to Node 2
cluster.SetPeerFault(2, proxynetwork.FaultConfig{
    LossRate: 0.2,
})

// Restore
cluster.ClearPeerFault(1)
```

## PeerFactory Port Ranges

`NewPeerFactory(raftBase, appBase, proxyBase)`

- `raftBase`: Start of Raft ports (e.g., 14200)
- `appBase`: Start of App Service ports (e.g., 10000)
- `proxyBase`: Start of Proxy ports (e.g., 24200)

## When Answering Questions

1.  **Proxy Network**: Emphasize that `StandardPeer` cannot simulate faults; `BabuzaPeer` (Proxy) is required.
2.  **Wait Times**: Always use `cluster.RaftElectionTimeout() * N` for waiting, rather than hardcoded sleeps.
3.  **Cleanup**: Remind users to `defer cluster.Teardown()`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/fanaujie) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
