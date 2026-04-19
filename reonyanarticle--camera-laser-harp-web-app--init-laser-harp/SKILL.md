---
name: init-laser-harp
description: Initialize the Camera Laser Harp MVP project with React, TypeScript, Vite, MediaPipe Hands, and Tone.js. Use when starting the project from scratch. Use when this capability is needed.
metadata:
  author: reonyanarticle
---

# Initialize Laser Harp Project

## When to Use This Skill

- User wants to create the project from scratch
- Project directory exists but has no `src/` or `package.json`
- User explicitly requests "initialize project" or "set up laser harp"
- No React/Vite project structure exists yet

## What This Skill Does

1. Creates React + TypeScript + Vite project structure
2. Installs required dependencies (MediaPipe Hands, Tone.js)
3. Sets up initial directory structure following the architecture
4. Creates base configuration files
5. Generates initial type definitions
6. Creates README with startup instructions

## Tech Stack (MVP)

- **Frontend:** React 18 + TypeScript 5
- **Build:** Vite 4
- **Canvas:** Canvas 2D API
- **Hand Detection:** MediaPipe Hands (@mediapipe/hands, @mediapipe/camera_utils)
- **Audio:** Tone.js (Sawtooth + Distortion for noisy cyberpunk sound)
- **Visual Effects:** Silhouette effect with brightness/contrast processing
- **Target Browsers:** Chrome, Edge

## Dependencies to Install

### Production Dependencies

```json
{
  "@mediapipe/hands": "^0.4.1646424915",
  "@mediapipe/camera_utils": "^0.3.1632432234",
  "tone": "^14.7.77",
  "react": "^18.2.0",
  "react-dom": "^18.2.0"
}
```

### Development Dependencies

```json
{
  "@types/react": "^18.2.0",
  "@types/react-dom": "^18.2.0",
  "@vitejs/plugin-react": "^4.0.0",
  "typescript": "^5.0.0",
  "vite": "^4.3.0"
}
```

## Directory Structure to Create

```
src/
├── components/
│   ├── App.tsx
│   ├── ControlPanel.tsx
│   ├── ControlPanel.css
│   ├── Stage.tsx
│   ├── Stage.css
│   ├── VideoLayer.tsx
│   ├── CanvasLayer.tsx
│   └── EffectLayer.tsx        # Silhouette effect rendering
├── hooks/
│   ├── useCameraStream.ts
│   ├── useHandTracker.ts
│   ├── useLaserHarpEngine.ts
│   └── useResponsiveDimensions.ts  # Responsive stage sizing
├── services/
│   └── audioEngine.ts         # Tone.js with Sawtooth + Distortion
├── types/
│   └── index.ts
├── utils/
│   ├── algorithms.ts          # Zone calculation algorithms
│   └── imageEffects.ts        # Silhouette image processing
├── styles/
│   └── index.css
├── main.tsx
└── App.css
```

## Key Design Decisions (Current Implementation)

### Zone-Based Detection
- Screen is divided into zones (default: 4 zones)
- Each zone corresponds to one string/note
- Zone occlusion is detected by hand presence in zone

### Position-Based Pitch Control
- X coordinate within zone affects pitch
- Provides expressive control beyond simple note triggering

### Silhouette Effect
- Video layer is hidden (opacity: 0)
- EffectLayer renders processed silhouette
- Cyberpunk aesthetic with brightness/contrast processing

### Audio Engine
- Sawtooth oscillator for harsh, industrial sound
- Distortion effect for added edge
- Inspired by Susumu Hirasawa's performance style

## Implementation Steps

### Step 1: Initialize Vite Project

If `package.json` does NOT exist, run:

```bash
npm create vite@latest . -- --template react-ts
```

This creates the basic React + TypeScript + Vite structure.

### Step 2: Install Dependencies

```bash
npm install @mediapipe/hands @mediapipe/camera_utils tone
```

