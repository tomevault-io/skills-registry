---
name: distributed-systems
description: Production-grade distributed systems skill for consensus protocols, replication, partitioning, and consistency models Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Distributed Systems Skill

> **Purpose**: Atomic skill for distributed systems patterns with formal correctness guarantees.

## Skill Identity

| Attribute | Value |
|-----------|-------|
| **Scope** | Consensus, Replication, Partitioning |
| **Responsibility** | Single: Distributed coordination patterns |
| **Invocation** | `Skill("distributed-systems")` |

## Parameter Schema

### Input Validation
```yaml
parameters:
  distributed_context:
    type: object
    required: true
    properties:
      problem_type:
        type: string
        enum: [consensus, replication, partitioning, transaction, conflict_resolution]
        required: true
      cluster_config:
        type: object
        properties:
          nodes: { type: integer, minimum: 1, maximum: 1000 }
          failure_tolerance: { type: integer, minimum: 0 }
          regions: { type: array, items: { type: string } }
        required: [nodes]
      consistency_requirement:
        type: string
        enum: [linearizable, sequential, causal, eventual]
        default: eventual
      network_assumptions:
        type: object
        properties:
          latency_ms: { type: integer, minimum: 1 }
          partition_probability: { type: number, minimum: 0, maximum: 1 }
          byzantine: { type: boolean, default: false }

validation_rules:
  - name: "quorum_possible"
    rule: "nodes >= 2 * failure_tolerance + 1"
    error: "Cannot tolerate f failures with fewer than 2f+1 nodes"
  - name: "byzantine_nodes"
    rule: "!byzantine || nodes >= 3 * failure_tolerance + 1"
    error: "Byzantine tolerance requires 3f+1 nodes"
```

### Output Schema
```yaml
output:
  type: object
  properties:
    protocol:
      type: object
      properties:
        name: { type: string }
        description: { type: string }
        correctness_guarantee: { type: string }
    configuration:
      type: object
      properties:
        quorum_size: { type: integer }
        timeout_ms: { type: integer }
        heartbeat_ms: { type: integer }
    failure_scenarios:
      type: array
      items:
        type: object
        properties:
          scenario: { type: string }
          behavior: { type: string }
          recovery: { type: string }
```

## Core Patterns

### Consensus Algorithms
```
Raft (Crash Fault Tolerant):
├── Leader Election
│   ├── Term-based voting
│   ├── Randomized timeout: 150-300ms
│   └── At most one leader per term
├── Log Replication
│   ├── Append-only entries
│   ├── Commit on majority ack
│   └── Log matching property
└── Safety Invariants
    ├── Election Safety
    ├── Leader Append-Only
    ├── Log Matching
    ├── Leader Completeness
    └── State Machine Safety

Paxos (Multi-Paxos):
├── Phase 1: Prepare
│   ├── Proposer: Send prepare(n)
│   └── Acceptor: Promise or reject
├── Phase 2: Accept
│   ├── Proposer: Send accept(n, v)
│   └── Acceptor: Accept or reject
└── Phase 3: Learn
    └── Learners: Apply committed value

PBFT (Byzantine Fault Tolerant):
├── Requires: 3f + 1 nodes
├── Phases: Pre-prepare → Prepare → Commit
├── Tolerates: f malicious nodes
└── Use: Blockchain, untrusted environments
```

### Replication Strategies
```
Synchronous:
├── Write waits for all replicas
├── Guarantees: Strong consistency
├── Trade-off: Higher latency
└── Formula: latency = max(replica_latencies)

Asynchronous:
├── Write returns after primary
├── Guarantees: Eventual consistency
├── Trade-off: Potential data loss
└── Risk: RPO > 0

Semi-Synchronous:
├── Write waits for at least one replica
├── Guarantees: Durability with quorum
├── Trade-off: Balance of consistency/latency
└── Common: MySQL semi-sync, PostgreSQL sync replicas
```

### Partitioning Schemes
```
Consistent Hashing:
├── Virtual nodes for balance
├── Minimal redistribution on change
├── Formula: position = hash(key) mod ring_size
└── Rebalance: Only k/n keys move

Range Partitioning:
├── Ordered data locality
├── Supports range queries
├── Risk: Hot spots
└── Mitigation: Split busy ranges

Directory-Based:
├── Centralized routing table
├── Maximum flexibility
├── Trade-off: Single point of failure
└── Mitigation: Replicated directory
```

## Retry Logic

