---
name: ascii-wireframing
description: Starter patterns and conventions for ASCII wireframes in game design. Use when creating screen layouts, UI mockups, spatial maps, encounter arenas, menu structures, or flow diagrams during game design sessions. Provides a toolkit of box-drawing characters, common primitives, and genre-specific examples as inspiration — not rules. Each project builds its own legend.yaml as the source of truth. Use when this capability is needed.
metadata:
  author: smileynet
---

# ASCII Wireframing Toolkit

Starter patterns for low-fidelity game wireframes. These are **building blocks, not rules** — a top-down RPG needs completely different conventions than a side-scroller. Use what fits, adapt what doesn't, invent what's missing.

## When to Wireframe

| Situation | What to Draw | Why |
|---|---|---|
| **Screen layout** | Full screen with regions marked | Establishes spatial hierarchy and information density |
| **UI state change** | Before/after of the same screen | Shows what changes on player input |
| **Spatial layout** | Room, level section, or world map | Tests navigation and sight lines |
| **Encounter arena** | Combat or puzzle space with entities placed | Validates spacing, threat placement, escape routes |
| **Menu / HUD** | Interface elements with content | Checks information load and access patterns |
| **Flow diagram** | Screens connected by arrows | Maps player journey through menus or game states |

**When NOT to wireframe:** Don't wireframe particle effects, animations, color palettes, or anything that's about polish rather than structure. "A satisfying effect plays" is enough during simulation.

## Starter Toolkit

### Box Drawing — UI Panels and Boundaries

```
┌──────────┐    ╔══════════╗    ┌──────┬──────┐
│  single  │    ║  double  ║    │ left │right │
│  border  │    ║  border  ║    │      │      │
└──────────┘    ╚══════════╝    └──────┴──────┘

Corners:  ┌ ┐ └ ┘ (single)    ╔ ╗ ╚ ╝ (double)
Lines:    ─ │ (single)        ═ ║ (double)
Joins:    ┬ ┴ ├ ┤ ┼           ╦ ╩ ╠ ╣ ╬
```

Use single borders for most panels. Reserve double borders for emphasis (active selection, important UI, primary window).

### Common Primitives

These are **starter suggestions**. Your project's `legend.yaml` overrides everything here.

| Symbol | Common Use | Notes |
|---|---|---|
| `@` | Player character | Traditional roguelike convention |
| `#` | Wall / solid block | Dense, reads as impassable |
| `.` | Floor / empty space | Light, reads as walkable |
| `~` | Water / liquid | Wavy reads as fluid |
| `*` | Item / collectible | Stands out against floor |
| `!` | Important / alert | Reads as attention-grabbing |
| `?` | Unknown / interactable | Invites investigation |
| `^` | Spike / hazard / upward | Pointy reads as dangerous |
| `>` `<` | Door / passage / direction | Arrows for flow |
| `X` | Enemy / danger | Bold, reads as threat |
| `$` | Currency / treasure | Universal money symbol |
| `+` | Health / positive | Medical cross association |
| `=` | Bridge / platform | Horizontal and solid |

### Shading and Fill

```
Dense:   ████████  (full block — solid walls, filled areas)
Medium:  ▓▓▓▓▓▓▓▓  (dark shade — semi-opaque, fog of war edge)
Light:   ░░░░░░░░  (light shade — transparent, fog of war)
Empty:   ........  (dots — open floor, traversable)
```

## The legend.yaml Workflow

Every project develops its own symbol conventions. The `legend.yaml` file is the per-project source of truth.

### How It Works

1. **First wireframe:** The agent proposes symbols based on the game's needs, drawing from the starter toolkit above or inventing new ones. Output the legend alongside the wireframe.

2. **Legend grows organically:** As new elements appear in simulation, add them to the legend. Each wireframe includes its legend so it's self-contained.

3. **User overrides welcome:** The user can change any symbol at any time. Their preference wins — update the legend and re-render if needed.

4. **Legend format:**

```yaml
# legend.yaml — Project symbol conventions
# Source of truth for all wireframes in this project

player: "@"
wall: "#"
floor: "."
enemy_basic: "g"      # goblin
enemy_elite: "G"      # elite goblin
door_locked: "D"
door_open: "/"
chest: "C"
npc_friendly: "N"
# ... grows as the project evolves
```

### Inline Legend Convention

Every wireframe should include a legend block so it reads independently:

