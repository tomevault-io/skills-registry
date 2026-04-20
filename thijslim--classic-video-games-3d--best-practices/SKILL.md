---
name: best-practices
description: Accumulated best practices, lessons learned, and coding standards for the 3D platformer project. Updated by the learning agent after each feature cycle. Use when this capability is needed.
metadata:
  author: thijslim
---

# Best Practices

Accumulated knowledge from building **Super Mario 3D Web Edition**. This file is continuously updated by the learning agent.

---

## Code Architecture

### Game Object Design
- **One class per file** in `src/game/objects/`
- **Config interface** in the same file, exported alongside the class
- **Constructor pattern:** `constructor(engine, config) → super(engine) → this.config = config → this.create()`
- **Guard clause:** Always start `update()` with `if (!this.isActive) return;`

### State Management
- Keep game state on the owning object (Mario owns coins/stars/lives)
- Use TypeScript `enum` for state machines (not string literals)
- HUD reads state — never writes it

### Engine Boundaries
- Game objects should only interact with the engine through the `GameEngine` API
- Don't access `scene`, `physicsWorld`, or `renderer` directly
- Use `this.engine.addToScene()` / `this.engine.addPhysicsBody()` for registration

## Visual Design

### Material Selection
| Use Case | Material | Key Properties |
|----------|----------|----------------|
| Solid objects | `MeshStandardMaterial` | color, roughness: 0.8, metalness: 0.1 |
| Metallic/shiny | `MeshStandardMaterial` | metalness: 0.8, roughness: 0.2 |
| Glowing | `MeshStandardMaterial` | + emissive, emissiveIntensity |
| Transparent overlay | `MeshBasicMaterial` | transparent: true, opacity: 0.15 |
| Shadow decal | `MeshBasicMaterial` | color: 0x000000, transparent, depthWrite: false |

### Geometry Segment Counts
- Character parts: 8-12 segments (good enough for small features)
- Large objects: 16 segments (pipes, cylinders)
- Tiny details (eyes, pupils): 6-8 segments
- Ground/platforms: no segments needed (BoxGeometry)

### Color Palette (Mario style)
- Red: `0xFF0000` (Mario, hat)
- Blue: `0x0000CC` (overalls)
- Green: `0x4CAF50` (grass), `0x388E3C` (pipes), `0x2E7D32` (foliage)
- Brown: `0x8B4513` (enemies), `0x5D4037` (wood), `0x795548` (stone steps)
- Gold: `0xFFD700` (coins)
- Gray: `0x9E9E9E` / `0xBDBDBD` (castle, stone)
- Sky: `0x87CEEB`

## Physics Design

### Body Mass Guide
| Entity Type | Mass | fixedRotation | Notes |
|-------------|------|---------------|-------|
| Player | 1 | true | linearDamping: 0.1 |
| Static platform | 0 | N/A | Default static body |
| Collectible | 0 | N/A | isTrigger: true |
| Enemy (patrol) | 0 | N/A | Move via position update |
| Projectile | 0.1-0.5 | false | Apply velocity/force |
| Moving platform | 0 | N/A | Kinematic — update position |

### Collision Shape Sizing
- Physics shapes should **approximately** match visuals — not exact
- Slightly smaller collision shapes feel better for platforming (player fits through gaps)
- Use `CANNON.Sphere` for round objects, `CANNON.Box` for blocky ones
- `CANNON.Cylinder` for pipes and columns

## World Building

### Object Placement
- Use array-driven placement for repeating objects (coins, enemies)
- Generate positions with `Array.from({ length: N }, (_, i) => ...)` for patterns
- Use trigonometric functions for circular arrangements
- Keep objects inside the ground platform bounds (40×40 default)

### Level Design Principles
- Ground level at y=0 (ground platform top surface)
- Floating platforms at y=3, 5, 7, 9+ (progressively higher)
- Coins at ~2 units above the surface they sit on
- Enemies at y=1 (slightly above ground for visual clarity)
- Trees and decorations near edges to frame the play area

## Common Mistakes to Avoid

1. **Forgetting `castShadow`** — Objects look flat without shadow casting
2. **Wrong physics shape size** — Remember `CANNON.Box` takes half-extents, not full size
3. **Not capping deltaTime** — Tab-switch causes huge delta spikes; engine caps at 0.05
4. **Forgetting isActive guard** — Destroyed objects still get update() calls
5. **Direct scene access** — Always use engine API methods
6. **Material per mesh** — Reuse materials when colors are the same
7. **Position-based ground checks** — Never use `body.position.y < N` to detect ground; it breaks on elevated platforms. Use collision normals instead
8. **Wrong collision normal sign** — `contact.ni` direction depends on body order (`contact.bi` vs `contact.bj`); always check `contact.bi === this.body` before reading the normal
9. **Missing isDead/isGameOver guards** — Always skip collision checks and input handling when Mario is dead or game is over
10. **Missing velocity reset on no input** — When using direct velocity control (`body.velocity.x = speed`), always zero velocity in the "no input" branch. An early `return` without zeroing leaves the body sliding forever (especially with low friction/damping)

