---
name: debugging
description: Known bug patterns, investigation techniques, and fix recipes for the 3D platformer. Used by debugging agents to recognize and resolve bugs faster. Use when this capability is needed.
metadata:
  author: thijslim
---

# Debugging Skill

Known bug patterns, root cause archetypes, and fix recipes for **Super Mario 3D Web Edition**. This file is continuously updated by the learning agent after each bug resolution cycle.

---

## Bug Pattern Catalog

### BP-001: Collision Normal Inversion
- **Symptom:** Ground detection is backwards — player registers as grounded while in the air, or never registers as grounded
- **Root Cause:** `contact.ni` direction depends on body order (`contact.bi` vs `contact.bj`). Reading `normal.y` without checking which body is which gives 50/50 wrong results
- **File:** `src/game/objects/Mario.ts` — collision event handler
- **Fix:**
```typescript
const isBodyA = contact.bi === this.body;
const upDot = isBodyA ? -normal.y : normal.y;
if (upDot > 0.5) this.isGrounded = true;
```
- **Detection Cue:** "jumping doesn't work", "stuck in air", "always grounded"

### BP-002: Position-Based Ground Check
- **Symptom:** Jumping works on the ground floor but breaks on elevated platforms
- **Root Cause:** Using `body.position.y < threshold` to detect ground instead of collision normals
- **File:** `src/game/objects/Mario.ts`
- **Fix:** Replace with collision normal-based detection (see BP-001) plus velocity fallback
- **Detection Cue:** "can't jump on platforms", "works on ground but not higher up"

### BP-003: Missing isActive Guard
- **Symptom:** Errors or ghost behavior after an object is destroyed
- **Root Cause:** `update()` runs on destroyed objects because the guard clause is missing
- **File:** Any game object's `update()` method
- **Fix:** Add `if (!this.isActive) return;` at the top of `update()`
- **Detection Cue:** "error after collecting coin", "ghost enemy", "crash after object removed"