```
┌─────────────────────────────┐
│ . . . . . # # # . . . . .  │
│ . @ . . . # C # . . X . .  │
│ . . . . . . D . . . . . .  │
│ . . . * . . . . . . g . .  │
└─────────────────────────────┘

Legend: @ Player  # Wall  . Floor  C Chest
        D Locked door  X Elite enemy  g Goblin  * Item
```

## Genre-Specific Inspiration

These examples show how different genres use ASCII differently. **Adapt, don't copy** — your game has its own needs.

### Side-Scroller / Platformer

```
                    *  *
          *     ════════
     @  ════
════════      ^^^^      ════════════
############  ####  ##  ############

Legend: @ Player  = Platform  # Ground
        ^ Spike   * Collectible
```

Conventions that work for platformers:
- Gravity matters — ground at bottom, platforms above
- Vertical space shows jump arcs and fall danger
- Hazards (`^`) below platforms show risk/reward

### Top-Down / RPG

```
##########  ########
#........#  #......#
#..@.....+--+..C...#
#........#  #...g..#
#.*......#  #......#
###+######  ########
   |
   |    ~~~~
---+--- ~N~~
 .....  ~~~~
 ..*..
 .....

Legend: @ Player  # Wall  . Floor  + Door
        * Item  C Chest  g Goblin  N NPC
        ~ Water  - Path  | Path
```

Conventions that work for top-down:
- Rooms as bounded rectangles connected by doors
- Corridors as `|` and `-` between rooms
- Terrain types use visually distinct fills

### Menu / HUD

```
╔═══════════════════════════════════╗
║  ADVENTURE GAME        ♥♥♥  $127 ║
╠═══════════════════════════════════╣
║                                   ║
║   (game area here)                ║
║                                   ║
╠═══════════════════════════════════╣
║ [Sword]  [Shield]  [Potion x3]   ║
╚═══════════════════════════════════╝

Legend: ♥ Health  $ Gold
        [ ] Inventory slot
```

Conventions that work for HUD:
- Double border (`╔═╗`) for outer frame
- Sections separated by horizontal rules (`╠═╣`)
- Brackets `[ ]` for interactive slots

### Inventory / Grid

```
╔══════════════════════════╗
║  INVENTORY         6/20  ║
╠══════════════════════════╣
║ [Sword ✦] [Shield  ]    ║
║ [Potion  ] [Key  ✦ ]    ║
║ [Scroll  ] [Ring    ]    ║
║ [       ] [       ]     ║
╠══════════════════════════╣
║ ✦ = Equipped             ║
╚══════════════════════════╝
```

Conventions that work for inventory:
- Grid cells as `[ ]` blocks with content
- State markers (`✦` equipped, `!` new) inside cells
- Capacity shown as `used/total`

### Flow Diagram

```
┌─────────┐     ┌─────────┐     ┌─────────┐
│  Title  │────→│  World  │────→│ Combat  │
│  Screen │     │   Map   │     │  Scene  │
└─────────┘     └────┬────┘     └────┬────┘
                     │               │
                     ↓               ↓
                ┌─────────┐     ┌─────────┐
                │  Shop   │     │ Game    │
                │         │     │ Over    │
                └─────────┘     └─────────┘

Arrows: ───→ player-initiated transition
        ---→ system-initiated transition
```

## Guidelines

### Sizing

- **Screen layouts:** 30-60 characters wide fits most contexts
- **Room maps:** Scale to fit — 10x10 is fine for a small room, 30x20 for a dungeon floor
- **HUD mockups:** Show relative proportions, not pixel-perfect sizing
- **Don't over-detail:** If a wireframe needs more than ~40 lines, split it into focused sections

### Readability

- Leave whitespace between distinct regions
- Align elements to a visible grid when possible
- Use consistent indentation within bordered panels
- Label ambiguous elements — if a symbol could mean two things, add a legend entry

### Iteration

- Wireframes are disposable — redraw freely as the design evolves
- Version wireframes by keeping both "before" and "after" when making significant layout changes
- Reference the legend.yaml so symbols stay consistent across wireframes in the same session

## See Also

- **simulation-guide** — Turn structure that uses these wireframes during Wizard of Oz simulation `(see simulation-guide → Turn Structure)`
- **scoping** — Vertical slice planning for what to prototype `(see scoping/vertical-slices.md → Vertical Slice Decomposition)`
- **scenario-walkthrough** — 5-Beat Structure that wireframes illustrate `(see scenario-walkthrough)`
- **design-frameworks** — Game feel principles for what wireframes should convey `(see design-frameworks)`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/smileynet) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
