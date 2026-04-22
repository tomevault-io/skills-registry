---
name: maplibre-gl-js
description: Expert guidance for Maplibre GL JS - an open-source TypeScript library for rendering interactive maps using WebGL. Use when users need help with map initialization, styling, layers, sources, markers, popups, controls, data visualization, or interactive features. Use when this capability is needed.
metadata:
  author: barisariburnu
---

# Maplibre GL JS Expert

Expert guidance for Maplibre GL JS - a powerful, open-source TypeScript library that uses WebGL to render interactive maps from vector tiles.

## When to Use This Skill

Use this skill when users ask about:

- **Setup & Installation**: Installing Maplibre GL JS, basic map initialization, framework integration
- **Map Configuration**: Map options, camera controls, bounds, projections
- **Styling**: Map styles, style specification, themes, custom styling
- **Layers**: Adding/removing layers, layer types (fill, line, symbol, circle, heatmap, etc.)
- **Sources**: Vector tiles, GeoJSON, raster sources, real-time data
- **Markers & Popups**: Adding markers, custom markers, popups, tooltips
- **Interactivity**: Click events, hover effects, feature queries, user interactions
- **Controls**: Zoom, navigation, scale, geolocate, custom controls
- **Data Visualization**: Heatmaps, clustering, 3D extrusions, data-driven styling
- **Performance**: Optimization, large datasets, vector tiles
- **Integration**: React, Vue, Angular, TypeScript usage

## Core Concepts

### What is Maplibre GL JS?

Maplibre GL JS is an **open-source TypeScript library** that:

- ✅ Renders interactive maps using **WebGL**
- ✅ Uses **vector tiles** for efficient, scalable maps
- ✅ Supports **custom styling** via MapLibre Style Spec
- ✅ Provides **smooth animations** and camera transitions
- ✅ Works on **web, mobile, and desktop** platforms
- ✅ Fork of Mapbox GL JS (v1.x), fully **community-driven**

### Key Features

- **Vector Tiles**: Efficient, scalable map rendering
- **WebGL Rendering**: Hardware-accelerated graphics
- **Style Specification**: JSON-based styling system
- **Interactive**: Click, hover, drag, and touch events
- **Data-Driven**: Style based on feature properties
- **3D Support**: Terrain, buildings, custom 3D models
- **Open Source**: MIT licensed, no API keys required

## Installation

### Using npm/bun

```bash
# Install with bun
bun add maplibre-gl

# Or with npm
npm install maplibre-gl
```

**TypeScript Types** are included by default - no separate `@types/maplibre-gl` package needed.

### CSS Import (with Bundlers)

```typescript
import "maplibre-gl/dist/maplibre-gl.css";
```

### Using CDN

```html
<!DOCTYPE html>
<html>
  <head>
    <meta charset="utf-8" />
    <title>Maplibre GL JS</title>
    <meta name="viewport" content="width=device-width, initial-scale=1" />

    <!-- Maplibre GL JS CSS -->
    <link
      href="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.css"
      rel="stylesheet"
    />

    <!-- Maplibre GL JS -->
    <script src="https://unpkg.com/maplibre-gl@latest/dist/maplibre-gl.js"></script>

    <style>
      body {
        margin: 0;
        padding: 0;
      }
      #map {
        position: absolute;
        top: 0;
        bottom: 0;
        width: 100%;
      }
    </style>
  </head>
  <body>
    <div id="map"></div>

    <script>
      const map = new maplibregl.Map({
        container: "map",
        style: "https://demotiles.maplibre.org/style.json",
        center: [0, 0],
        zoom: 1,
      });
    </script>
  </body>
</html>
```

### Content Security Policy (CSP)

If you're using CSP, you may need to add the following directives for MapLibre GL JS:

```http
Content-Security-Policy: script-src 'self' 'unsafe-eval' 'unsafe-inline' blob: data:; worker-src 'self' blob: data:; connect-src 'self' blob: data:; img-src 'self' blob: data:; font-src 'self' blob: data:; style-src 'self' 'unsafe-inline';
```

## Quick Start

### Basic Map Setup

```typescript
import maplibregl from "maplibre-gl";
import "maplibre-gl/dist/maplibre-gl.css";

const map = new maplibregl.Map({
  container: "map", // div id
  style: "https://demotiles.maplibre.org/style.json", // map style
  center: [-74.5, 40], // starting position [lng, lat]
  zoom: 9, // starting zoom
});
```

### Map Container

**HTML:**

```html
<div id="map" style="width: 100%; height: 500px;"></div>
```

