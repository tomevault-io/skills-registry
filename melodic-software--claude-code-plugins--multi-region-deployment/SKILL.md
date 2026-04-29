---
name: multi-region-deployment
description: Use when designing globally distributed systems, multi-region architectures, or disaster recovery strategies. Covers region selection, active-active vs active-passive, data replication, and failover patterns.
metadata:
  author: melodic-software
---

# Multi-Region Deployment

Comprehensive guide to deploying applications across multiple geographic regions for availability, performance, and disaster recovery.

## When to Use This Skill

- Designing globally distributed applications
- Implementing disaster recovery (DR)
- Reducing latency for global users
- Meeting data residency requirements
- Achieving high availability (99.99%+)
- Planning failover strategies

## Multi-Region Fundamentals

### Why Multi-Region?

```text
Reasons for Multi-Region:

1. High Availability
   └── Survive region-wide failures
   └── Natural disasters, power outages
   └── Target: 99.99%+ uptime

2. Low Latency
   └── Serve users from nearest region
   └── Reduce round-trip time
   └── Better user experience

3. Data Residency
   └── GDPR, data sovereignty laws
   └── Keep data in specific countries
   └── Compliance requirements

4. Disaster Recovery
   └── Business continuity
   └── RTO/RPO requirements
   └── Regulatory requirements

Trade-offs:
+ Higher availability
+ Lower latency globally
+ Compliance capability
- Higher cost (2x-3x or more)
- Increased complexity
- Data consistency challenges
```

### Deployment Models

```text
Model 1: Active-Passive (DR)
┌─────────────────┐         ┌─────────────────┐
│  PRIMARY (Active)│         │ SECONDARY (Passive)│
│  ┌─────────────┐│         │  ┌─────────────┐│
│  │     App     ││   ──►   │  │    App      ││
│  │    (Live)   ││  Sync   │  │  (Standby)  ││
│  └─────────────┘│         │  └─────────────┘│
│  ┌─────────────┐│         │  ┌─────────────┐│
│  │     DB      ││   ──►   │  │    DB       ││
│  │  (Primary)  ││  Replic │  │  (Replica)  ││
│  └─────────────┘│         │  └─────────────┘│
└─────────────────┘         └─────────────────┘
    All traffic              Failover only

Model 2: Active-Active (Load Distributed)
┌─────────────────┐         ┌─────────────────┐
│   REGION A       │         │   REGION B       │
│  ┌─────────────┐│    ◄──► │  ┌─────────────┐│
│  │     App     ││  Users  │  │     App     ││
│  │   (Active)  ││  routed │  │   (Active)  ││
│  └─────────────┘│   by    │  └─────────────┘│
│  ┌─────────────┐│ location│  ┌─────────────┐│
│  │     DB      ││    ◄──► │  │     DB      ││
│  │  (Primary)  ││  Replic │  │  (Primary)  ││
│  └─────────────┘│   Both  │  └─────────────┘│
└─────────────────┘  ways   └─────────────────┘
  Serves Region A           Serves Region B

Model 3: Active-Active-Active (Global)
┌──────┐    ┌──────┐    ┌──────┐
│  US  │◄──►│  EU  │◄──►│ APAC │
│Active│    │Active│    │Active│
└──┬───┘    └──┬───┘    └──┬───┘
   │           │           │
   └───────────┼───────────┘
               │
         Global Load Balancer
         routes by location
```

## Region Selection

### Selection Criteria

```text
Region Selection Factors:

1. User Location
   □ Where are your users?
   □ Latency requirements per region?
   □ User concentration (80/20 rule)?

2. Compliance Requirements
   □ Data residency laws (GDPR, etc.)
   □ Government regulations
   □ Industry requirements (HIPAA, PCI)

3. Cloud Provider Availability
   □ Not all services in all regions
   □ Service feature parity
   □ Regional pricing differences

4. Network Connectivity
   □ Internet exchange points
   □ Direct connect options
   □ Cross-region latency

5. Disaster Risk
   □ Natural disaster patterns
   □ Political stability
   □ Infrastructure reliability

6. Cost
   □ Compute/storage pricing varies
   □ Data transfer costs (egress)
   □ Support availability
```

### Common Region Pairs

```text
Region Pair Strategy:

Americas:
- Primary: US East (N. Virginia)
- Secondary: US West (Oregon) or US East (Ohio)
- Distance: 2,500-3,000 km
- Latency: ~60ms

Europe:
- Primary: EU West (Ireland)
- Secondary: EU Central (Frankfurt) or EU West (London)
- Distance: ~1,000-1,500 km
- Latency: ~20-30ms

Asia Pacific:
- Primary: Singapore or Tokyo
- Secondary: Sydney or Mumbai
- Distance: 5,000-7,000 km
- Latency: ~100-150ms

Global Triad:
- US East + EU West + Singapore/Tokyo
- Covers most global users
- <100ms to 80%+ of users
```

