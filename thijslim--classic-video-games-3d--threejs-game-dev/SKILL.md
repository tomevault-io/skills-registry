---
name: threejs-game-dev
description: Three.js and cannon-es techniques, patterns, and performance tips for 3D web game development. Use this when building visuals, physics, or rendering features. Use when this capability is needed.
metadata:
  author: thijslim
---

# Three.js & cannon-es Game Development

Techniques and patterns for building 3D web games with Three.js (rendering) and cannon-es (physics).

## Project Setup

- **Three.js version:** ^0.170.0
- **cannon-es version:** ^0.20.0
- **Bundler:** Vite 6
- **TypeScript:** strict mode, ES2022 target

## Rendering

### Scene Configuration
```typescript
const scene = new THREE.Scene();
scene.background = new THREE.Color(0x87CEEB);  // Sky blue
scene.fog = new THREE.Fog(0x87CEEB, 50, 200);  // Distance fog
```

### Renderer Settings
```typescript
const renderer = new THREE.WebGLRenderer({ antialias: true });
renderer.shadowMap.enabled = true;
renderer.shadowMap.type = THREE.PCFSoftShadowMap;
renderer.toneMapping = THREE.ACESFilmicToneMapping;
renderer.toneMappingExposure = 1.2;
renderer.setPixelRatio(Math.min(window.devicePixelRatio, 2));  // Cap for performance
```

### Lighting Setup (Platformer Style)
```typescript
// Ambient (base illumination)
new THREE.AmbientLight(0xffffff, 0.5);

// Hemisphere (sky + ground bounce)
new THREE.HemisphereLight(0x87CEEB, 0x8B4513, 0.4);

// Directional sun (shadows)
const sun = new THREE.DirectionalLight(0xffffff, 1.2);
sun.position.set(50, 80, 30);
sun.castShadow = true;
sun.shadow.mapSize.set(2048, 2048);
sun.shadow.camera.left = -50;
sun.shadow.camera.right = 50;
sun.shadow.camera.top = 50;
sun.shadow.camera.bottom = -50;
```

### Material Best Practices
```typescript
// Standard lit material (preferred for most objects)
new THREE.MeshStandardMaterial({
  color: 0xFF0000,
  roughness: 0.8,
  metalness: 0.1,
});

// Glowing / emissive material (coins, power-ups)
new THREE.MeshStandardMaterial({
  color: 0xFFD700,
  metalness: 0.8,
  roughness: 0.2,
  emissive: 0xFFA000,
  emissiveIntensity: 0.3,
});

// Transparent overlay (glow effects, shadows)
new THREE.MeshBasicMaterial({
  color: 0xFFD700,
  transparent: true,
  opacity: 0.15,
});
```

### Common Geometries
| Shape | Three.js | Typical Use |
|-------|----------|-------------|
| Box | `BoxGeometry(w, h, d)` | Platforms, blocks, body parts |
| Sphere | `SphereGeometry(r, wSeg, hSeg)` | Heads, eyes, projectiles |
| Cylinder | `CylinderGeometry(rTop, rBot, h, seg)` | Pipes, hats, coins, limbs |
| Plane | `PlaneGeometry(w, h)` | Shadow decals, flat surfaces |

### Shadow Rules
- `castShadow = true` on all visible character/object meshes
- `receiveShadow = true` on ground and large platforms
- Don't enable shadows on transparent/glow overlays
- Shadow decals: `PlaneGeometry` with low-opacity dark `MeshBasicMaterial`, `depthWrite: false`

## Physics (cannon-es)

### World Configuration
```typescript
const world = new CANNON.World({
  gravity: new CANNON.Vec3(0, -25, 0),  // Strong gravity for platformer
});
world.broadphase = new CANNON.SAPBroadphase(world);
world.defaultContactMaterial.friction = 0.3;
world.defaultContactMaterial.restitution = 0.1;
```