### Distributed Operation Retry
```yaml
retry_config:
  consensus_operations:
    max_attempts: 10
    initial_delay_ms: 50
    max_delay_ms: 10000
    multiplier: 2.0
    jitter_factor: 0.25

  idempotency:
    required: true
    key_format: "{request_id}:{operation_type}"
    dedup_window_seconds: 300

  retry_on:
    - LEADER_NOT_FOUND
    - QUORUM_UNREACHABLE
    - TIMEOUT
    - NETWORK_PARTITION_HEALING

  abort_on:
    - INVALID_TERM
    - DUPLICATE_REQUEST
    - PERMANENT_FAILURE
```

## Logging & Observability

### Log Format
```yaml
log_schema:
  level: { type: string }
  timestamp: { type: string, format: ISO8601 }
  skill: { type: string, value: "distributed-systems" }
  node_id: { type: string }
  term: { type: integer }
  event:
    type: string
    enum:
      - election_started
      - leader_elected
      - log_replicated
      - commit_applied
      - partition_detected
      - partition_healed
      - conflict_resolved
  context: { type: object }

examples:
  - level: INFO
    event: leader_elected
    context: { node_id: "node-3", term: 42 }

  - level: WARN
    event: partition_detected
    context: { isolated_nodes: ["node-2", "node-5"] }

  - level: INFO
    event: conflict_resolved
    context: { key: "user:123", strategy: "LWW" }
```

### Metrics
```yaml
metrics:
  - name: leader_elections_total
    type: counter
    labels: [result]  # success, timeout, split_vote

  - name: replication_lag_seconds
    type: gauge
    labels: [follower_id]

  - name: consensus_latency_seconds
    type: histogram
    labels: [operation_type]
    buckets: [0.001, 0.005, 0.01, 0.05, 0.1, 0.5]

  - name: partition_events_total
    type: counter
    labels: [type]  # detected, healed
```

## Troubleshooting

### Common Issues

| Issue | Cause | Resolution |
|-------|-------|------------|
| Split-brain | Network partition | Implement proper fencing |
| Leader flapping | Unstable network | Increase election timeout |
| Stale reads | Replication lag | Route to leader or read-repair |
| Deadlock | Lock ordering | Timeout + retry with backoff |
| Data divergence | Concurrent writes | Implement conflict resolution |

### Debug Checklist
```
□ Quorum size correct (n/2 + 1)?
□ Clock skew within bounds?
□ Network partition tested?
□ Fencing mechanism in place?
□ Idempotency guaranteed?
□ Recovery procedure documented?
```

## Unit Test Templates

### Consensus Tests
```python
# test_distributed_systems.py

def test_quorum_calculation():
    assert calculate_quorum(3) == 2
    assert calculate_quorum(5) == 3
    assert calculate_quorum(7) == 4

def test_failure_tolerance_validation():
    # 5 nodes can tolerate 2 failures
    result = validate_cluster(nodes=5, failure_tolerance=2)
    assert result.valid == True

    # 5 nodes cannot tolerate 3 failures
    result = validate_cluster(nodes=5, failure_tolerance=3)
    assert result.valid == False
    assert "2f+1" in result.error

def test_byzantine_tolerance():
    # PBFT requires 3f+1 nodes
    result = validate_cluster(
        nodes=4, failure_tolerance=1, byzantine=True
    )
    assert result.valid == True  # 3*1+1 = 4

    result = validate_cluster(
        nodes=4, failure_tolerance=2, byzantine=True
    )
    assert result.valid == False  # 3*2+1 = 7 needed

def test_consistent_hashing():
    ring = ConsistentHashRing(virtual_nodes=100)
    ring.add_node("node-1")
    ring.add_node("node-2")

    key = "user:123"
    node = ring.get_node(key)
    assert node in ["node-1", "node-2"]

    # Minimal redistribution
    ring.add_node("node-3")
    # Only ~1/3 of keys should move
```

### Replication Tests
```python
def test_sync_replication():
    result = simulate_sync_replication(
        replicas=3,
        latencies_ms=[10, 20, 30]
    )
    assert result.total_latency_ms == 30  # max of all

def test_async_replication_lag():
    result = simulate_async_replication(
        write_rate=1000,
        apply_rate=800,
        duration_seconds=60
    )
    assert result.lag_seconds > 0
    assert result.lag_messages == 200 * 60  # 200/s gap

def test_conflict_resolution_lww():
    v1 = {"value": "A", "timestamp": 1000}
    v2 = {"value": "B", "timestamp": 1001}
    result = resolve_conflict_lww(v1, v2)
    assert result.value == "B"  # Later timestamp wins
```

## Version History

| Version | Date | Changes |
|---------|------|---------|
| 2.0.0 | 2025-01 | Production-grade rewrite with formal validation |
| 1.0.0 | 2024-12 | Initial release |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
