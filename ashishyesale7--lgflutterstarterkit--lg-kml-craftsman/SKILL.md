---
name: lg-kml-craftsman
description: Artistic KML composition skill — combines standard KML elements into visually compelling multi-layer compositions for Liquid Galaxy. Handles advanced features like 3D extrusions, time animations, gradient styling, and panoramic tour design. Use when this capability is needed.
metadata:
  author: ashishyesale7
---

# LG KML Craftsman — Artistic KML Composition

Goes beyond basic placemarks to craft **visually stunning** KML compositions
that take full advantage of Google Earth's rendering capabilities on a
multi-screen Liquid Galaxy rig.

**Announce:** "KML Craftsman activated. Let's make the rig look extraordinary."

## When to Invoke
- After `lg-viz-architect` designs the experience storyboard.
- After `lg-kml-writer` handles basic KML generation patterns.
- When the student wants to elevate a visualization from functional to impressive.

## Craft Techniques

### 1. Multi-Layer Composition
Combine multiple KML documents for depth:

```
Layer 1: Base placemarks (data points with icons)
Layer 2: Connecting lines (LineString between related points)
Layer 3: 3D extrusions (extruded polygons for magnitude/value)
Layer 4: Screen overlay (legend + branding on slave screen)
Layer 5: Camera tour (gx:Tour tying it all together)
```

Send each layer to the LG rig in sequence to build up the visualization.

### 2. Color Palettes for Data
Design intentional color schemes (KML uses `aaBBGGRR` format):

| Use Case | Low | Medium | High | Critical |
|----------|-----|--------|------|----------|
| Earthquake magnitude | `ff00ff00` (green) | `ff00ffff` (yellow) | `ff0080ff` (orange) | `ff0000ff` (red) |
| Temperature | `ffff8800` (blue) | `ff00ff00` (green) | `ff00ffff` (yellow) | `ff0000ff` (red) |
| Altitude/depth | `ffffffff` (white) | `ff888888` (gray) | `ff444444` (dark) | `ff000000` (black) |

### 3. 3D Extruded Geometry
Use `<extrude>` and `<altitudeMode>` for volumetric visualization:

```xml
<Placemark>
  <name>M 6.2 Earthquake</name>
  <Style>
    <PolyStyle>
      <color>990000ff</color>
      <outline>1</outline>
    </PolyStyle>
  </Style>
  <Polygon>
    <extrude>1</extrude>
    <altitudeMode>relativeToGround</altitudeMode>
    <outerBoundaryIs>
      <LinearRing>
        <coordinates>
          <!-- Circle approximation around epicenter -->
          <!-- Height proportional to magnitude -->
        </coordinates>
      </LinearRing>
    </outerBoundaryIs>
  </Polygon>
</Placemark>
```

### 4. Time-Based Animation
Use `<TimeStamp>` and `<TimeSpan>` for temporal data:

```xml
<Placemark>
  <TimeStamp><when>2026-02-17T10:30:00Z</when></TimeStamp>
  <name>Event at 10:30</name>
  <Point><coordinates>-122.42,37.77,0</coordinates></Point>
</Placemark>
```

Google Earth's time slider will animate the data chronologically.
Use `<TimeSpan>` for events with duration (long-running observations).

### 5. Tour Choreography
Design multi-stop tours with variety:

```xml
<gx:Tour>
  <name>Earthquake Tour 2026</name>
  <gx:Playlist>
    <!-- Act 1: Global overview -->
    <gx:FlyTo>
      <gx:duration>4</gx:duration>
      <gx:flyToMode>bounce</gx:flyToMode>
      <!-- High altitude, tilt 0 (top-down) -->
    </gx:FlyTo>
    <gx:Wait><gx:duration>3</gx:duration></gx:Wait>

    <!-- Act 2: Dive to strongest quake -->
    <gx:FlyTo>
      <gx:duration>3</gx:duration>
      <gx:flyToMode>smooth</gx:flyToMode>
      <!-- Low altitude, tilt 60 (dramatic) -->
    </gx:FlyTo>
    <gx:AnimatedUpdate>
      <!-- Open balloon with details -->
    </gx:AnimatedUpdate>
    <gx:Wait><gx:duration>5</gx:duration></gx:Wait>

    <!-- Act 3: Orbit -->
    <!-- 36 FlyTo steps, 10° heading increment each -->

    <!-- Act 4: Pull back to regional view -->
    <gx:FlyTo>
      <gx:duration>3</gx:duration>
      <gx:flyToMode>smooth</gx:flyToMode>
    </gx:FlyTo>
  </gx:Playlist>
</gx:Tour>
```