All dev dependencies are already included by the Vite template.

### Step 3: Create Directory Structure

```bash
mkdir -p src/components src/hooks src/services src/types src/utils src/styles
```

### Step 4: Create Type Definitions

Create `src/types/index.ts` with all type definitions from `.claude/docs/data-structures.md`:

```typescript
// AppConfig, Point2D, TrackedHand, LaserString, Zone
// Copy from data-structures.md
```

Refer to `.claude/docs/data-structures.md` for complete type definitions.

### Step 5: Create Components

Create component files following the established patterns:

- **App.tsx** - Main application with state management
- **ControlPanel.tsx** - UI controls for settings
- **Stage.tsx** - Container for visual layers
- **VideoLayer.tsx** - Hidden camera feed source (opacity: 0)
- **CanvasLayer.tsx** - Zone boundaries and glow effects
- **EffectLayer.tsx** - Silhouette rendering

### Step 6: Create Hooks

- **useCameraStream.ts** - Camera access and management
- **useHandTracker.ts** - MediaPipe Hands integration
- **useLaserHarpEngine.ts** - Zone detection and audio triggering
- **useResponsiveDimensions.ts** - Responsive sizing

### Step 7: Create Audio Engine

**src/services/audioEngine.ts:**
- Singleton pattern
- Sawtooth oscillator
- Distortion effect
- Position-based pitch modulation

### Step 8: Create Utilities

**src/utils/algorithms.ts:**
- Zone position calculation
- String layout algorithms

**src/utils/imageEffects.ts:**
- Silhouette processing
- Brightness/contrast adjustment

### Step 9: Verify Setup

Run the development server to verify:

```bash
npm run dev
```

Open the browser and confirm the app loads without errors.

## Default Configuration

```typescript
{
  numStrings: 4,          // Number of zones/strings
  scale: "pentatonic",    // Pentatonic scale
  baseNote: "C4",         // Base note C4
  noteDuration: "8n",     // Eighth note
  cooldownMs: 100         // Cooldown between triggers
}
```

## Configuration Files

The Vite template already creates appropriate configuration files:

- `vite.config.ts` - Standard React Vite config
- `tsconfig.json` - Strict TypeScript config
- `tsconfig.node.json` - Config for Vite Node

No modifications needed for MVP.

## Post-Initialization

After initialization, the user can:

1. Use the `create-component` Skill to implement each component
2. Implement hooks for camera, hand tracking, and laser harp engine
3. Set up MediaPipe Hands integration
4. Configure Tone.js audio engine with Sawtooth + Distortion
5. Add silhouette effect processing

## Reference

See `reference.md` for:
- Complete MVP specification (goals, scope, user stories)
- Functional and non-functional requirements
- Task breakdown
- Constraints and deliverables

## Architecture Reference

For detailed architecture information, see:
- `.claude/docs/architecture.md` - Component hierarchy and data flow
- `.claude/docs/data-structures.md` - Complete type definitions
- `.claude/docs/algorithms.md` - Algorithm specifications

## Troubleshooting

### Issue: npm create vite fails
- Ensure Node.js 18+ is installed
- Try `npm cache clean --force`

### Issue: MediaPipe installation fails
- MediaPipe packages are large (~50MB)
- Ensure stable internet connection
- Try installing separately: `npm install @mediapipe/hands`

### Issue: TypeScript errors after initialization
- Run `npm install` to ensure all dependencies are installed
- Check that `tsconfig.json` exists and is valid
- Restart TypeScript server in your IDE

## Success Criteria

After initialization, verify:
- [ ] `package.json` exists with all dependencies
- [ ] `node_modules/` contains @mediapipe and tone packages
- [ ] `src/` directory structure is complete
- [ ] `npm run dev` starts the development server
- [ ] Browser shows "Camera Laser Harp" page without errors
- [ ] TypeScript compilation succeeds

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/reonyanarticle) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