**Important**: The map container must have a defined width and height.

### Map Options

```typescript
const map = new maplibregl.Map({
  container: "map",
  style: "https://demotiles.maplibre.org/style.json",

  // Initial view
  center: [lng, lat],
  zoom: 12,
  bearing: 0, // rotation (0-360)
  pitch: 0, // tilt (0-60)

  // Bounds (alternative to center/zoom)
  // bounds: [[-74.5, 40], [-73.5, 41]],

  // Interaction
  interactive: true,
  dragPan: true,
  dragRotate: true,
  scrollZoom: true,
  doubleClickZoom: true,
  touchZoomRotate: true,
  touchPitch: true,
  keyboard: true,

  // Limits
  minZoom: 0,
  maxZoom: 22,
  minPitch: 0,
  maxPitch: 60,
  maxBounds: [
    [-180, -90],
    [180, 90],
  ], // restrict panning

  // Performance
  preserveDrawingBuffer: false,
  antialias: false,

  // Hash in URL
  hash: false, // set to true for #map=zoom/lat/lng

  // Terrain
  terrain: {
    source: "terrain-source",
    exaggeration: 1.5,
  },
});
```

## Working with Styles

### Using Pre-made Styles

```typescript
// Maplibre demo tiles (free)
const map = new maplibregl.Map({
  style: "https://demotiles.maplibre.org/style.json",
});

// Custom style URL
const map = new maplibregl.Map({
  style: "https://api.maptiler.com/maps/streets/style.json?key=YOUR_KEY",
});
```

### Inline Style Object

```typescript
const map = new maplibregl.Map({
  container: "map",
  style: {
    version: 8,
    sources: {
      "osm-tiles": {
        type: "raster",
        tiles: [
          "https://a.tile.openstreetmap.org/{z}/{x}/{y}.png",
          "https://b.tile.openstreetmap.org/{z}/{x}/{y}.png",
          "https://c.tile.openstreetmap.org/{z}/{x}/{y}.png",
        ],
        tileSize: 256,
        attribution: "© OpenStreetMap contributors",
      },
    },
    layers: [
      {
        id: "osm-layer",
        type: "raster",
        source: "osm-tiles",
      },
    ],
  },
  center: [0, 0],
  zoom: 2,
});
```

### Changing Style Dynamically

```typescript
// Load new style
map.setStyle("https://demotiles.maplibre.org/style.json");

// Wait for style to load
map.on("style.load", () => {
  console.log("Style loaded");
});
```

## Camera Controls

### Jump (Instant)

```typescript
map.jumpTo({
  center: [-74.5, 40],
  zoom: 12,
  bearing: 45,
  pitch: 30,
});
```

### Ease (Animated)

```typescript
map.easeTo({
  center: [-74.5, 40],
  zoom: 12,
  duration: 2000, // milliseconds
  easing: (t) => t, // linear easing
});
```

### Fly (Curved Path)

```typescript
map.flyTo({
  center: [-74.5, 40],
  zoom: 12,
  speed: 1.2, // animation speed
  curve: 1, // curve intensity
  essential: true, // animation runs even if prefers-reduced-motion
});
```

### Fit Bounds

```typescript
// Fit map to bounding box
map.fitBounds(
  [
    [-74.5, 40], // southwest
    [-73.5, 41], // northeast
  ],
  {
    padding: 20, // pixels
    maxZoom: 15,
    duration: 1000,
  }
);
```

### Get Current Camera

```typescript
const center = map.getCenter(); // LngLat object
const zoom = map.getZoom();
const bearing = map.getBearing();
const pitch = map.getPitch();

console.log(`Center: ${center.lng}, ${center.lat}`);
console.log(`Zoom: ${zoom}`);
```

## Adding Markers

### Basic Marker

```typescript
import maplibregl from "maplibre-gl";

const marker = new maplibregl.Marker().setLngLat([-74.5, 40]).addTo(map);
```

### Custom Color

```typescript
const marker = new maplibregl.Marker({ color: "#FF0000" })
  .setLngLat([-74.5, 40])
  .addTo(map);
```

### Custom HTML Marker

```typescript
// Create custom element
const el = document.createElement("div");
el.className = "custom-marker";
el.style.backgroundImage = "url(/marker-icon.png)";
el.style.width = "32px";
el.style.height = "32px";
el.style.backgroundSize = "100%";

const marker = new maplibregl.Marker({ element: el })
  .setLngLat([-74.5, 40])
  .addTo(map);
```

### Draggable Marker

