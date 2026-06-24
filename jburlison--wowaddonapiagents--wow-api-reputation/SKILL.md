---
name: wow-api-reputation
description: Complete reference for WoW Retail Reputation, Faction, Major Factions, Paragon, and Neighborhood Initiative APIs. Covers C_Reputation (faction info, standings, watched faction, headers, friendship reps, paragon), C_MajorFactions (Dragonflight+ renown factions, renown levels, rewards), C_NeighborhoodInitiative (12.0.0 housing neighborhood reputation), faction expansion data, and reputation-related events. Use when working with reputation tracking, faction standings, renown systems, paragon rewards, friendship reputations, or neighborhood initiatives. Use when this capability is needed.
metadata:
  author: jburlison
---

# Reputation API (Retail — Patch 12.0.0)

Comprehensive reference for reputation, faction, major factions, paragon, and neighborhood initiative APIs.

> **Source:** https://warcraft.wiki.gg/wiki/World_of_Warcraft_API
> **Current as of:** Patch 12.0.0 (Build 65655) — January 28, 2026
> **Scope:** Retail only.

---

## Scope

- **C_Reputation** — Faction info, standings, watched faction, headers
- **C_MajorFactions** — Renown factions (Dragonflight+)
- **Friendship Reputations** — Friendship-style rep (Tillers, etc.)
- **Paragon** — Paragon reputation (post-max)
- **C_NeighborhoodInitiative** — Housing neighborhood rep (12.0.0)

---

## C_Reputation — Core Reputation System

### Faction Info

| Function | Returns | Description |
|----------|---------|-------------|
| `C_Reputation.GetNumFactions()` | `numFactions` | Number of factions in list |
| `C_Reputation.GetFactionDataByIndex(index)` | `factionData` | Faction data at index |
| `C_Reputation.GetFactionDataByID(factionID)` | `factionData` | Faction data by ID |
| `C_Reputation.IsFactionActive(factionID)` | `isActive` | Is faction active? |
| `C_Reputation.IsMajorFaction(factionID)` | `isMajor` | Is major faction? |
| `C_Reputation.IsAccountWideReputation(factionID)` | `isAccountWide` | Account-wide rep? |
| `C_Reputation.GetFactionParagonInfo(factionID)` | `currentValue, threshold, questID, hasRewardPending, tooLowLevelForParagon` | Paragon info |
| `C_Reputation.IsFactionParagon(factionID)` | `isParagon` | Has paragon? |
| `C_Reputation.RequestFactionParagonPreloadRewardData(factionID)` | — | Preload paragon data |

### Faction Data Fields

The `factionData` table contains:
- `factionID` — Unique faction ID
- `name` — Faction name
- `description` — Description text
- `reaction` — Standing index (1=Hated to 8=Exalted)
- `currentReactionThreshold` — Min rep for current standing
- `nextReactionThreshold` — Rep needed for next standing
- `currentStanding` — Current rep value
- `atWarWith` — At war? (PvP hostile)
- `canToggleAtWar` — Can toggle at war?
- `isChild` — Is sub-faction?
- `isHeader` — Is a header row?
- `isHeaderWithRep` — Header that has rep?
- `isCollapsed` — Is header collapsed?
- `isWatched` — Is watched faction?
- `hasBonusRepGain` — Has rep bonus?
- `canSetInactive` — Can set inactive?

### Watched Faction

| Function | Returns | Description |
|----------|---------|-------------|
| `C_Reputation.GetWatchedFactionData()` | `factionData` | Watched faction info |
| `C_Reputation.SetWatchedFactionByIndex(index)` | — | Set watched by index |

### Faction List Management

| Function | Returns | Description |
|----------|---------|-------------|
| `C_Reputation.ExpandFactionHeader(index)` | — | Expand header |
| `C_Reputation.CollapseFactionHeader(index)` | — | Collapse header |
| `C_Reputation.SetFactionActive(index)` | — | Set faction active |
| `C_Reputation.SetFactionInactive(index)` | — | Set faction inactive |
| `C_Reputation.ToggleAtWar(index)` | — | Toggle at war |

### Standing Names

| Index | Standing |
|-------|----------|
| 1 | Hated |
| 2 | Hostile |
| 3 | Unfriendly |
| 4 | Neutral |
| 5 | Friendly |
| 6 | Honored |
| 7 | Revered |
| 8 | Exalted |

---

## C_MajorFactions — Renown System

Major factions use renown levels instead of traditional reputation standings.

| Function | Returns | Description |
|----------|---------|-------------|
| `C_MajorFactions.GetMajorFactionData(factionID)` | `majorFactionData` | Major faction info |
| `C_MajorFactions.GetMajorFactionIDs(expansionID)` | `factionIDs` | Major factions for expansion |
| `C_MajorFactions.GetCurrentRenownLevel(factionID)` | `renownLevel` | Current renown level |
| `C_MajorFactions.GetRenownLevels(factionID)` | `levels` | All renown levels |
| `C_MajorFactions.GetRenownRewardsForLevel(factionID, renownLevel)` | `rewards` | Rewards at level |
| `C_MajorFactions.HasMaximumRenown(factionID)` | `hasMax` | At max renown? |
| `C_MajorFactions.IsWeeklyRenownCapped(factionID)` | `isCapped` | Weekly cap reached? |
| `C_MajorFactions.GetMajorFactionRenownInfo(factionID)` | `renownInfo` | Renown progress info |

