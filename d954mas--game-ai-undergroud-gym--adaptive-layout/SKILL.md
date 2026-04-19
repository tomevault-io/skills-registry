---
name: adaptive-layout
description: Scale-to-fit canvas layout system for games. Use when implementing or modifying game layout, resolution handling, UI scaling, text fitting, or input coordinate transformation. Ensures identical visual composition across all devices using unified scale model. Use when this capability is needed.
metadata:
  author: d954mas
---

# Adaptive Layout Strategy

This skill defines how the game handles different screen sizes, aspect ratios, and orientations.

The game uses a **scale-to-fit canvas model** with a **single unified scale** applied to both gameplay and UI.

## Core Principles

### 1. Logical vs Physical Resolution

The game operates in two coordinate spaces:

* **Logical Resolution (Game Space)** - Fixed base resolution used for all gameplay logic, grid layout, and UI composition.
* **Physical Resolution (Screen Space)** - Actual browser resolution (`window.innerWidth`, `window.innerHeight`).

All visuals are authored in logical resolution and scaled uniformly.

### 2. Scale-to-Fit as the Primary Rule

The game is a rigid canvas:

* One base resolution
* One aspect ratio
* Uniform scaling only
* No stretching
* No independent X/Y scaling

The entire game must always fit inside the visible screen.

### 3. Unified Scale Model

The game uses **one scale value**. There is no separate UI scale.

```js
uiScale = gameScale
```

Gameplay and UI always scale together, guaranteeing fully predictable sizes.

## Layout Model

### Base Resolution

Each project defines:

* `BASE_WIDTH`
* `BASE_HEIGHT`

All positions, sizes, and UI bounds are authored in this coordinate system.

### Scale Calculation

On every resize or orientation change, compute:

```js
layout(screenWidth, screenHeight) {
  const targetAspect = BASE_WIDTH / BASE_HEIGHT;
  const currentAspect = screenWidth / screenHeight;

  if (currentAspect > targetAspect) {
    // Fit height (pillarbox)
    this.gameScale = screenHeight / BASE_HEIGHT;
    this.offsetX = (screenWidth - BASE_WIDTH * this.gameScale) / 2;
    this.offsetY = 0;
  } else {
    // Fit width (letterbox)
    this.gameScale = screenWidth / BASE_WIDTH;
    this.offsetX = 0;
    this.offsetY = (screenHeight - BASE_HEIGHT * this.gameScale) / 2;
  }
}
```

### GameRect

`gameRect` is the screen-space rectangle occupied by the scaled game.

* The entire game always fits inside `gameRect`
* No part of the game is clipped
* All gameplay and UI exist within this space

## UI Strategy

### Unified UI Philosophy

UI is part of the game composition:

* UI is authored in logical resolution
* UI scales exactly together with gameplay
* UI size is always proportional to game scale

There is no responsive or fluid UI system.

### UI Overlap

UI may visually overlap gameplay elements. Correctness is ensured by consistent scale and composition, not by spatial separation.

## Layout Rearrangement Policy

By default, the game uses a **single stable layout**:

* No automatic layout rearrangement
* No portrait/landscape variants by default
* No adaptive structural changes

**Exception:** Layout rearrangement is allowed **only when explicitly requested by the user**. Do NOT introduce alternative layouts proactively.

## Input Handling

All input must be transformed from screen space to game space:

```js
gameX = (screenX - offsetX) / gameScale
gameY = (screenY - offsetY) / gameScale
```

* Input outside `gameRect` is ignored
* All interaction logic operates in logical resolution

## Rendering Rules

### Canvas Resolution

Support device pixel ratio (DPR):

```js
canvas.width  = Math.floor(width * dpr);
canvas.height = Math.floor(height * dpr);
canvas.style.width  = `${width}px`;
canvas.style.height = `${height}px`;
ctx.setTransform(dpr, 0, 0, dpr, 0, 0);
```

Rendering always uses logical coordinates.

## Typography & Localization

### Fixed Text Areas

All UI text is rendered inside **predefined logical rectangles**.

Text must adapt to the area. The area must never adapt to the text.

### Text Fitting

For any label placed inside a fixed rectangle (buttons, tabs, headers, counters), **dynamic text fitting is mandatory**:

* Text must fully fit inside its allocated area
* Fitting may be configured per label:
  * width-only
  * full rectangle (width + height)
  * single-line or multi-line

Only text scale may change.

### Localization Safety

Localized text is unpredictable. UI must never rely on string length or language. Text fitting must always be applied.

### Forbidden for Gameplay-Critical Text

* truncation
* ellipsis
* clipping / hidden overflow
* resizing UI containers to fit text

### Stability Rule

Text fitting must not:

* move UI elements
* resize UI containers
* alter layout composition

Only text scale is allowed to change.

## Supported Resolutions

The layout must remain correct under:

* 320×640
* 360×640
* 390×844
* 640×360
* arbitrary desktop resizing (e.g. 1031×580)
* very large resolutions (4K and above)

Visual composition must remain identical.

## Design Intent

This game is **not a responsive website**. It is a **scaled interactive canvas**.

The system prioritizes:

* composition over adaptability
* predictability over flexibility
* stability over device-specific optimization

## Summary

This layout system guarantees:

* one logical coordinate space
* one unified scale
* predictable UI size
* stable input mapping
* localization-safe typography
* identical visual composition on all devices

Everything scales together. Nothing adapts automatically.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/d954mas) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