```typescript
const marker = new maplibregl.Marker({ draggable: true })
  .setLngLat([-74.5, 40])
  .addTo(map);

// Listen to drag events
marker.on("dragend", () => {
  const lngLat = marker.getLngLat();
  console.log(`Marker moved to: ${lngLat.lng}, ${lngLat.lat}`);
});
```

### Marker with Popup

```typescript
const popup = new maplibregl.Popup({ offset: 25 }).setHTML(
  "<h3>Location Name</h3><p>Description here</p>"
);

const marker = new maplibregl.Marker()
  .setLngLat([-74.5, 40])
  .setPopup(popup)
  .addTo(map);
```

### Remove Marker

```typescript
marker.remove();
```

## Working with Popups

### Basic Popup

```typescript
const popup = new maplibregl.Popup()
  .setLngLat([-74.5, 40])
  .setHTML("<h3>Hello World!</h3>")
  .addTo(map);
```

### Popup Options

```typescript
const popup = new maplibregl.Popup({
  closeButton: true,
  closeOnClick: true,
  closeOnMove: false,
  anchor: "bottom", // 'top', 'bottom', 'left', 'right'
  offset: 25, // pixels
  className: "custom-popup",
  maxWidth: "300px",
})
  .setLngLat([-74.5, 40])
  .setHTML("<h3>Popup Content</h3>")
  .addTo(map);
```

### Popup on Click

```typescript
map.on("click", "poi-layer", (e) => {
  const coordinates = e.features[0].geometry.coordinates.slice();
  const description = e.features[0].properties.description;

  new maplibregl.Popup().setLngLat(coordinates).setHTML(description).addTo(map);
});

// Change cursor on hover
map.on("mouseenter", "poi-layer", () => {
  map.getCanvas().style.cursor = "pointer";
});

map.on("mouseleave", "poi-layer", () => {
  map.getCanvas().style.cursor = "";
});
```

## Adding Sources and Layers

### GeoJSON Source

```typescript
map.on("load", () => {
  // Add source
  map.addSource("points", {
    type: "geojson",
    data: {
      type: "FeatureCollection",
      features: [
        {
          type: "Feature",
          geometry: {
            type: "Point",
            coordinates: [-74.5, 40],
          },
          properties: {
            title: "Location 1",
            description: "Description here",
          },
        },
      ],
    },
  });

  // Add layer
  map.addLayer({
    id: "points-layer",
    type: "circle",
    source: "points",
    paint: {
      "circle-radius": 8,
      "circle-color": "#FF0000",
      "circle-stroke-width": 2,
      "circle-stroke-color": "#FFFFFF",
    },
  });
});
```

### Vector Tile Source

```typescript
map.addSource("vector-tiles", {
  type: "vector",
  tiles: ["https://example.com/tiles/{z}/{x}/{y}.pbf"],
  minzoom: 0,
  maxzoom: 14,
});

map.addLayer({
  id: "buildings",
  type: "fill-extrusion",
  source: "vector-tiles",
  "source-layer": "building", // layer name in vector tile
  paint: {
    "fill-extrusion-color": "#aaa",
    "fill-extrusion-height": ["get", "height"],
    "fill-extrusion-base": ["get", "min_height"],
    "fill-extrusion-opacity": 0.6,
  },
});
```

### MapLibre Tiles (MLT) Encoding

MapLibre GL JS supports MapLibre Tiles (MLT) encoding for vector sources:

```typescript
map.addSource("vector-tiles-mlt", {
  type: "vector",
  tiles: ["https://example.com/tiles/{z}/{x}/{y}.mlt"],
  encoding: "mlt", // Use MLT encoding
  minzoom: 0,
  maxzoom: 14,
});

map.addLayer({
  id: "buildings-mlt",
  type: "fill-extrusion",
  source: "vector-tiles-mlt",
  "source-layer": "building",
  paint: {
    "fill-extrusion-color": "#aaa",
    "fill-extrusion-height": ["get", "height"],
    "fill-extrusion-base": ["get", "min_height"],
    "fill-extrusion-opacity": 0.6,
  },
});
```

### Raster Source

```typescript
map.addSource("satellite", {
  type: "raster",
  tiles: ["https://a.tile.openstreetmap.org/{z}/{x}/{y}.png"],
  tileSize: 256,
});

map.addLayer({
  id: "satellite-layer",
  type: "raster",
  source: "satellite",
  paint: {
    "raster-opacity": 0.8,
  },
});
```

### Update GeoJSON Source

```typescript
const source = map.getSource("points");
if (source && source.type === "geojson") {
  source.setData({
    type: "FeatureCollection",
    features: newFeatures,
  });
}
```