### Body Types
```typescript
// Static (platforms, walls) — mass = 0
new CANNON.Body({ mass: 0, shape: new CANNON.Box(...) });

// Dynamic (player) — mass > 0
new CANNON.Body({ mass: 1, fixedRotation: true, linearDamping: 0.1 });

// Trigger (collectibles) — no physical collision
new CANNON.Body({ mass: 0, isTrigger: true, collisionResponse: false });

// Kinematic (enemies) — mass = 0, move via position
new CANNON.Body({ mass: 0, collisionResponse: true });
```

### Collision Detection
```typescript
body.addEventListener('collide', (event: any) => {
  const contact = event.contact;
  const normal = contact.ni;
  // Check collision direction using normal vector
});
```

### Physics-Visual Sync
```typescript
// Option A: Built-in helper
this.syncMeshToBody();

// Option B: Manual (for offset)
this.mesh.position.set(
  this.body.position.x,
  this.body.position.y - 0.5,  // visual offset
  this.body.position.z
);
```

## Camera

### Third-Person Orbit Camera
```typescript
// Spherical coordinates around target
const offsetX = Math.sin(rotX) * Math.cos(rotY) * distance;
const offsetY = Math.sin(rotY) * distance;
const offsetZ = Math.cos(rotX) * Math.cos(rotY) * distance;

// Smooth follow with lerp
const t = 1 - Math.pow(0.001, deltaTime * smoothSpeed);
currentPos.lerp(desiredPos, t);
camera.lookAt(targetPos);
```

## Performance Tips
- Cap `deltaTime` to 0.05s to prevent physics explosion on tab-switch
- Cap `pixelRatio` to 2 to avoid excessive GPU workload on retina screens
- Use `SAPBroadphase` for physics (better than default `NaiveBroadphase`)
- Keep polygon counts low: 8-16 segments for cylinders/spheres
- Reuse materials across objects when colors match
- Use `THREE.Group` for complex objects — easier transforms and cleanup

---

## Techniques Added: 2026-02-11

### Reliable Ground Detection (cannon-es)
The collision normal direction depends on which body is `bi` vs `bj`. **Always** check `contact.bi === this.body` to determine the correct sign:
```typescript
this.body.addEventListener('collide', (event: any) => {
  const contact = event.contact;
  const normal = contact.ni;
  const isBodyA = contact.bi === this.body;
  const upDot = isBodyA ? -normal.y : normal.y;
  if (upDot > 0.5) {
    this.isGrounded = true;
  }
});
```
**Pitfall:** Using `event.body === this.body` or checking raw `normal.y` without body-order correction will give wrong results on platforms.

**Pitfall:** Do NOT use position-based ground checks like `body.position.y < 1.5` — this breaks on elevated platforms. Use collision normals or velocity-based fallback instead.

### Velocity-Based Grounded Fallback
Complement collision-based detection with a velocity check for edge cases:
```typescript
// If velocity.y is near zero, body is resting on something
if (Math.abs(this.body.velocity.y) < 0.3 && !this.isGrounded) {
  this.isGrounded = true;
}
// If clearly falling, mark as not grounded
if (this.body.velocity.y < -2) {
  this.isGrounded = false;
}
```

### Disabling collisionResponse for Death/Ghost
Toggle `body.collisionResponse` at runtime to make objects pass through platforms:
```typescript
this.body.collisionResponse = false;  // Ghost mode — falls through everything
this.body.collisionResponse = true;   // Restore normal collisions
```
Useful for death animations (body pops up then falls through floor).

### Platformer Speed Constants (with gravity -25)
Tuned values that feel right with the project's strong gravity:
| Parameter | Value | Notes |
|-----------|-------|-------|
| Walk speed | 14 | Camera-relative |
| Run speed | 22 | Hold Shift |
| Jump force | 13 | Single jump |
| Double jump | 15 | Within 0.4s window |
| Triple jump | 19 | Within 0.4s window |
| Ground pound | -20 | Instant downward velocity |
| Death pop | 12 | Upward velocity on die |

### Loading 3D Models with ColladaLoader
The project uses `ColladaLoader` from `three/examples/jsm/loaders/ColladaLoader.js` to load `.dae` (Collada) model files.

