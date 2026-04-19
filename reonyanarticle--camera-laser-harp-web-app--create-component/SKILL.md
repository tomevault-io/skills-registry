---
name: create-component
description: Create React TypeScript components for the laser harp following established patterns. Use when implementing UI components like ControlPanel, Stage, VideoLayer, or CanvasLayer. Use when this capability is needed.
metadata:
  author: reonyanarticle
---

# Create Laser Harp Component

## When to Use This Skill

- User requests to create a new component
- Implementing components from architecture: App, ControlPanel, Stage, VideoLayer, CanvasLayer, EffectLayer
- Need to follow established patterns for consistency
- Adding new UI components to the laser harp

## Component Architecture

```
App (root)
├── ControlPanel (UI controls)
└── Stage (display area)
    ├── VideoLayer (camera feed - hidden, but needed as source)
    ├── EffectLayer (silhouette rendering)
    └── CanvasLayer (zone boundaries + glow effects)
```

## Templates Available

- `template-functional.tsx` - Standard functional component with TypeScript
- `template-canvas.tsx` - Canvas-based component with useRef and resize handling

## Component Guidelines

### For ControlPanel Component

**Location:** `src/components/ControlPanel.tsx`

**Props:**
```typescript
interface ControlPanelProps {
  config: AppConfig;
  onConfigChange: (config: AppConfig) => void;
  onStart: () => void;
  onStop: () => void;
  isRunning: boolean;
}
```

**Responsibilities:**
- Display Start/Stop button
- Show numStrings selector (range: 3-12)
- Show scale selector (major, minor, pentatonic)
- Show baseNote selector (C4-B4)
- Trigger Tone.start() on Start button click
- Clean, minimal UI that doesn't obstruct camera view

**Key Implementation Points:**
1. Start button must call `await Tone.start()` before onStart()
2. Disable controls while running
3. Use semantic HTML (buttons, selects, labels)
4. Provide visual feedback for running state

### For Stage Component

**Location:** `src/components/Stage.tsx`

**Props:**
```typescript
interface StageProps {
  width: number;
  height: number;
  children: React.ReactNode;
}
```

**Responsibilities:**
- Container for VideoLayer, EffectLayer, and CanvasLayer
- Relative positioning
- Maintains aspect ratio
- Handles responsive sizing

**Key Implementation Points:**
1. Use `position: relative` for container
2. Children should be absolutely positioned
3. Set explicit width/height
4. Center align in page

### For VideoLayer Component

**Location:** `src/components/VideoLayer.tsx`

**Props:**
```typescript
interface VideoLayerProps {
  videoRef: RefObject<HTMLVideoElement>;
}
```

**Responsibilities:**
- Provide camera video stream as source for EffectLayer
- **Hidden from view** (opacity: 0) - silhouette only
- Mirror video horizontally for natural interaction
- Full size within Stage

**Key Implementation Points:**
1. Use `<video>` element with ref
2. Apply `transform: scaleX(-1)` for mirroring
3. Set autoPlay, playsInline attributes
4. Muted (no audio from camera)
5. **opacity: 0** - video element is needed for camera access but not displayed
6. Position absolute, full width/height

### For EffectLayer Component

**Location:** `src/components/EffectLayer.tsx`

**Props:**
```typescript
interface EffectLayerProps {
  videoRef: RefObject<HTMLVideoElement>;
  width: number;
  height: number;
}
```

**Responsibilities:**
- Render silhouette effect from video feed
- Apply brightness/contrast processing for cyberpunk aesthetic
- Real-time frame processing

**Key Implementation Points:**
1. Use canvas for pixel manipulation
2. Draw video frame to canvas
3. Apply image processing (brightness, contrast)
4. Create silhouette effect
5. Position absolute, layered above VideoLayer

### For CanvasLayer Component

**Location:** `src/components/CanvasLayer.tsx`

**Props:**
```typescript
interface CanvasLayerProps {
  canvasRef: RefObject<HTMLCanvasElement>;
  width: number;
  height: number;
  strings: LaserString[];
  hands: TrackedHand[];
  activeZones: number[];
}
```

**Responsibilities:**
- Render zone boundaries
- Draw glow effects for active zones
- Draw hand tracking visualization
- Transparent background
- Handle window resize

