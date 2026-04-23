---
name: frontend-three-fiber-drei
description: Use when working with React Three Fiber, drei components, 3D scenes, camera management, or coordinate systems (Y-up)
metadata:
  author: ntq78
---

# Frontend: React Three Fiber & drei

Guidelines for working with 3D scenes using React Three Fiber (@react-three/fiber) and drei (@react-three/drei).

## Coordinate System: Y-Up

**CRITICAL: This project uses Y-up coordinate system (Three.js default)**

| Axis | Direction                | Usage                     |
| ---- | ------------------------ | ------------------------- |
| +X   | Right                    | Strafe right              |
| +Y   | Up                       | Vertical movement         |
| +Z   | Forward (towards camera) | Movement away from camera |
| -Z   | Into screen              | Movement towards target   |

### Camera Configuration

```tsx
<Canvas
    camera={{
        position: [-10, 10, 10],  // Y is height
        up: [0, 1, 0],            // Y-up
        fov: 75,
    }}
>
```

### Math Conventions

| Term    | Axis     | Description                  |
| ------- | -------- | ---------------------------- |
| Yaw     | Around Y | Horizontal look (left/right) |
| Pitch   | Around X | Vertical look (up/down)      |
| Forward | -Z       | Direction camera faces       |
| Right   | +X       | Camera's right side          |
| Up      | +Y       | World up direction           |

### Look Direction Calculation

```typescript
// Calculate look direction from yaw/pitch (Y-up)
const lookDir = new THREE.Vector3(
    -Math.sin(yaw) * Math.cos(pitch),
    Math.sin(pitch),
    -Math.cos(yaw) * Math.cos(pitch)
);
camera.up.set(0, 1, 0);
camera.lookAt(camera.position.clone().add(lookDir));
```

### Movement Vectors (WASD)

```typescript
// Forward/Right in XZ plane, Up along Y
const forward = new THREE.Vector3(-Math.sin(yaw), 0, -Math.cos(yaw));
const right = new THREE.Vector3(Math.cos(yaw), 0, -Math.sin(yaw));
const up = new THREE.Vector3(0, 1, 0);
```

---

## Camera Management

### Dual-Camera Architecture Warning

**CRITICAL: Switching cameras with drei's `makeDefault` has state isolation issues.**

```tsx
// This pattern causes problems for camera state restoration:
<Canvas camera={{ position: [...] }}>  {/* PerspectiveCamera */}
    {isOrthographic && (
        <OrthographicCamera makeDefault position={...} />
    )}
    <CustomFlyControls />
</Canvas>
```

**Problem:** `useThree().camera` returns the _currently active_ camera. State (position, refs) set on one camera doesn't transfer to the other when switching.

**Symptoms:**

- Camera position not restored after switching modes
- `getCameraState()` returns wrong camera's state
- `setCameraState()` applies to wrong camera

**Known failed approaches:**

1. Effect-based capture (race condition - captures after camera moved)
2. Synchronous ref capture (timing issues)
3. Imperative setCameraState (applies to active camera, not target)
4. Delayed restoration via pending ref (state isolation persists)
5. Continuous state capture via callback (same dual-camera problem)

**Workarounds:**

- Accept camera staying at new position after mode switch
- Use single camera with modified projection matrix (complex)
- Use drei's CameraControls (but loses WASD+mouselook)

### drei CameraControls vs Custom FlyControls

| Feature                | Custom FlyControls | drei CameraControls |
| ---------------------- | ------------------ | ------------------- |
| WASD Movement          | ✅ Yes             | ❌ No               |
| Mouse Look FPS         | ✅ Yes             | ❌ No (orbit only)  |
| ALT + Orbit            | ✅ Yes             | ✅ Yes (default)    |
| Q/E Vertical           | ✅ Yes             | ❌ No               |
| saveState/restoreState | ❌ No              | ✅ Yes              |

**Choose FlyControls for FPS-style navigation. Choose CameraControls for orbit-style with state management.**

---

## Key Files

| File                                                      | Purpose                                |
| --------------------------------------------------------- | -------------------------------------- |
| `components/Fiber/Fiber_Canvas/Fiber_Canvas.tsx`          | Base canvas wrapper with camera config |
| `components/Fiber/Fiber_Canvas/Fiber_Canvas_FlyControls/` | WASD + mouse look + orbit controls     |
| `components/Fiber/Fiber_PLY/Fiber_PLY.tsx`                | Gaussian splat (.ply) loading          |
| `components/Fiber/Fiber_USD/Fiber_USD.tsx`                | USD model loading                      |

---

## Common Patterns

### Ground Plane (XZ at Y=0)

```tsx
<Grid
    args={[100, 100]}
    position={[0, 0, 0]}
    rotation={[0, 0, 0]} // No rotation for Y-up
/>
```

### Plane Intersection (Ground)

```typescript
const plane = new THREE.Plane(new THREE.Vector3(0, 1, 0), 0);
const intersectionPoint = new THREE.Vector3();
raycaster.ray.intersectPlane(plane, intersectionPoint);
```

### Initial Angle Calculation

```typescript
// Calculate yaw/pitch to look at origin from camera position
const calculateInitialAngles = (pos: [number, number, number]) => {
    const [x, y, z] = pos;
    const yaw = Math.atan2(x, z);
    const pitch = -Math.atan2(y, Math.sqrt(x * x + z * z));
    return { yaw, pitch };
};
```

---

## Anti-Patterns

| Wrong (Z-up)                                     | Correct (Y-up)                                   |
| ------------------------------------------------ | ------------------------------------------------ |
| `up: [0, 0, 1]`                                  | `up: [0, 1, 0]`                                  |
| `camera.up.set(0, 0, 1)`                         | `camera.up.set(0, 1, 0)`                         |
| `new THREE.Plane(new THREE.Vector3(0, 0, 1), 0)` | `new THREE.Plane(new THREE.Vector3(0, 1, 0), 0)` |
| `position.z` for height                          | `position.y` for height                          |
| `rotation={[-Math.PI / 2, 0, 0]}` for grid       | `rotation={[0, 0, 0]}` for grid                  |

---

## Related Skills

- **frontend-naming-conventions** - Component naming for Fiber components
- **frontend-code-style** - Code style patterns

<!-- Last updated: 2026-01-22 - Added camera management section, renamed from frontend-fiber-canvas -->

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ntq78) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