---

## Collision & Interaction Design (Added 2026-02-11)

### Distance-Based vs Physics-Event Collisions
- **Use distance-based** (World-level loop) for game-logic interactions: coin collection, enemy contact, item pickups. Simpler to implement, easier to debug, reliable.
- **Use physics events** (`body.addEventListener('collide')`) for physical interactions: ground detection, wall sliding, platform riding. These need contact normals.
- **Don't mix them** for the same purpose — pick one approach per interaction type.

### Collision Radius Guide
| Interaction | Radius | Notes |
|------------|--------|-------|
| Coin collection | 1.2 | Generous — feels better to collect easily |
| Enemy contact (damage) | 1.0 | Tighter — unfair hits feel bad |
| Stomp detection | Check dy > 0.5 | Player must be above enemy |

### Death & Game-Over Flow
1. `mario.die()` — Sets `isDead=true`, disables collisionResponse, starts pop-up animation
2. Death animation runs for ~2 seconds (timer-based)
3. `handleDeathComplete()` — Decrements lives; if lives <= 0, sets `isGameOver=true`; otherwise calls `respawn()`
4. `main.ts` game loop detects `isGameOver` flag and shows overlay
5. Restart button calls `mario.resetGame()` which resets all state and respawns

### Typed Object Arrays in World.ts
Keep separate typed arrays (`coins: Coin[]`, `goombas: Goomba[]`) alongside the generic `entities: GameObject[]`. This enables efficient, type-safe collision checking without casting:
```typescript
addEntity(entity: GameObject): void {
  this.entities.push(entity);
  if (entity instanceof Mario) this.mario = entity;
}
```

### UI Overlay Pattern
- Define overlay HTML in `index.html` with `display: none` default
- Toggle with CSS class `.visible` (`display: flex`)
- Control from `main.ts` game loop, not from game objects
- Use `document.exitPointerLock()` when showing overlays
- Re-lock pointer on restart

---

## 3D Model Loading (Added 2026-02-11)

### Using External 3D Models vs Primitives
- **Prefer external models** (`.dae`, `.fbx`, `.glb`) for complex characters like Mario — they look much better than hand-built primitive shapes
- **Keep primitives** for simple geometric objects (platforms, coins, basic enemies) where the box/sphere/cylinder aesthetic fits the style
- **Hybrid approach works:** Load a 3D model for the character mesh but use a simple `CANNON.Box` for physics — the physics shape doesn't need to match the visual exactly

### Async Model Loading Checklist
1. Start loading in `create()` — the object is functional with physics before the model arrives
2. Add a `modelLoaded` boolean flag, initially `false`
3. Set `modelLoaded = true` inside the loader callback
4. Guard animation/visual code with `if (!this.modelLoaded) return`
5. The object still participates in physics while the model loads — the player can move immediately

### Model Container Hierarchy
For loaded models that need animation, use a 3-level hierarchy:
```
marioGroup (THREE.Group)      ← this.mesh, rotated to face direction
  ├── shadow (PlaneGeometry)  ← shadow decal, always flat on ground
  └── container (THREE.Group) ← isolates loader's Z_UP rotation
       └── model (loaded scene) ← the actual 3D model
```
This prevents animation code on `marioGroup` from conflicting with coordinate system corrections applied by the loader.

### Asset File Conventions
- Store assets in `public/assets/<object-name>/`
- Primary model file + all referenced textures in the same folder
- Texture naming: `<object>_<part>.png` (e.g., `mario_eyes_center.png`)
- Editor variants: `<object>_<part>_edit.png` suffix
- Unused variants: `<part>_unused.png` suffix (kept for future use)

---

---

## Large Structure Building (Added 2026-02-12)

### Building Complex Structures from Primitives
When building large multi-part objects like castles or buildings:
1. **Group everything** — Put all meshes in a single `THREE.Group`, position the group once
2. **Define materials first** — Create shared materials at the top of `create()` and reuse across all meshes
3. **Section by section** — Build in logical sections (base → walls → roof → turrets → decorations) with comments separating each section
4. **Selective physics** — Don't give every mesh a physics body. Only add bodies for surfaces Mario can walk on or collide with (walls, floors, bridges). Decorative elements (windows, flags, trim) don't need physics
5. **Track all bodies** — Use the Multiple Physics Bodies pattern (array + custom `destroy()`) when >1 body is needed

