---
name: game-servers
description: | Use when this capability is needed.
metadata:
  author: pluginagentmarketplace
---

# Game Servers

## Server Architecture Patterns

```
┌─────────────────────────────────────────────────────────────┐
│                    SERVER ARCHITECTURES                      │
├─────────────────────────────────────────────────────────────┤
│  DEDICATED SERVER:                                           │
│  • Server runs game simulation                              │
│  • Clients send inputs, receive state                       │
│  • Best security and consistency                            │
│  • Higher infrastructure cost                               │
├─────────────────────────────────────────────────────────────┤
│  LISTEN SERVER:                                              │
│  • One player hosts the game                                │
│  • Free infrastructure                                      │
│  • Host has advantage (no latency)                          │
│  • Session ends if host leaves                              │
├─────────────────────────────────────────────────────────────┤
│  RELAY SERVER:                                               │
│  • Routes packets between peers                             │
│  • No game logic on server                                  │
│  • Good for P2P with NAT traversal                         │
│  • Less secure than dedicated                               │
└─────────────────────────────────────────────────────────────┘
```

## Scalable Architecture

```
SCALABLE GAME BACKEND:
┌─────────────────────────────────────────────────────────────┐
│                     GLOBAL LOAD BALANCER                     │
│                            ↓                                 │
├─────────────────────────────────────────────────────────────┤
│                      GATEWAY SERVERS                         │
│            Authentication, Routing, Rate Limiting           │
│                            ↓                                 │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────┐  ┌─────────────┐  ┌─────────────┐        │
│  │ MATCHMAKING │  │   LOBBY     │  │   SOCIAL    │        │
│  │   SERVICE   │  │   SERVICE   │  │   SERVICE   │        │
│  └─────────────┘  └─────────────┘  └─────────────┘        │
│                            ↓                                 │
├─────────────────────────────────────────────────────────────┤
│              GAME SERVER ORCHESTRATOR                        │
│         (Spawns/despawns based on demand)                   │
│                            ↓                                 │
├─────────────────────────────────────────────────────────────┤
│  ┌─────────────────────────────────────────────────────┐   │
│  │ GAME SERVERS (Regional, Auto-scaled)                 │   │
│  │ [US-East] [US-West] [EU-West] [Asia] [Oceania]      │   │
│  └─────────────────────────────────────────────────────┘   │
│                            ↓                                 │
├─────────────────────────────────────────────────────────────┤
│                    DATABASE CLUSTER                          │
│  [Player Profiles] [Leaderboards] [Match History] [Items]  │
└─────────────────────────────────────────────────────────────┘
```

## Matchmaking System

```
MATCHMAKING FLOW:
┌─────────────────────────────────────────────────────────────┐
│  1. QUEUE: Player enters matchmaking queue                  │
│     → Store: skill rating, region, preferences             │
│                                                              │
│  2. SEARCH: Find compatible players                         │
│     → Same region (or expand after timeout)                │
│     → Similar skill (±100 MMR, expand over time)           │
│     → Compatible party sizes                               │
│                                                              │
│  3. MATCH: Form teams when criteria met                     │
│     → Balance teams by total MMR                           │
│     → Check for premade groups                             │
│                                                              │
│  4. PROVISION: Request game server                          │
│     → Orchestrator spawns or assigns server                │
│     → Wait for server ready                                │
│                                                              │
│  5. CONNECT: Send connection info to all players            │
│     → IP:Port or relay token                               │
│     → Timeout if player doesn't connect                    │
└─────────────────────────────────────────────────────────────┘
```

## Player Data Management

```csharp
// ✅ Production-Ready: Player Session
public class PlayerSession
{
    public string PlayerId { get; }
    public string SessionToken { get; }
    public DateTime CreatedAt { get; }
    public DateTime LastActivity { get; private set; }

    private readonly IDatabase _db;
    private readonly ICache _cache;

    public async Task<PlayerProfile> GetProfile()
    {
        // Try cache first
        var cached = await _cache.GetAsync<PlayerProfile>($"profile:{PlayerId}");
        if (cached != null)
        {
            return cached;
        }

        // Fall back to database
        var profile = await _db.GetPlayerProfile(PlayerId);

        // Cache for 5 minutes
        await _cache.SetAsync($"profile:{PlayerId}", profile, TimeSpan.FromMinutes(5));

        return profile;
    }

    public async Task UpdateStats(MatchResult result)
    {
        LastActivity = DateTime.UtcNow;

        // Update in database
        await _db.UpdatePlayerStats(PlayerId, result);

        // Invalidate cache
        await _cache.DeleteAsync($"profile:{PlayerId}");
    }
}
```

## Auto-Scaling Strategy

```
SCALING TRIGGERS:
┌─────────────────────────────────────────────────────────────┐
│  SCALE UP when:                                              │
│  • Queue time > 60 seconds                                  │
│  • Server utilization > 70%                                 │
│  • Approaching peak hours                                   │
│                                                              │
│  SCALE DOWN when:                                            │
│  • Server utilization < 30% for 15+ minutes                │
│  • Off-peak hours                                           │
│  • Allow graceful drain (don't kill active matches)        │
├─────────────────────────────────────────────────────────────┤
│  PRE-WARMING:                                                │
│  • Spin up servers before expected peak                    │
│  • Use historical data to predict demand                   │
│  • Keep warm pool for instant availability                 │
└─────────────────────────────────────────────────────────────┘
```

## 🔧 Troubleshooting

```
┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Long matchmaking times                             │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Expand skill range over time                              │
│ → Allow cross-region matching                               │
│ → Reduce minimum player count                               │
│ → Add bots to fill partial matches                          │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Server crashes during peak                         │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Pre-warm servers before peak                              │
│ → Increase max server instances                             │
│ → Add circuit breakers                                      │
│ → Implement graceful degradation                            │
└─────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────────────────┐
│ PROBLEM: Database bottleneck                                │
├─────────────────────────────────────────────────────────────┤
│ SOLUTIONS:                                                   │
│ → Add caching layer (Redis)                                 │
│ → Use read replicas                                         │
│ → Shard by player ID                                        │
│ → Queue non-critical writes                                 │
└─────────────────────────────────────────────────────────────┘
```

## Infrastructure Costs

| Scale | Players | Servers | Monthly Cost |
|-------|---------|---------|--------------|
| Small | 1K CCU | 10 | $500-1K |
| Medium | 10K CCU | 100 | $5K-10K |
| Large | 100K CCU | 1000 | $50K-100K |
| Massive | 1M+ CCU | 10000+ | $500K+ |

---

**Use this skill**: When building online backends, scaling systems, or implementing matchmaking.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pluginagentmarketplace) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