**Z_UP Container Pattern:** ColladaLoader may apply a rotation to convert from Z_UP to Y_UP coordinate systems. If you also need to rotate/animate the model, wrap it in an intermediate `THREE.Group` container so the loader's correction isn't overwritten:
```typescript
const container = new THREE.Group();
container.add(collada.scene);
parentGroup.add(container);
```

**Model Scaling:** External models often use different unit scales. Calculate scale factor as `desiredSize / nativeSize` (e.g., ~90 native units → 0.02 scale for ~1.8 game units).

**Shadow Traversal:** Loaded models don't have shadows enabled by default. Traverse all children:
```typescript
model.traverse((child) => {
  if ((child as THREE.Mesh).isMesh) {
    (child as THREE.Mesh).castShadow = true;
    (child as THREE.Mesh).receiveShadow = true;
  }
});
```

**Async Loading Guard:** Since model loading is async, use a boolean flag (`modelLoaded`) and guard any code that depends on the model's presence.

### Asset Organization
Store model files and their textures together under `public/assets/<name>/`. Textures referenced by `.dae` files are resolved relative to the model file. Current asset structure:
- `/public/assets/mario/mario.dae` — Collada model (primary)
- `/public/assets/mario/mario.fbx` — FBX format (alternative)
- `/public/assets/mario/*.png` — Textures (color maps, eye variants, detail textures)

Serve assets from `public/` so Vite makes them available at the root path (e.g., `/assets/mario/mario.dae`).

---

## Techniques Added: 2026-02-12

### Rectangular Hip Roof via ConeGeometry
Create a hip roof (pyramid shape with rectangular base) using a 4-sided `ConeGeometry` rotated 45° and scaled non-uniformly on one axis:
```typescript
const roof = new THREE.Mesh(
  new THREE.ConeGeometry(15.56, 6, 4), // 4 sides = square pyramid
  roofMat,
);
roof.rotation.y = Math.PI / 4;   // Rotate 45° to align edges with walls
roof.scale.z = 0.727;            // Compress Z to make rectangular (ratio = shortSide / longSide)
roof.position.set(0, 16, 0);
```
**Key insight:** A 4-sided cone is a pyramid. The `radiusTop=0` default makes it a perfect point. The radius should be calculated so the base covers the building: `radius = (longSide / 2) * sqrt(2)` for a square, then scale one axis to make it rectangular.
**Formula:** `scale.z = shortSide / longSide` (e.g., 16/22 ≈ 0.727 for a 22×16 building).

### Semi-Circular Arch Construction
Build a detailed entrance arch by combining three primitives:
```typescript
// 1. Rectangular void (door opening)
const doorVoid = new THREE.Mesh(
  new THREE.BoxGeometry(2.5, 3, 1.2),
  darkMat,
);
doorVoid.position.set(0, 3.5, wallZ);

// 2. Half-cylinder for the arch top
const archTop = new THREE.Mesh(
  new THREE.CylinderGeometry(1.25, 1.25, 1.2, 16, 1, false, 0, Math.PI),
  darkMat,
);
archTop.position.set(0, 5, wallZ);
archTop.rotation.z = Math.PI / 2;  // Stand upright
archTop.rotation.x = Math.PI / 2;  // Face forward

// 3. Half-torus for the stone surround
const archSurround = new THREE.Mesh(
  new THREE.TorusGeometry(1.35, 0.15, 8, 16, Math.PI),
  stoneMat,
);
archSurround.position.set(0, 5, wallZ - 0.01); // Slightly in front
archSurround.rotation.z = Math.PI;  // Arch opens downward
```
**Technique:** Use `CylinderGeometry(r, r, depth, seg, 1, false, 0, Math.PI)` for a half-cylinder. The `thetaStart=0, thetaLength=Math.PI` parameters create the half shape. Combine rotations to orient it correctly.
**Tip:** The torus surround radius should be slightly larger than the cylinder so it frames the arch visually.