### Material Reuse Strategy
For objects with many meshes, define all materials once and reuse:
```typescript
// 10 materials × ~49 meshes = significant savings
const stoneMat = new THREE.MeshStandardMaterial({ color: 0xD3CFC7, roughness: 0.9 });
const roofMat  = new THREE.MeshStandardMaterial({ color: 0xC85A34, roughness: 0.7 });
// Reuse references — never create duplicate materials
```
**Rule of thumb:** If two meshes have the same color and material properties, they MUST share the same material instance. Creating `new MeshStandardMaterial({ color: 0xFF0000 })` twice wastes GPU resources.

### Scaling from Architectural Reference
When building real-world structures, establish a scale factor from one known measurement:
1. Pick a recognizable element (e.g., entrance arch = 4m real-world)
2. Decide its game-unit size (e.g., 3 game units tall)
3. Calculate scale factor: `3 / 4 = 0.75 game units per meter`
4. Apply consistently to ALL measurements
5. Validate against the player: Mario at 1.8 units should be ~60% of a 3-unit doorway

**PeachCastle reference:** 0.75 gu/m — entrance arch 3gu (4m), main body 11gu tall (14.7m), central tower 15gu (20m)

### Castle Color Palette Addition
Extending the project's color palette for stone/castle structures:
- Warm stone: `0xD3CFC7` (main walls)
- Terracotta roof: `0xC85A34`
- Old wood: `0x8B6914` (bridge, doors)
- Water blue: `0x2196F3` (transparent, opacity 0.6)
- Gold trim: `0xDAA520` (metalness 0.7)
- Pink glass: `0xFFB6C1` with emissive `0xFF69B4` (stained glass)
- Dark void: `0x1A1A1A` (window interiors, doorways)
- Earth brown: `0x6B4226` (mound, foundation)

### World Integration for Large Objects
When adding a large structure that replaces multiple smaller objects:
1. Remove the placeholder objects it replaces
2. Check for overlapping objects (coins, enemies, platforms) and reposition them
3. A single `addEntity()` call is sufficient — the object manages its own sub-bodies
4. The castle's physics bodies are self-contained; no extra collision setup needed in World.ts

---

---

## Terrain & Structure Integration (Added 2026-02-12)

### Terrain-Structure Integration Pattern
When placing a structure (e.g., castle) relative to sculpted terrain:
1. **Ground-level behind foreground hill** — Place the structure at y=0 behind a foreground hill that partially occludes it, creating depth and a sense of scale (e.g., castle at z=-50 behind hill at z=-12)
2. **Or atop a terrain mound** — Set `position.y` to the mound top height so the structure sits on the mound
3. **Physics auto-shifts** — If the structure's physics bodies use `config.position` offsets, they automatically move with the new position
4. **Verify bridge/entrance alignment** — Ensure connecting elements (bridges, ramps) still meet the terrain at the correct height

### Foreground Hill for Depth
Place a prominent concentric-cylinder hill between the player and a distant structure to:
- Create visual depth and a sense of journey
- Partially occlude the structure from the spawn point, rewarding exploration
- Provide additional terrain for gameplay (coins, enemies on the hill)

**Layout reference:** Mario spawns at z=0, foreground hill at z=-12, castle at z=-50. The hill is ~8m tall (10 layers), partially hiding the castle behind it.

### Layered Ground Planes
Use multiple overlapping ground platforms at different Y levels instead of a single flat plane:
| Layer | Size | Y Position | Purpose |
|-------|------|------------|------------------|
| Dark earth base | 300×300 | -2.0 | Catch-all, prevents void gaps |
| Grass field | 200×200 | -0.25 | Main playable area |
| Sandy plaza | 15×20 | -0.1 | Localized area near spawn |

**Key benefit:** The large base layer at y=-2 prevents the player from falling into the void if they walk off the main grass area.

### Selective Physics on Terrain Hills
Not every visual terrain layer needs physics. For hills the player won't climb:
- Add physics only to the bottom 2 layers (walkable base)
- Skip physics on upper layers (decorative peaks)
- This reduces physics body count while maintaining visual richness

### Tree Placement on Terrain
When placing trees on terrain hills, pass `baseY` to set vertical position:
```typescript
this.createTree(x, z, baseY); // baseY matches the hill layer height at that position
```
Estimate `baseY` from the hill layer radii — a tree at distance D from center sits at the Y of the outermost layer whose radius ≥ D.

### Object Relocation When Terrain Changes
When adding or restructuring terrain features, all game objects in the affected Z-range must be checked and relocated:
1. **Identify the collision zone** — Determine the X/Z footprint and height of the new terrain (e.g., foreground hill at z=-12, radius 20)
2. **Find overlapping objects** — Check coins, goombas, floating platforms, and decorations within that footprint
3. **Relocate or remove** — Move objects outside the terrain feature, or respace them to avoid overlap
4. **Test walkability** — Verify Mario can still reach relocated objects without getting stuck