## Layer Types

### Circle Layer

```typescript
map.addLayer({
  id: "circles",
  type: "circle",
  source: "points",
  paint: {
    "circle-radius": 10,
    "circle-color": "#007cbf",
    "circle-opacity": 0.8,
    "circle-stroke-width": 2,
    "circle-stroke-color": "#fff",
  },
});
```

### Symbol Layer (Icons/Text)

```typescript
// First, load the image
map.loadImage("/marker-icon.png", (error, image) => {
  if (error) throw error;
  map.addImage("custom-marker", image);

  // Then add the layer
  map.addLayer({
    id: "symbols",
    type: "symbol",
    source: "points",
    layout: {
      "icon-image": "custom-marker",
      "icon-size": 1,
      "icon-allow-overlap": true,
      "text-field": ["get", "title"],
      "text-font": ["Open Sans Semibold"],
      "text-offset": [0, 1.5],
      "text-anchor": "top",
    },
    paint: {
      "text-color": "#000",
      "text-halo-color": "#fff",
      "text-halo-width": 2,
    },
  });
});
```

### Line Layer

```typescript
map.addLayer({
  id: "route",
  type: "line",
  source: "route-source",
  layout: {
    "line-join": "round",
    "line-cap": "round",
  },
  paint: {
    "line-color": "#888",
    "line-width": 8,
    "line-opacity": 0.8,
  },
});
```

### Fill Layer

```typescript
map.addLayer({
  id: "polygon",
  type: "fill",
  source: "polygon-source",
  paint: {
    "fill-color": "#088",
    "fill-opacity": 0.4,
    "fill-outline-color": "#000",
  },
});
```

### Heatmap Layer

```typescript
map.addLayer({
  id: "heatmap",
  type: "heatmap",
  source: "earthquakes",
  paint: {
    "heatmap-weight": [
      "interpolate",
      ["linear"],
      ["get", "magnitude"],
      0,
      0,
      6,
      1,
    ],
    "heatmap-intensity": ["interpolate", ["linear"], ["zoom"], 0, 1, 9, 3],
    "heatmap-color": [
      "interpolate",
      ["linear"],
      ["heatmap-density"],
      0,
      "rgba(0,0,255,0)",
      0.1,
      "royalblue",
      0.3,
      "cyan",
      0.5,
      "lime",
      0.7,
      "yellow",
      1,
      "red",
    ],
    "heatmap-radius": 20,
    "heatmap-opacity": 0.8,
  },
});
```

## Event Handling

### Map Events

```typescript
// Map loaded
map.on("load", () => {
  console.log("Map loaded");
});

// Map moved
map.on("move", () => {
  console.log("Map is moving");
});

// Zoom changed
map.on("zoom", () => {
  console.log(`Zoom level: ${map.getZoom()}`);
});

// Map clicked
map.on("click", (e) => {
  console.log(`Clicked at: ${e.lngLat.lng}, ${e.lngLat.lat}`);
});
```

### Layer Events

```typescript
// Click on layer
map.on("click", "poi-layer", (e) => {
  const feature = e.features[0];
  console.log("Clicked feature:", feature.properties);
});

// Hover on layer
map.on("mouseenter", "poi-layer", (e) => {
  map.getCanvas().style.cursor = "pointer";

  // Optional: highlight feature
  if (e.features.length > 0) {
    const feature = e.features[0];
    map.setFeatureState({ source: "points", id: feature.id }, { hover: true });
  }
});

map.on("mouseleave", "poi-layer", () => {
  map.getCanvas().style.cursor = "";
});
```

## Controls

### Navigation Control

```typescript
map.addControl(new maplibregl.NavigationControl(), "top-right");
```

### Scale Control

```typescript
map.addControl(
  new maplibregl.ScaleControl({
    maxWidth: 100,
    unit: "metric", // or 'imperial'
  }),
  "bottom-left"
);
```

### Fullscreen Control

```typescript
map.addControl(new maplibregl.FullscreenControl(), "top-right");
```

### Geolocate Control

```typescript
const geolocate = new maplibregl.GeolocateControl({
  positionOptions: {
    enableHighAccuracy: true,
  },
  trackUserLocation: true,
  showUserHeading: true,
});

map.addControl(geolocate, "top-right");

// Trigger on load
map.on("load", () => {
  geolocate.trigger();
});
```

### Custom Control