### Z-Fighting Prevention for Facade Decorations
When placing flat decorations (windows, frames, plaques) on wall surfaces, offset them 0.01–0.02 units from the wall to prevent z-fighting flicker:
```typescript
// Wall surface is at z = 8.0
const window = new THREE.Mesh(circleGeom, darkMat);
window.position.set(x, y, 8.01);   // 0.01 offset prevents z-fighting

const frame = new THREE.Mesh(frameGeom, goldMat);
frame.position.set(x, y, 8.02);    // Frame slightly in front of window
```
**Rule:** Layer decorations with increasing offsets: wall (0.00) → glass/void (0.01) → frame/trim (0.02). This ensures consistent render order without `depthWrite` hacks.

### Circular Window Construction
Combine `RingGeometry` (for the trim) and `CircleGeometry` (for the interior) to create circular windows:
```typescript
// Gold ring trim
const ring = new THREE.Mesh(
  new THREE.RingGeometry(0.35, 0.45, 16), // inner, outer radius
  goldMat,
);
ring.position.set(x, y, wallZ + 0.01);

// Dark interior
const circle = new THREE.Mesh(
  new THREE.CircleGeometry(0.35, 16),
  darkMat,
);
circle.position.set(x, y, wallZ + 0.01);
```

### Moat / Water Ring Effect
Use `RingGeometry` with a transparent water material for a moat around a structure:
```typescript
const moat = new THREE.Mesh(
  new THREE.RingGeometry(15, 19, 32),  // inner, outer radius
  new THREE.MeshStandardMaterial({ color: 0x2196F3, transparent: true, opacity: 0.6, roughness: 0.1 }),
);
moat.position.set(0, 0.05, 0);      // Just above ground
moat.rotation.x = -Math.PI / 2;     // Lay flat
```

### Large Structure Material Strategy
Define all materials upfront and reuse across meshes. For PeachCastle, 10 materials serve ~49 meshes:
```typescript
// Define once at the top of create()
const stoneMat = new THREE.MeshStandardMaterial({ color: 0xD3CFC7, roughness: 0.9 });
const roofMat  = new THREE.MeshStandardMaterial({ color: 0xC85A34, roughness: 0.7 });
const woodMat  = new THREE.MeshStandardMaterial({ color: 0x8B6914, roughness: 0.85 });
// ... reuse everywhere
```
**Performance benefit:** Fewer material instances = fewer GPU state changes during rendering. Three.js can batch meshes that share the same material.

### Concentric Cylinder Hill Technique (Added 2026-02-12)
Approximate smooth hills/mounds using stacked `CylinderGeometry` layers with decreasing radii. Each layer has its own `CANNON.Cylinder` physics body.

**Visual segments:** Use 32 radial segments for a smooth visual appearance on large terrain cylinders. Use 16 segments for the physics `CANNON.Cylinder` — physics doesn't need visual fidelity.

**Layer sizing:** 6-8 layers with 0.8 gu height each, radius decreasing ~3-5 units per step. Top radius ~50% of bottom radius creates a dome-like profile.

**Performance note:** Each layer adds one physics body. For large hills that the player won't walk on, use `hasPhysics: false` on upper layers to save physics computation:
```typescript
for (const layer of hillLayers) {
  // Always add visual mesh
  const mesh = new THREE.Mesh(new THREE.CylinderGeometry(layer.radius, layer.radius, layer.height, 24), mat);
  engine.addToScene(mesh);

  // Only add physics where needed
  if (layer.hasPhysics) {
    const body = new CANNON.Body({ mass: 0, shape: new CANNON.Cylinder(layer.radius, layer.radius, layer.height, 16) });
    engine.addPhysicsBody(body);
  }
}
```

### Non-Uniform Cylinder Scaling for Physics (Added 2026-02-12)
`CANNON.Cylinder` doesn't support non-uniform scaling. When stretching a `THREE.CylinderGeometry` via `mesh.scale.x` or `mesh.scale.z` for elliptical shapes, approximate the physics with a `CANNON.Box`:
```typescript
// Visual: elliptical cylinder
mesh.scale.x = 1.5;

// Physics: box approximation with scaled half-extent
const body = new CANNON.Body({
  mass: 0,
  shape: new CANNON.Box(new CANNON.Vec3(radius * scaleX, height / 2, radius)),
});
```
This is acceptable because the player won't notice the box vs cylinder difference for large terrain features.