### 6. Balloon HTML Styling
Design rich info balloons with HTML/CSS:

```html
<div style="font-family: 'Segoe UI', Arial, sans-serif;
            background: linear-gradient(135deg, #1a1a2e 0%, #16213e 100%);
            padding: 16px; border-radius: 8px; color: #e0e0e0;
            min-width: 280px;">
  <h2 style="color: #4fc3f7; margin: 0 0 12px 0;">{{title}}</h2>
  <table style="width: 100%; border-collapse: collapse;">
    <tr>
      <td style="color: #90a4ae; padding: 4px 8px;">Magnitude</td>
      <td style="color: #fff; font-weight: bold;">{{magnitude}}</td>
    </tr>
    <!-- More rows -->
  </table>
</div>
```

### 7. Screen Overlay Design
Create polished overlays for slave screens:

- **Logo**: Bottom-left corner, ~15% screen width, transparent PNG.
- **Legend**: Bottom-right corner, shows color scale for the current visualization.
- **Title bar**: Top-center, app name + current visualization mode.

### 8. Line and Route Styling
For path-based data (routes, fault lines, migration paths):

```xml
<Style>
  <LineStyle>
    <color>ff4fc3f7</color>
    <width>3</width>
  </LineStyle>
</Style>
<LineString>
  <tessellate>1</tessellate>
  <coordinates>
    lon1,lat1,0 lon2,lat2,0 lon3,lat3,0
  </coordinates>
</LineString>
```

## Craft Checklist
Before finalizing any KML composition:

- [ ] All coordinates use `longitude,latitude,altitude` order
- [ ] Colors use `aaBBGGRR` format (not `#RRGGBB`)
- [ ] XML entities are escaped (`&amp;`, `&lt;`, etc.)
- [ ] Total payload < 2 MB
- [ ] Tested on at least one Google Earth instance
- [ ] Tour duration is reasonable (30s–3min for demos)
- [ ] Balloons render correctly in Google Earth's embedded browser
- [ ] 3D extrusions have sensible altitude values (not 0 or absurdly high)

## Output
Produces KML composition code (Dart methods) and any new KML templates.

## ⛔️ Student Interaction — MANDATORY

**After crafting each KML composition, STOP and show the student:**
1. A preview of the generated KML in Google Earth Pro (or link to KML reference).
2. Explain the `aaBBGGRR` color format and `lon,lat,alt` coordinate order.
3. Ask: *"What KML element creates this visual effect? How would you change the color/size?"*
4. If the student cannot modify the KML confidently, link to **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md).

**DO NOT auto-craft the next composition** until the student understands the current one.

## Reference Links

- **KML Reference (Google)**: https://developers.google.com/kml/documentation/kmlreference
- **KML Tutorial**: https://developers.google.com/kml/documentation/kml_tut
- **Lucia's KML patterns**: https://github.com/LiquidGalaxyLAB/LG-Master-Web-App
- **gx:Tour (animated tours)**: https://developers.google.com/kml/documentation/touring
- For deeper study → **lg-learning-resources** (.agent/skills/lg-learning-resources/SKILL.md)

## Handoff
Passes composed KML code to `lg-data-pipeline` for integration into the
transport flow, and to `lg-code-reviewer` for quality checks.

## 🔗 Skill Chain

After the KML composition is crafted and the student understands the visual design, **automatically offer the next stage**:

> *"KML composition is looking stunning! The multi-layer design will really pop on the video wall. Let's get this reviewed for code quality and KML correctness. Ready for the Code Review? 🔍"*

If student says "ready" → activate `.agent/skills/lg-code-reviewer/SKILL.md`.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ashishyesale7) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