**Example:** When adding a foreground hill at z=-12 with radius 20, the floating platform at z=-12 and coins near z=-12 were moved to z=12 (positive Z, away from the hill).

### Physics Catch Bodies for Pits and Trenches
Whenever you excavate terrain (moats, pits, gaps), always add a thin physics body at the bottom:
- Prevents Mario from falling through the world into the void
- Use a 0.3-unit thick cylinder/box spanning the full width of the excavation
- Position at the lowest visual point (e.g., below the water surface)

### Bridge and Connector Sizing
When surrounding terrain features change size, connectors (bridges, ramps, walkways) must be resized to match:
- Bridge length should span the full gap from structure exit to solid ground
- Add railing posts at regular intervals (every 1 unit) for visual fidelity
- Physics body must cover the full bridge length to remain walkable

**Example:** When the moat was widened from ~5 to ~13 units, the bridge was extended from 5→13 game units, railing posts increased from 5→13 per side.

### Open-Ended Cylinders for Walls
`CylinderGeometry(r, r, h, segments, 1, true)` — the `true` parameter removes top/bottom caps, creating tube walls that can be seen through from inside. Useful for:
- Moat/trench walls (see stone walls from inside the trench)
- Hollow towers or fortress walls
- Any ring-shaped enclosure

### Terrain Building Method Organization
Keep terrain construction in a dedicated `buildTerrain()` method called from `buildLevel()`. This separates terrain from gameplay objects (platforms, coins, enemies) and keeps `buildLevel()` readable.

---

## Terrain Map Implementation (Added 2026-03-02)

### cannon-es Quaternion API: `setFromEuler` vs `setFromAxisAngle`
canon-es provides **two different methods** for setting body rotation:
- `body.quaternion.setFromEuler(x, y, z)` — Sets rotation from Euler angles (all three axes at once). Best for matching `mesh.rotation.set(x, y, z)` or when you have data-driven rotY values.
- `body.quaternion.setFromAxisAngle(axis, angle)` — Sets rotation around a single axis. Best for single-axis rotations like ramps.

**WARNING:** There is NO `setFromEulerAngles` method in cannon-es. If you see this in documentation or AI-generated code, it's wrong. The correct method is `setFromEuler(x, y, z)`.

```typescript
// For Y-axis rotation (paths, roads):
body.quaternion.setFromEuler(0, rotY, 0);

// For single-axis rotation (ramps):
body.quaternion.setFromAxisAngle(new CANNON.Vec3(1, 0, 0), -0.12);
```

### Inline Terrain Pattern vs Class-Based
Terrain built via `buildTerrain()` uses **inline construction** (direct mesh + body creation) rather than creating `GameObject` subclasses. This is the correct choice when:
- The terrain is static (no `update()` needed)
- No entity tracking required (no collision checks against terrain zones)
- Many small pieces compose a single feature (e.g., 10 cylinder layers for one hill)
- No reuse across files (terrain is World-specific)

**Use class-based `GameObject`** when:
- The object needs per-frame updates (enemies, collectibles)
- Object lifecycle management is needed (destroy, respawn)
- The object needs collision detection in the World update loop
- Reuse across different levels/worlds

**Exception:** The Stone Platform (Zone F) uses `Platform` class because it's a distinct gameplay surface that benefits from entity tracking. This is a good example of mixing both patterns in the same level.

### CANNON.Box for Scaled Cylinder Physics
When using `mesh.scale.x`/`mesh.scale.z` on CylinderGeometry to create elongated shapes, always use `CANNON.Box` (not `CANNON.Cylinder`) for the physics body:
- `CANNON.Cylinder` doesn't support non-uniform scaling
- `CANNON.Box` with scaled half-extents provides more predictable collision behavior for large terrain features
- The player won't notice the box-vs-cylinder difference on terrain hills

### Terrain Zone Organization
Organize complex terrain into named zones (A, B, C...) with comments separating each zone in the code. This makes the terrain map navigable and debuggable:
```typescript
// === Sandy Path (Zone A) — L-shaped curve from SW to SE ===
// ... zone A code ...

// === Main Hill (Zone B) — Elongated, center-left ===
// ... zone B code ...
```
Maintain a companion architecture document ([castle-grounds-terrain-map.md](../../architecture-analysis/castle-grounds-terrain-map.md)) that maps zones to game coordinates.

### Spawn Point Alignment with Terrain
When restructuring terrain, update the player spawn/respawn position to match the new layout:
- Spawn should be on walkable terrain (not inside a hill or over a cliff)
- Spawn Y should be above the terrain surface (e.g., y=5 if ground is at y=0)
- Test that the player lands cleanly on the ground after spawning

*Last updated: 2026-03-02*
*Updated by: learning agent — Castle Grounds terrain map: setFromEuler API, inline terrain pattern, CANNON.Box for scaled cylinders, zone organization, spawn alignment*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thijslim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