### BP-004: State Leak Across Respawn
- **Symptom:** Behavior is broken after dying and respawning (e.g., can't jump, stuck in state, wrong speed)
- **Root Cause:** `respawn()` or `resetGame()` doesn't reset all relevant state variables
- **File:** `src/game/objects/Mario.ts` — `respawn()` / `resetGame()`
- **Fix:** Audit all state variables and ensure complete reset, including `isGrounded`, `state`, `body.collisionResponse`, velocity, etc.
- **Detection Cue:** "broken after respawn", "works until I die", "stuck after restart"

### BP-005: deltaTime Spike
- **Symptom:** Physics explosion, objects teleporting, or strange behavior after tabbing away from the game
- **Root Cause:** `deltaTime` is the wall-clock time since last frame — tabbing away causes huge values (seconds, not milliseconds)
- **File:** `src/engine/GameEngine.ts` or `src/main.ts`
- **Fix:** Cap deltaTime: `const dt = Math.min(clock.getDelta(), 0.05);`
- **Detection Cue:** "glitches when I tab back", "objects fly away", "physics breaks after pause"

### BP-006: CANNON.Box Half-Extent Confusion
- **Symptom:** Physics body is twice the expected size, or half the expected size
- **Root Cause:** `CANNON.Box` constructor takes **half-extents** (half of width/height/depth), but it's easy to pass full dimensions
- **File:** Any game object's `create()` method
- **Fix:** `new CANNON.Box(new CANNON.Vec3(width/2, height/2, depth/2))`
- **Detection Cue:** "collision box too big", "object clips", "physics shape doesn't match visual"

### BP-007: Distance Check Radius Mismatch
- **Symptom:** Can't collect coins (too tight) or enemies kill from too far away (too generous)
- **Root Cause:** The distance threshold in World.ts collision check is wrong relative to object sizes
- **File:** `src/game/World.ts` — collision check methods
- **Fix:** Adjust radius: 1.2 for coins (generous), 1.0 for enemy contact (tighter)
- **Detection Cue:** "can't pick up coins", "unfair enemy kills", "have to be exactly on top"

### BP-008: Mesh-Physics Desync
- **Symptom:** Visual object drifts away from where it physically is, or object appears at origin
- **Root Cause:** Missing `syncMeshToBody()` call in `update()`, or manual position sync is wrong
- **File:** Any game object's `update()` method
- **Fix:** Call `this.syncMeshToBody()` at end of `update()`, or sync manually with correct offset
- **Detection Cue:** "object in wrong place", "visual doesn't match collision", "object at 0,0,0"

### BP-009: Conflicting Velocity Writes
- **Symptom:** Movement is jittery, character fights against itself, or jumps are inconsistent
- **Root Cause:** Multiple systems overwrite `body.velocity` in the same frame (gravity, jump, movement, physics step)
- **File:** `src/game/objects/Mario.ts` — `update()` method
- **Fix:** Set velocity components independently (x/z for movement, y for jumping), don't overwrite the full vector
- **Detection Cue:** "jittery movement", "jumps cut short", "horizontal movement kills vertical"

### BP-010: Dead State Not Checking
- **Symptom:** Player can still collect coins, die again, or receive input while dead/game-over
- **Root Cause:** Missing `isDead` / `isGameOver` guard in collision checks or input handling
- **File:** `src/game/World.ts` (collisions), `src/game/objects/Mario.ts` (input)
- **Fix:** Add `if (this.mario.isDead || this.mario.isGameOver) return;` before collision checks
- **Detection Cue:** "die twice", "collect coins while dead", "can still move after game over"

### BP-011: Missing Velocity Reset on Input Release (Added 2026-02-11)
- **Symptom:** Player character keeps moving in the last input direction after releasing all movement keys (WASD/arrows)
- **Root Cause:** When using direct velocity control (setting `body.velocity.x`/`body.velocity.z` directly), the "no input" branch exits early without zeroing horizontal velocity. Combined with low `friction` (0) and low `linearDamping` (0.1), the body retains its last velocity indefinitely.
- **File:** `src/game/objects/Mario.ts` — `handleMovement()`
- **Fix:**
```typescript
if (x === 0 && z === 0) {
    this.body.velocity.x = 0;
    this.body.velocity.z = 0;
    return;
}
```
- **Detection Cue:** "never stops moving", "keeps running after releasing keys", "slides forever", "won't stop"
- **General Rule:** When using direct velocity control (not forces/impulses), every frame must either set the velocity to the desired value OR explicitly zero it. Both the "input active" and "no input" paths must write to velocity. An early `return` without zeroing is a bug.
- **Related:** BP-009 (conflicting velocity writes). This is the inverse: instead of too many writes, the problem is a *missing* write.

---

## Investigation Decision Tree

Use this tree to quickly narrow down bug categories:

```
Bug Report
├── Player can't do X (movement/ability)
│   ├── Works sometimes → State machine (check transitions)
│   ├── Never works → Input binding or guard clause blocking
│   ├── Works on ground only → Ground detection (BP-001, BP-002)
│   └── Never stops → Missing velocity reset (BP-011)
│
├── Object behaves wrong (enemy/coin/platform)
│   ├── Not visible → Missing from World.ts buildLevel() or wrong position
│   ├── Visible but no interaction → Collision check missing or wrong radius (BP-007)
│   ├── Wrong position → Mesh-physics desync (BP-008) or wrong config
│   └── Wrong behavior → Object's update() logic or state
│
├── After dying / respawning
│   ├── Everything broken → State leak (BP-004)
│   ├── Specific thing broken → Missing reset for that state variable
│   └── Can't restart → Game-over flow in main.ts
│
├── Visual glitch
│   ├── Z-fighting → Two meshes at same position, add tiny offset
│   ├── Missing shadows → castShadow/receiveShadow not set
│   ├── Wrong color/material → Material config
│   └── Object flickering → Mesh-body desync (BP-008)
│
├── Performance issue
│   ├── After tab switch → deltaTime spike (BP-005)
│   ├── Gets worse over time → Memory leak, objects not destroyed
│   └── Always slow → Too many meshes/bodies, check counts
│
└── Crash / error
    ├── After destroying object → Missing isActive guard (BP-003)
    ├── On startup → Import error, missing file, wrong config
    └── Random timing → Race condition in physics vs game logic
```

---

## Fix Verification Checklist

After every bug fix, verify these items:

- [ ] TypeScript compiles: `npx tsc --noEmit`
- [ ] The original bug no longer reproduces
- [ ] Normal gameplay still works (run, jump, collect coins, stomp enemies)
- [ ] Death and respawn cycle works correctly
- [ ] Game-over and restart cycle works correctly
- [ ] Objects spawn at correct positions
- [ ] No console errors during normal play

---

## Common Debugging Techniques

### Technique: Console Logging State Machine
Add temporary logs to track state transitions:
```typescript
update(deltaTime: number): void {
  const prevState = this.state;
  // ... state transition logic ...
  if (this.state !== prevState) {
    console.log(`State: ${MarioState[prevState]} → ${MarioState[this.state]}`);
  }
}
```

### Technique: Physics Body Visualizer
Temporarily add wireframe meshes matching physics shapes:
```typescript
const wireframe = new THREE.Mesh(
  new THREE.BoxGeometry(w, h, d),
  new THREE.MeshBasicMaterial({ wireframe: true, color: 0xff0000 })
);
this.engine.addToScene(wireframe);
// Sync with body in update()
```

### Technique: Collision Event Logger
Log all collision events to understand what's hitting what:
```typescript
this.body.addEventListener('collide', (event: any) => {
  console.log('Collision:', {
    other: event.body,
    normal: event.contact.ni,
    isBodyA: event.contact.bi === this.body,
  });
});
```

### Technique: Freeze Frame
Pause the game loop to inspect state:
```typescript
// In main.ts, add a debug key:
if (inputManager.isKeyPressed('p')) {
  debugger; // Browser DevTools will pause here
}
```

---

*Last updated: 2026-02-11*
*Created by: bug-orchestrator system setup*

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thijslim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
