---
name: react-threejs-game
description: Three.js game development with React using @react-three/fiber and @react-three/drei with strict TypeScript and 60fps performance Use when this capability is needed.
metadata:
  author: hack23
---

# react-threejs-game Skill

## Context
Applies when building 3D game scenes, implementing game loops, handling 3D interactions, optimizing Three.js performance, or managing game state with React.

## Rules

1. **Declarative Components**: Use `@react-three/fiber` JSX syntax, not imperative Three.js API
2. **Type Everything**: `useRef<THREE.Mesh>(null)`, typed props interfaces, typed event handlers
3. **useFrame for Game Loop**: All animations, physics, AI in `useFrame((state, delta) => ...)` with delta time
4. **Refs for Three.js Objects**: Access via `useRef`, never mutate props directly
5. **Minimize Re-renders**: Keep high-frequency updates in refs, not `useState`
6. **Use Drei Helpers**: `OrbitControls`, `useTexture`, `Html` from `@react-three/drei`
7. **Mesh Events**: `onClick`, `onPointerOver` on meshes — no manual raycasting or DOM listeners
8. **Dispose Resources**: Clean up geometries, materials, textures on unmount
9. **InstancedMesh**: Use for >10 similar objects (particles, enemies, bullets)
10. **60fps Target**: Keep frame time under 16ms. Profile with React DevTools
11. **Separate Concerns**: Logic in hooks, rendering in JSX, state in React/Zustand
12. **Avoid useState in useFrame**: Use refs for transient animation state

## Examples

### ✅ Typed Game Object with useFrame

```tsx
import { useRef } from 'react';
import { useFrame } from '@react-three/fiber';
import * as THREE from 'three';

interface TargetProps {
  position: [number, number, number];
  size: number;
  onClick: () => void;
}

export function Target({ position, size, onClick }: TargetProps): JSX.Element {
  const meshRef = useRef<THREE.Mesh>(null);

  useFrame((state, delta) => {
    if (meshRef.current) {
      meshRef.current.rotation.y += delta * 0.5;
      meshRef.current.position.y = position[1] + Math.sin(state.clock.elapsedTime) * 0.3;
    }
  });

  return (
    <mesh ref={meshRef} position={position} onClick={onClick}>
      <sphereGeometry args={[size, 16, 16]} />
      <meshStandardMaterial color="hotpink" />
    </mesh>
  );
}
```

### ❌ Anti-Patterns

```typescript
// BAD: setInterval for animation
setInterval(() => { mesh.rotation.y += 0.01 }, 16);

// BAD: useState in useFrame
useFrame(() => { setPosition(prev => prev + 1) }); // 60 re-renders/sec!

// BAD: Untyped ref
const meshRef = useRef(null); // Missing THREE.Mesh type

// BAD: No resource cleanup
// Forgetting to dispose geometry/material on unmount
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hack23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
