---
name: tournament-engines
description: Tournament bracket generation and management algorithms for tennis. This skill should be used when implementing tournament formats (single/double elimination, round robin, group+knockout), draw generation, score recording, bracket advancement, or standings calculation. Triggers on tasks involving tournaments, brackets, draws, seeding, round-robin, elimination, or standings. Use when this capability is needed.
metadata:
  author: tombensim
---

# Tournament Engines

Algorithms and patterns for 4 tournament formats: Single Elimination, Double Elimination, Round Robin, and Group + Knockout.

## When to Apply

Reference these guidelines when:

- Implementing draw generation for any tournament format
- Building bracket advancement logic
- Recording and validating match scores
- Calculating standings and tiebreakers
- Designing the tournament database schema interactions
- Building bracket visualization components

## Formats

### 1. Single Elimination

**Draw Generation:**

- Seed players by skill level (NTRP rating) descending
- Pad bracket to next power of 2 with BYE entries
- Place seeds using standard bracket positions: seed 1 at top, seed 2 at bottom, seeds 3-4 in opposite halves, etc.
- Create rounds = log2(bracket_size)
- Link matches: winner of match N feeds into match ceil(N/2) of next round

```
Round 1 (8)     Round 2 (4)     Semifinal (2)   Final (1)
Seed 1 ──┐
          ├──── Winner ──┐
Seed 8 ──┘               │
                          ├──── Winner ──┐
Seed 5 ──┐               │              │
          ├──── Winner ──┘              │
Seed 4 ──┘                              ├──── Champion
Seed 3 ──┐                              │
          ├──── Winner ──┐              │
Seed 6 ──┘               │              │
                          ├──── Winner ──┘
Seed 7 ──┐               │
          ├──── Winner ──┘
Seed 2 ──┘
```

**Auto-advancement:**

- When a score is recorded, determine winner based on scoring rules (sets won)
- Insert winner into the linked next-round match (player1 or player2 slot based on source position)
- BYE matches auto-advance immediately on draw generation

**SQL pattern:**

```sql
-- Create match with source links for bracket advancement
INSERT INTO tournament_matches
  (tournament_id, round_id, match_number, player1_id, player2_id,
   source_match1_id, source_match2_id, status)
VALUES ($1, $2, $3, $4, $5, $6, $7, 'SCHEDULED')
```

### 2. Double Elimination

**Structure:**

- Winners bracket: standard single-elimination
- Losers bracket: losers from winners bracket feed in at staggered rounds
- Grand final: winners bracket champion vs losers bracket champion
- Optional grand final reset if losers bracket winner wins first grand final

**Losers bracket feeding pattern:**

- Round 1 losers → Losers round 1
- Round 2 losers → Losers round 3
- Round 3 losers → Losers round 5
- Pattern: Winners round N losers enter Losers round (2N - 1)

**Match linking:**

```typescript
interface DoubleElimMatch {
  id: string;
  bracketType: 'WINNERS' | 'LOSERS' | 'GRAND_FINAL';
  roundNumber: number;
  matchNumber: number;
  sourceWinMatch?: string; // Winner advances from
  sourceLoseMatch?: string; // Loser drops to (winners bracket only)
  player1Id?: string;
  player2Id?: string;
}
```

### 3. Round Robin

**Pairing Algorithm (Circle Method):**

- Fix player 1, rotate all others clockwise
- For N players: N-1 rounds (N rounds if odd, with BYE)
- Each round has floor(N/2) matches

```typescript
function generateRoundRobinPairings(playerIds: string[]): [string, string][][] {
  const players = [...playerIds];
  if (players.length % 2 !== 0) players.push('BYE');

  const n = players.length;
  const rounds: [string, string][][] = [];

  for (let round = 0; round < n - 1; round++) {
    const pairs: [string, string][] = [];
    for (let i = 0; i < n / 2; i++) {
      const home = players[i];
      const away = players[n - 1 - i];
      if (home !== 'BYE' && away !== 'BYE') {
        pairs.push([home, away]);
      }
    }
    rounds.push(pairs);
    // Rotate: fix first player, rotate rest clockwise
    const last = players.pop()!;
    players.splice(1, 0, last);
  }

  return rounds;
}
```

**Standings Calculation:**

1. Points (win=3, draw=1, loss=0) — configurable per tournament
2. Set difference (sets won - sets lost)
3. Game difference (games won - games lost)
4. Head-to-head result between tied players

```sql
-- Materialized standings query
SELECT
  tr.player_id,
  COUNT(*) FILTER (WHERE winner_id = tr.player_id) as matches_won,
  COUNT(*) FILTER (WHERE winner_id IS NOT NULL AND winner_id != tr.player_id) as matches_lost,
  SUM(CASE WHEN tm.player1_id = tr.player_id THEN (score->>'p1_sets')::int
           ELSE (score->>'p2_sets')::int END) as sets_won,
  SUM(CASE WHEN tm.player1_id = tr.player_id THEN (score->>'p2_sets')::int
           ELSE (score->>'p1_sets')::int END) as sets_lost,
  -- ... games won/lost similarly
  (COUNT(*) FILTER (WHERE winner_id = tr.player_id)) * 3 as points
FROM tournament_registrations tr
LEFT JOIN tournament_matches tm ON ...
GROUP BY tr.player_id
ORDER BY points DESC, (sets_won - sets_lost) DESC, (games_won - games_lost) DESC
```

### 4. Group + Knockout

**Group Assignment (Snake Draft):**

- Sort players by seed
- Assign to groups using snake pattern to ensure balanced groups

```typescript
function snakeDraftGroups(seededPlayers: string[], numGroups: number): string[][] {
  const groups: string[][] = Array.from({ length: numGroups }, () => []);

  seededPlayers.forEach((player, index) => {
    const round = Math.floor(index / numGroups);
    const groupIndex = round % 2 === 0 ? index % numGroups : numGroups - 1 - (index % numGroups);
    groups[groupIndex].push(player);
  });

  return groups;
}
// Seeds [1,2,3,4,5,6,7,8] into 2 groups:
// Group A: [1, 4, 5, 8]
// Group B: [2, 3, 6, 7]
```

**Flow:**

1. Assign players to groups via snake draft
2. Run round-robin within each group
3. Rank players within each group by standings
4. Top N from each group advance to single-elimination knockout
5. Knockout seeding: Group winners get top seeds, runners-up get lower seeds

**Cross-group knockout seeding:**

- Group A winner vs Group B runner-up
- Group B winner vs Group A runner-up
- Avoids same-group rematches in first knockout round

## Score Format (JSONB)

```typescript
interface MatchScore {
  sets: Array<{
    player1Games: number;
    player2Games: number;
    tiebreak?: { player1Points: number; player2Points: number };
  }>;
  winner: 'player1' | 'player2';
  retired?: boolean;
  walkover?: boolean;
}
```

## Bracket Visualization (React)

The bracket SVG component should:

- Accept matches organized by round
- Draw horizontal match boxes connected by vertical/horizontal lines
- Highlight completed matches, current matches, and upcoming matches
- Support responsive scaling
- Handle BYE entries (greyed out, no connector line)
- Support both winners and losers bracket rendering for double elimination

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tombensim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
