---
name: rive-react
description: | Use when this capability is needed.
metadata:
  author: neversight
---

# Rive React

React components and hooks for Rive animations.

## Installation

```bash
# Canvas renderer (recommended)
npm install @rive-app/react-canvas

# WebGL renderer (complex animations)
npm install @rive-app/react-webgl
```

## Quick Start

```tsx
import { useRive } from '@rive-app/react-canvas';

function MyAnimation() {
  const { RiveComponent } = useRive({
    src: 'animation.riv',
    stateMachines: 'State Machine 1',
    autoplay: true,
  });

  return <RiveComponent />;
}
```

## useRive Hook

The main hook for Rive integration:

```tsx
const {
  RiveComponent,    // The canvas component to render
  rive,            // Rive instance for direct control
} = useRive({
  src: 'animation.riv',
  stateMachines: 'State Machine',
  autoplay: true,
});
```

## Controlling Inputs

```tsx
import { useRive, useStateMachineInput } from '@rive-app/react-canvas';

function ControlledAnimation() {
  const { rive, RiveComponent } = useRive({
    src: 'animation.riv',
    stateMachines: 'State Machine',
    autoplay: true,
  });

  // Get typed input
  const isActive = useStateMachineInput(rive, 'State Machine', 'isActive');
  const progress = useStateMachineInput(rive, 'State Machine', 'progress');

  return (
    <>
      <RiveComponent />
      <button onClick={() => isActive && (isActive.value = !isActive.value)}>
        Toggle
      </button>
      <input
        type="range"
        onChange={(e) => progress && (progress.value = +e.target.value)}
      />
    </>
  );
}
```

## Callbacks

```tsx
const { RiveComponent } = useRive({
  src: 'animation.riv',
  stateMachines: 'State Machine',
  autoplay: true,
  onLoad: () => console.log('Loaded'),
  onStateChange: (event) => console.log('State:', event.data),
  onPlay: () => console.log('Playing'),
  onPause: () => console.log('Paused'),
});
```

## Rules

@file rules/use-rive.md
@file rules/callbacks.md
@file rules/layout.md
@file rules/scroll-animations.md

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