## Data Replication

### Replication Patterns

```text
Pattern 1: Async Replication (Most Common)
Primary ──────► Replica
         lag:
         ms to seconds

+ Lower latency for writes
+ Primary not blocked by replica
- Potential data loss on failover (RPO > 0)
- Replication lag visible

Pattern 2: Sync Replication
Primary ◄─────► Replica
         both
         confirm

+ No data loss on failover (RPO = 0)
+ Strong consistency
- Higher write latency
- Availability coupled to both regions

Pattern 3: Semi-Sync Replication
Primary ──────► At least 1 Replica (sync)
        └────► Other Replicas (async)

+ Guaranteed durability for some replicas
+ Balance of latency and durability
- More complex failure handling
```

### Conflict Resolution

```text
Multi-Primary Conflict Resolution:

Scenario: Same record updated in two regions simultaneously

Resolution Strategies:

1. Last Write Wins (LWW)
   └── Timestamp-based
   └── Simple but can lose data
   └── Clock sync important

2. First Write Wins
   └── First committed wins
   └── Later writes rejected or queued
   └── Good for "create once" data

3. Application-Level Resolution
   └── Custom merge logic
   └── Most flexible
   └── Most complex

4. CRDTs (Conflict-free Replicated Data Types)
   └── Mathematically guaranteed convergence
   └── Counters, sets, maps
   └── Good for specific use cases

Best Practice:
- Design to avoid conflicts where possible
- Partition data by region when appropriate
- Use single-primary for conflict-sensitive data
```

## Failover Strategies

### Failover Types

```text
Failover Types:

1. DNS-Based Failover
   ┌─────────────────────────────────────────┐
   │  DNS Health Check                       │
   │  ├── Check primary every 10-30s        │
   │  ├── 3 consecutive failures = unhealthy│
   │  └── Update DNS to point to secondary  │
   └─────────────────────────────────────────┘

   RTO: 60-300 seconds (DNS TTL + propagation)
   Pros: Simple, works with any app
   Cons: Slow failover, DNS caching issues

2. Load Balancer Failover
   ┌─────────────────────────────────────────┐
   │  Global Load Balancer                   │
   │  ├── Continuous health checks          │
   │  ├── Instant routing changes           │
   │  └── No DNS propagation wait           │
   └─────────────────────────────────────────┘

   RTO: 10-60 seconds
   Pros: Fast, reliable
   Cons: Requires GLB, potential single point

3. Application-Level Failover
   ┌─────────────────────────────────────────┐
   │  Client/App Aware                       │
   │  ├── Client retries to alternate region│
   │  ├── SDK handles failover              │
   │  └── No infrastructure dependency      │
   └─────────────────────────────────────────┘

   RTO: 1-10 seconds
   Pros: Fastest, most control
   Cons: Requires client changes
```

### RTO and RPO

```text
Recovery Objectives:

RTO (Recovery Time Objective):
└── Maximum acceptable downtime
└── Time from failure to recovery
└── Drives failover automation investment

RPO (Recovery Point Objective):
└── Maximum acceptable data loss
└── Time between last backup and failure
└── Drives replication strategy

Common Targets:
┌──────────────┬──────────┬──────────┬───────────────────┐
│ Tier         │ RTO      │ RPO      │ Strategy          │
├──────────────┼──────────┼──────────┼───────────────────┤
│ Critical     │ <1 min   │ 0        │ Active-active     │
│              │          │          │ Sync replication  │
├──────────────┼──────────┼──────────┼───────────────────┤
│ High         │ <15 min  │ <1 min   │ Active-passive    │
│              │          │          │ Hot standby       │
├──────────────┼──────────┼──────────┼───────────────────┤
│ Medium       │ <4 hours │ <1 hour  │ Warm standby      │
│              │          │          │ Async replication │
├──────────────┼──────────┼──────────┼───────────────────┤
│ Low          │ <24 hours│ <24 hours│ Backup/Restore    │
│              │          │          │ Pilot light       │
└──────────────┴──────────┴──────────┴───────────────────┘
```

## Traffic Routing

### Global Load Balancing