```typescript
class CustomControl {
  onAdd(map) {
    this._map = map;
    this._container = document.createElement("div");
    this._container.className = "maplibregl-ctrl";

    const button = document.createElement("button");
    button.textContent = "Custom Action";
    button.onclick = () => {
      console.log("Custom control clicked");
    };

    this._container.appendChild(button);
    return this._container;
  }

  onRemove() {
    this._container.parentNode.removeChild(this._container);
    this._map = undefined;
  }
}

map.addControl(new CustomControl(), "top-left");
```

## Best Practices

### Wait for Map Load

```typescript
// ✅ Good: Wait for map to load
map.on("load", () => {
  map.addSource("data", {
    /* ... */
  });
  map.addLayer({
    /* ... */
  });
});

// ❌ Bad: Don't add layers immediately
map.addLayer({
  /* ... */
}); // May fail if map isn't loaded
```

### Remove Event Listeners

```typescript
function handleClick(e) {
  console.log("Clicked:", e.lngLat);
}

// Add listener
map.on("click", handleClick);

// Remove when done
map.off("click", handleClick);
```

### Clean Up on Unmount

```typescript
// React example
useEffect(() => {
  const map = new maplibregl.Map({
    /* ... */
  });

  return () => {
    map.remove(); // Clean up
  };
}, []);
```

### Performance: Use Clustering

```typescript
map.addSource("earthquakes", {
  type: "geojson",
  data: "/earthquakes.geojson",
  cluster: true,
  clusterMaxZoom: 14,
  clusterRadius: 50,
});

// Cluster circles
map.addLayer({
  id: "clusters",
  type: "circle",
  source: "earthquakes",
  filter: ["has", "point_count"],
  paint: {
    "circle-color": [
      "step",
      ["get", "point_count"],
      "#51bbd6",
      100,
      "#f1f075",
      750,
      "#f28cb1",
    ],
    "circle-radius": ["step", ["get", "point_count"], 20, 100, 30, 750, 40],
  },
});

// Cluster count
map.addLayer({
  id: "cluster-count",
  type: "symbol",
  source: "earthquakes",
  filter: ["has", "point_count"],
  layout: {
    "text-field": "{point_count_abbreviated}",
    "text-font": ["DIN Offc Pro Medium"],
    "text-size": 12,
  },
});
```

## Common Patterns

### Loading Indicator

```typescript
const loader = document.getElementById("loader");

map.on("dataloading", () => {
  loader.style.display = "block";
});

map.on("idle", () => {
  loader.style.display = "none";
});
```

### Query Features

```typescript
// Query rendered features at a point
map.on("click", (e) => {
  const features = map.queryRenderedFeatures(e.point, {
    layers: ["poi-layer"],
  });

  if (features.length > 0) {
    console.log("Found features:", features);
  }
});

// Query all features from a source
const features = map.querySourceFeatures("my-source", {
  sourceLayer: "my-source-layer",
  filter: ["==", "type", "restaurant"],
});
```

## Reference Documentation

For detailed API reference, advanced patterns, and examples:

- **[API Reference](references/api.md)**: Complete Map, Marker, Popup API
- **[Examples](references/examples.md)**: Common use cases and patterns

## Troubleshooting

### Map Not Displaying

```typescript
// Check container has dimensions
#map {
  width: 100%;
  height: 500px; /* Must have explicit height */
}

// Check style is loaded
map.on('error', (e) => {
  console.error('Map error:', e);
});
```

### Images Not Loading

```typescript
// Load image before using in layer
map.loadImage("/icon.png", (error, image) => {
  if (error) {
    console.error("Failed to load image:", error);
    return;
  }
  map.addImage("custom-icon", image);
});
```

### TypeScript Errors

```typescript
// Import types
import maplibregl, { Map, Marker, Popup } from "maplibre-gl";

// Type the map
const map: Map = new maplibregl.Map({
  /* ... */
});
```

## Quick Reference

```typescript
// Installation
bun add maplibre-gl

// Basic map
import maplibregl from 'maplibre-gl';
import 'maplibre-gl/dist/maplibre-gl.css';

const map = new maplibregl.Map({
  container: 'map',
  style: 'https://demotiles.maplibre.org/style.json',
  center: [0, 0],
  zoom: 2
});

// Add marker
new maplibregl.Marker()
  .setLngLat([0, 0])
  .addTo(map);

// Add popup
new maplibregl.Popup()
  .setLngLat([0, 0])
  .setHTML('<h3>Hello</h3>')
  .addTo(map);

// Add GeoJSON
map.on('load', () => {
  map.addSource('data', {
    type: 'geojson',
    data: geojsonData
  });

  map.addLayer({
    id: 'layer',
    type: 'circle',
    source: 'data'
  });
});
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/barisariburnu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