### Rotated Physics Body Quaternion (Added 2026-02-12)
When rotating a mesh (e.g., for a ramp), the cannon-es body must match. Use `setFromAxisAngle` on the body's quaternion:
```typescript
// Visual rotation
mesh.rotation.x = -0.12;

// Physics rotation (must match)
body.quaternion.setFromAxisAngle(new CANNON.Vec3(1, 0, 0), -0.12);
```
**Axis mapping:** Three.js `rotation.x` → cannon-es axis `(1, 0, 0)`, `rotation.y` → `(0, 1, 0)`, `rotation.z` → `(0, 0, 1)`. The angle value is the same.

### Background Decorative Geometry (Added 2026-02-12)
Distant terrain features (mountains, slopes) should be:
- **Low-poly:** 6-sided cones for mountains, simple boxes for slopes
- **No physics:** Saves computation; player won't reach them
- **Large scale:** Radii of 30-50 units, heights of 20-35 units
- **Muted colors:** Slightly lighter/bluer greens for atmospheric perspective (`0x66BB6A`, `0x81C784`, `0x90CAF9`)
- **Partially buried:** Set Y position to half-height so the base is at ground level

### cannon-es Euler Rotation API (Added 2026-03-02)
canon-es `Body.quaternion` has `setFromEuler(x, y, z)` — NOT `setFromEulerAngles`. Use it for data-driven multi-axis rotations (e.g., curved path segments):
```typescript
// Match mesh.rotation.y on the physics body
body.quaternion.setFromEuler(0, seg.rotY, 0);
```
For single-axis rotation, `setFromAxisAngle` is equally valid:
```typescript
body.quaternion.setFromAxisAngle(new CANNON.Vec3(0, 1, 0), seg.rotY);
```
**Pitfall:** `setFromEulerAngles` does NOT exist in cannon-es. This is a common hallucination in AI-generated code.

### Transparent Water Surfaces (Added 2026-03-02)
For ponds and water features, use `MeshStandardMaterial` with transparency for a more realistic look than `MeshBasicMaterial`:
```typescript
const pondWaterMat = new THREE.MeshStandardMaterial({
  color: 0x2196F3,
  roughness: 0.3,    // Low roughness = reflective
  metalness: 0.1,
  transparent: true,
  opacity: 0.7,
});
```
**Tip:** Use `roughness: 0.3` and `metalness: 0.1` to get subtle reflections on the water surface. Lower opacity (0.5-0.7) lets the basin beneath show through.

### Open-Ended Cylinder with DoubleSide for Basin Walls (Added 2026-03-02)
When creating a sunken basin (pond, pit), use a slightly tapered open-ended cylinder with `DoubleSide` so the walls are visible from both inside and outside:
```typescript
const basinWall = new THREE.Mesh(
  new THREE.CylinderGeometry(11, 9, 3, 32, 1, true), // tapered: wider at top
  new THREE.MeshStandardMaterial({ color: 0x5D4037, side: THREE.DoubleSide }),
);
```
The taper (11 top → 9 bottom) gives a natural slope to the basin walls.

### Invisible Physics Walls (Added 2026-03-02)
For complex visual geometry that would be unreliable as physics shapes (e.g., rotated cliff boxes, cone peaks), create separate invisible `CANNON.Body` instances with simple shapes:
```typescript
// No visual mesh — just a physics barrier
const body = new CANNON.Body({
  mass: 0,
  shape: new CANNON.Box(new CANNON.Vec3(14, 6, 18)),
  position: new CANNON.Vec3(-45, 6, -35),
});
engine.addPhysicsBody(body);
```
**When to use:** Visual geometry uses rotations, cones, or complex arrangements that would produce unpredictable physics collision. The invisible walls provide clean, reliable barriers.
**Design principle:** Visual fidelity and physics reliability serve different goals. Don't try to make one shape serve both unless it's simple (box, sphere).

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/thijslim) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