```text
GLB Routing Policies:

1. Geolocation Routing
   └── Route by user's geographic location
   └── Europe users → EU region
   └── Fallback for unmapped locations

2. Latency-Based Routing
   └── Route to lowest latency region
   └── Based on real measurements
   └── Adapts to network conditions

3. Weighted Routing
   └── Split traffic by percentage
   └── Good for rollouts, testing
   └── Example: 90% primary, 10% secondary

4. Failover Routing
   └── Primary region until unhealthy
   └── Automatic switch to secondary
   └── Health check driven

Cloud Implementations:
- AWS: Route 53, Global Accelerator
- Azure: Traffic Manager, Front Door
- GCP: Cloud Load Balancing
- Cloudflare: Load Balancing
```

### Session Handling

```text
Session Affinity in Multi-Region:

Challenge: User session state across regions

Option 1: Sticky Sessions
└── User stays in same region for session
└── Failover loses session
└── Simple but limited DR

Option 2: Centralized Session Store
└── Session in Redis/database
└── All regions access same store
└── Adds latency, single point of failure

Option 3: Distributed Session Store
└── Redis Cluster across regions
└── Session replicated
└── Complex but resilient

Option 4: Stateless (JWT/Token)
└── Session in client-side token
└── No server-side state
└── Best for multi-region

Recommendation:
- Prefer stateless where possible
- If stateful, use distributed store
- Design for session loss on failover
```

## Database Patterns

### Database Deployment Options

```text
Option 1: Single Primary + Read Replicas
┌───────────────┐         ┌───────────────┐
│   US-EAST     │         │   EU-WEST     │
│  ┌─────────┐  │   ───►  │  ┌─────────┐  │
│  │ Primary │  │  Async  │  │ Replica │  │
│  │  (R/W)  │  │  Replic │  │  (Read) │  │
│  └─────────┘  │         │  └─────────┘  │
└───────────────┘         └───────────────┘
- Writes go to primary region
- Reads served locally
- Failover promotes replica

Option 2: Multi-Primary (Active-Active)
┌───────────────┐         ┌───────────────┐
│   US-EAST     │◄───────►│   EU-WEST     │
│  ┌─────────┐  │  Bi-dir │  ┌─────────┐  │
│  │ Primary │  │  Replic │  │ Primary │  │
│  │  (R/W)  │  │         │  │  (R/W)  │  │
│  └─────────┘  │         │  └─────────┘  │
└───────────────┘         └───────────────┘
- Writes accepted in both regions
- Conflict resolution required
- Complex but lowest latency

Option 3: Globally Distributed Database
┌─────────────────────────────────────────┐
│  CockroachDB / Spanner / YugabyteDB    │
│  ┌─────┐    ┌─────┐    ┌─────┐        │
│  │ US  │────│ EU  │────│ APAC│        │
│  └─────┘    └─────┘    └─────┘        │
│  Automatic sharding and replication    │
└─────────────────────────────────────────┘
- Database handles distribution
- Strong consistency available
- Higher latency for writes
```

## Testing and Validation

### Chaos Engineering for Multi-Region

```text
Multi-Region Chaos Tests:

1. Region Failover Test
   □ Fail primary region completely
   □ Measure failover time
   □ Verify data integrity
   □ Test user experience

2. Network Partition Test
   □ Block inter-region communication
   □ Verify split-brain handling
   □ Test conflict resolution

3. Partial Failure Test
   □ Fail subset of services in region
   □ Test degraded operation
   □ Verify monitoring/alerting

4. Data Replication Lag Test
   □ Introduce artificial lag
   □ Test application behavior
   □ Verify consistency expectations

5. Failback Test
   □ Restore failed region
   □ Test data sync
   □ Test traffic redistribution

Schedule:
- Failover tests: Monthly
- Full DR drill: Quarterly
- Chaos experiments: Weekly
```

## Best Practices

```text
Multi-Region Best Practices:

1. Design for Failure
   □ Assume any region can fail
   □ No single points of failure
   □ Automated failover
   □ Regular testing

2. Data Strategy
   □ Define consistency requirements
   □ Choose appropriate replication
   □ Plan for conflicts
   □ Consider data residency

3. Observability
   □ Cross-region metrics
   □ Distributed tracing
   □ Centralized logging
   □ Region-aware alerting

4. Cost Management
   □ Right-size standby resources
   □ Use reserved capacity wisely
   □ Monitor data transfer costs
   □ Consider traffic patterns

5. Operational Readiness
   □ Runbooks for failover
   □ Regular DR drills
   □ On-call training
   □ Post-incident reviews
```

## Related Skills

- `latency-optimization` - Reducing global latency
- `distributed-consensus` - Consistency patterns
- `cdn-architecture` - Edge caching for multi-region
- `chaos-engineering-fundamentals` - Testing resilience

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/melodic-software) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