### Major Faction Data Fields

- `factionID` — Faction ID
- `name` — Faction name
- `celebrationSoundKit` — Sound on level up
- `renownLevel` — Current renown level
- `renownReputationEarned` — Rep earned toward next level
- `renownLevelThreshold` — Rep needed for next level
- `textureKit` — UI texture kit
- `expansionID` — Which expansion
- `isUnlocked` — Is faction unlocked?
- `unlockDescription` — How to unlock

---

## Friendship Reputations

Some factions use friendship instead of traditional reputation.

| Function | Returns | Description |
|----------|---------|-------------|
| `C_Reputation.GetFriendshipReputation(factionID)` | `friendshipData` | Friendship rep info |
| `C_Reputation.IsFactionFriendship(factionID)` | `isFriendship` | Uses friendship? |

### Friendship Data Fields

- `friendshipFactionID` — Faction ID
- `standing` — Current standing text (e.g., "Good Friend")
- `maxRep` — Max rep for current tier
- `reputation` — Current rep value
- `nextThreshold` — Next tier threshold
- `text` — Standing description
- `texture` — Standing icon
- `reaction` — Reaction index
- `reversedColor` — Reverse progress bar color?

---

## C_NeighborhoodInitiative — Housing Neighborhood (12.0.0)

New in 12.0.0 for the player housing system.

| Function | Returns | Description |
|----------|---------|-------------|
| `C_NeighborhoodInitiative.GetCurrentInitiative()` | `initiativeInfo` | Current initiative |
| `C_NeighborhoodInitiative.GetInitiativeProgress()` | `progress` | Progress info |
| `C_NeighborhoodInitiative.GetInitiativeRewards()` | `rewards` | Initiative rewards |

---

## Common Patterns

### List All Factions with Standing

```lua
local numFactions = C_Reputation.GetNumFactions()
for i = 1, numFactions do
    local data = C_Reputation.GetFactionDataByIndex(i)
    if data and not data.isHeader then
        local standingNames = {"Hated","Hostile","Unfriendly","Neutral","Friendly","Honored","Revered","Exalted"}
        local standing = standingNames[data.reaction] or "Unknown"
        print(data.name, standing, data.currentStanding)
    end
end
```

### Check Renown Level

```lua
local function GetRenownProgress(factionID)
    if C_Reputation.IsMajorFaction(factionID) then
        local data = C_MajorFactions.GetMajorFactionData(factionID)
        if data then
            print(data.name, "Renown:", data.renownLevel)
            print("Progress:", data.renownReputationEarned, "/", data.renownLevelThreshold)
            return data.renownLevel
        end
    end
    return nil
end
```

### Check Paragon Reward

```lua
local function CheckParagonReward(factionID)
    if C_Reputation.IsFactionParagon(factionID) then
        local currentValue, threshold, questID, hasReward = 
            C_Reputation.GetFactionParagonInfo(factionID)
        if hasReward then
            print("Paragon reward available for faction", factionID)
        end
        return hasReward
    end
    return false
end
```

---

## Key Events

| Event | Payload | Description |
|-------|---------|-------------|
| `UPDATE_FACTION` | — | Reputation changed |
| `QUEST_LOG_UPDATE` | — | Quest/rep update (shared) |
| `MAJOR_FACTION_RENOWN_LEVEL_CHANGED` | factionID, newRenownLevel, oldRenownLevel | Renown level up |
| `MAJOR_FACTION_UNLOCKED` | factionID | Major faction unlocked |
| `UPDATE_EXPANSION_LEVEL` | — | Expansion level changed |

---

## Gotchas & Restrictions

1. **Headers in faction list** — `C_Reputation.GetFactionDataByIndex()` returns headers. Check `isHeader` to skip.
2. **Major factions vs traditional** — Check `C_Reputation.IsMajorFaction()` to determine which API to use.
3. **Paragon is post-exalted** — `GetFactionParagonInfo()` only works for factions at Exalted (or max renown for major factions).
4. **Friendship rep display** — Friendship factions show custom standing names. Use `GetFriendshipReputation()` for proper display text.
5. **Account-wide rep** — Some 12.0.0 reps are account-wide. Check `IsAccountWideReputation()`.
6. **Collapsed headers** — Collapsed headers hide child factions from the indexed list. Expand before iterating.
7. **Faction IDs are stable** — Unlike indices, factionIDs are stable and can be persisted in SavedVariables.
8. **Neighborhood initiative** — New 12.0.0 system; APIs may evolve.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jburlison) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