**Key Implementation Points:**
1. Use `<canvas>` element with ref
2. Set explicit width/height from props
3. Transparent background
4. Position absolute on top of EffectLayer
5. Pointer events: none (don't block interaction)
6. Draw zone boundary lines
7. Apply glow effect for active zones

## Implementation Steps

1. **Choose the appropriate template**
   - Use `template-functional.tsx` for ControlPanel, Stage, VideoLayer
   - Use `template-canvas.tsx` for CanvasLayer, EffectLayer

2. **Check if component already exists**
   ```bash
   # Check for existing file
   ls src/components/[ComponentName].tsx
   ```

3. **Copy template and customize**
   - Replace `[ComponentName]` with actual name
   - Define proper TypeScript interfaces for props
   - Import required types from `src/types/index.ts`

4. **Implement component logic**
   - Add state management (useState)
   - Add effects (useEffect)
   - Add event handlers
   - Follow React best practices

5. **Add styling**
   - Use className for CSS styling
   - Keep styles minimal for MVP
   - Ensure accessibility

6. **Verify TypeScript types**
   - All props have interfaces
   - No implicit any types
   - Proper return type annotations

## React Best Practices

### Hooks Usage
- Use `useState` for local state
- Use `useEffect` for side effects
- Use `useRef` for DOM refs and mutable values
- Use `useCallback` for stable callbacks (if needed)

### Performance
- Avoid premature optimization
- Use React.memo only if needed
- Keep components focused and small

### Error Handling
- Add error boundaries for critical components
- Handle null/undefined refs safely
- Provide fallback UI for errors

## Type Definitions

All types should be imported from `src/types/index.ts`:

```typescript
import type { AppConfig, Point2D, TrackedHand, LaserString } from '../types';
```

See `.claude/docs/data-structures.md` for complete type definitions.

## Examples

See `examples/` directory for complete reference implementations:
- `ControlPanel.example.tsx` - Full ControlPanel with all features
- `Stage.example.tsx` - Stage component with layout

## Common Patterns

### Controlled Components
```typescript
const ControlPanel: React.FC<ControlPanelProps> = ({ config, onConfigChange }) => {
  const handleNumStringsChange = (e: React.ChangeEvent<HTMLSelectElement>) => {
    const numStrings = parseInt(e.target.value, 10);
    onConfigChange({ ...config, numStrings });
  };

  return (
    <select value={config.numStrings} onChange={handleNumStringsChange}>
      {/* options */}
    </select>
  );
};
```

### Canvas Ref Management
```typescript
const CanvasLayer: React.FC<CanvasLayerProps> = ({ canvasRef, width, height }) => {
  useEffect(() => {
    const canvas = canvasRef.current;
    if (!canvas) return;

    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    // Drawing logic - zone boundaries and glow effects
  }, [canvasRef, width, height]);

  return <canvas ref={canvasRef} width={width} height={height} />;
};
```

### Video Ref Management (Hidden)
```typescript
const VideoLayer: React.FC<VideoLayerProps> = ({ videoRef }) => {
  return (
    <video
      ref={videoRef}
      autoPlay
      playsInline
      muted
      style={{
        transform: 'scaleX(-1)',
        opacity: 0  // Hidden - silhouette only
      }}
    />
  );
};
```

### Effect Layer Pattern
```typescript
const EffectLayer: React.FC<EffectLayerProps> = ({ videoRef, width, height }) => {
  const canvasRef = useRef<HTMLCanvasElement>(null);

  useEffect(() => {
    const canvas = canvasRef.current;
    const video = videoRef.current;
    if (!canvas || !video) return;

    const ctx = canvas.getContext('2d');
    if (!ctx) return;

    // Frame processing loop for silhouette effect
  }, [videoRef, width, height]);

  return <canvas ref={canvasRef} width={width} height={height} />;
};
```

## Styling Guidelines

### ControlPanel
- Fixed or floating position
- Non-intrusive (top or bottom)
- Clear visual hierarchy
- Sufficient contrast

### Stage
- Centered in viewport
- Responsive sizing
- Clear boundaries

### VideoLayer
- Full size within Stage
- **Hidden** (opacity: 0)
- Mirrored for natural interaction

### EffectLayer
- Full size within Stage
- Renders silhouette effect
- Layered above VideoLayer

### CanvasLayer
- Transparent background
- Same size as other layers
- Layered on top
- Zone boundaries and glow effects

## Troubleshooting

### Issue: Component not rendering
- Check if component is exported
- Verify import path
- Check for TypeScript errors

### Issue: Props not updating
- Ensure proper state lifting
- Check if parent is passing updated props
- Verify useEffect dependencies

### Issue: Ref is null
- Check if ref is passed correctly
- Ensure DOM element is mounted
- Add null checks before using ref

### Issue: Silhouette not showing
- Verify VideoLayer is receiving camera stream
- Check EffectLayer canvas dimensions
- Ensure video element is playing

## Success Criteria

After creating a component, verify:
- [ ] TypeScript compiles without errors
- [ ] Component renders without console errors
- [ ] Props are properly typed
- [ ] Responsive to prop changes
- [ ] Follows project conventions
- [ ] Accessible (semantic HTML, ARIA if needed)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reonyanarticle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
