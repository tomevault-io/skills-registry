---
name: performance-optimization
description: React re-render optimization, Three.js rendering performance, useMemo/useCallback patterns, bundle size reduction, and 60fps profiling Use when this capability is needed.
metadata:
  author: hack23
---

# Performance Optimization Skill

## Context
Applies when building performance-sensitive features, optimizing re-renders, reducing draw calls, minimizing bundle size, profiling bottlenecks, or implementing game loops.

## Rules

1. **60fps Target**: 16.67ms per frame maximum in game rendering
2. **Minimize Re-renders**: `React.memo`, `useMemo`, `useCallback` for optimization
3. **Refs for Mutations**: Never `useState` for 60fps updates — use refs
4. **Batch Draw Calls**: Group similar geometries and materials in Three.js
5. **InstancedMesh**: For >10 similar objects (particles, enemies)
6. **Optimize Geometry**: Low poly counts, LOD for distant objects
7. **Lazy Load Assets**: On-demand loading for textures, models, sounds
8. **Code Splitting**: Dynamic imports by route/feature
9. **Memoize Calculations**: `useMemo` for expensive computations
10. **Debounce/Throttle Events**: Limit handler frequency (resize, scroll, input)
11. **Profile First**: React DevTools Profiler + Chrome DevTools before optimizing
12. **Bundle Size**: Target <500KB gzipped initial load
13. **Tree Shake**: Import only what you need from libraries

## Examples

### ✅ Memoized Component

```tsx
import { memo, useMemo, useCallback } from 'react';

interface HUDProps {
  score: number;
  health: number;
  onPause: () => void;
}

/** Memoized HUD — only re-renders when props change */
function GameHUDInner({ score, health, onPause }: HUDProps): JSX.Element {
  const healthColor = useMemo(() => health > 50 ? '#0f0' : '#f00', [health]);
  const handlePause = useCallback(() => onPause(), [onPause]);

  return (
    <div className="hud">
      <span>Score: {score}</span>
      <span style={{ color: healthColor }}>Health: {health}%</span>
      <button onClick={handlePause}>Pause</button>
    </div>
  );
}

export const GameHUD = memo(GameHUDInner);
```

### ✅ Ref-based Animation (No Re-renders)

```tsx
import { useRef } from 'react';
import { useFrame } from '@react-three/fiber';
import * as THREE from 'three';

function AnimatedObject(): JSX.Element {
  const meshRef = useRef<THREE.Mesh>(null);
  // Use ref, not state — avoids 60 re-renders/sec
  useFrame((_, delta) => {
    if (meshRef.current) meshRef.current.rotation.y += delta;
  });
  return <mesh ref={meshRef}><boxGeometry /><meshStandardMaterial /></mesh>;
}
```

### ❌ Anti-Patterns

```tsx
// BAD: useState in useFrame — 60 re-renders/sec!
useFrame(() => { setRotation(r => r + 0.01); });

// BAD: New object every render
<mesh position={[0, 0, 0]}>
  {/* Creates new array each render */}
  {/* GOOD: const POS = [0, 0, 0] as const; outside component */}
</mesh>

// BAD: Unmemoized expensive calc
const sorted = data.sort((a, b) => a - b); // Sorts every render
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
