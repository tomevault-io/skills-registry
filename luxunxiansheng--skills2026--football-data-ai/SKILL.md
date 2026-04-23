---
name: football-data-ai
description: Use when implementing or reviewing football data logic, pitch coordinates, event modeling, or sports vision pipelines.
metadata:
  author: luxunxiansheng
---

# Football Data AI

## Intent
Apply domain-specific logic to football-related features. This includes pitch geometry, industry-standard data formats (Opta, StatsBomb), and computer vision pipelines for sports.

## Domain Knowledge
1. **Pitch Geometry**: Standard pitch dimensions (105m x 68m). Transformation logic from 2D pixel coordinates to 3D pitch coordinates.
2. **Keypoints**: Standard football field keypoints (corners, penalty spots, center circle). Reference: FIFA/UEFA pitch regulations.
3. **Data Standards**: Support for common event types (pass, shot, foul, tackle) and tracking data formats.
4. **Physiology/Physics**: Basic understanding of ball physics and player movement patterns for better AI-assisted annotation.

## Checklist
1. **Coordinate Accuracy**: Are the pitch transformations mathematically sound?
2. **Labeling Consistency**: Are keypoints named according to the project's technical documentation?
3. **Animation Smoothness**: Do player/ball movements look realistic in the UI?

## Output Format
### Football Domain Review
- **Geometric Sanity Check**: [Pass/Fail]
- **Domain Observation**: [Observation about player/ball logic]
- **Refinement Suggestion**: [Specific tweak for sports context]

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/luxunxiansheng) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
