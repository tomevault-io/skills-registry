---
name: verde
description: | Use when this capability is needed.
metadata:
  author: steadfastasart
---

# Verde - Spatial Data Gridding

## Quick Reference

```python
import verde as vd

# Basic gridding
spline = vd.Spline()
spline.fit(coordinates, values)  # coordinates = (lon, lat) tuple
grid = spline.grid(spacing=0.1)  # Returns xarray Dataset

# Access result
elevation = grid.elevation.values

# Save output
grid.to_netcdf('output.nc')
```

## Key Classes

| Class | Purpose |
|-------|---------|
| `Spline` | Bi-harmonic spline interpolation (smooth, good extrapolation) |
| `Linear` | Delaunay triangulation (fast, no extrapolation) |
| `Cubic` | Cubic interpolation (medium smoothness) |
| `Chain` | Pipeline of processing steps |
| `BlockReduce` | Decimate data to block means/medians |
| `Trend` | Polynomial trend fitting and removal |
| `Vector` | Grid 2-component vector data |

## Essential Operations

### Grid Scattered Data
```python
coordinates = (longitude, latitude)  # Tuple of 1D arrays
values = elevation  # 1D array

spline = vd.Spline()
spline.fit(coordinates, values)
grid = spline.grid(spacing=0.1, data_names=['elevation'])
```

### Project to Cartesian
```python
import pyproj

projection = pyproj.Proj(proj='merc', lat_ts=data_lat.mean())
proj_coords = projection(longitude, latitude)

spline = vd.Spline()
spline.fit(proj_coords, values)
grid = spline.grid(spacing=1000)  # 1000m spacing
```

### Block Reduce Large Datasets
```python
import numpy as np

reducer = vd.BlockReduce(reduction=np.median, spacing=0.1)
coords_reduced, values_reduced = reducer.filter(coordinates, values)
```

### Remove Trend Before Gridding
```python
trend = vd.Trend(degree=2)  # Quadratic
trend.fit(coordinates, values)
residuals = values - trend.predict(coordinates)

# Grid residuals, then add trend back
```

### Processing Pipeline
```python
chain = vd.Chain([
    ('trend', vd.Trend(degree=1)),
    ('reduce', vd.BlockReduce(np.median, spacing=0.05)),
    ('spline', vd.Spline())
])
chain.fit(coordinates, values)
grid = chain.grid(spacing=0.01)
```

### Cross-Validation
```python
spline = vd.Spline()
scores = vd.cross_val_score(spline, coordinates, values, cv=5)
print(f"Mean R2: {scores.mean():.3f}")
```

### Mask Far from Data
```python
grid = spline.grid(spacing=0.1)
mask = vd.distance_mask(coordinates, maxdist=0.2, grid=grid)
grid_masked = grid.where(mask)
```

## Grid Parameters

| Parameter | Description |
|-----------|-------------|
| `spacing` | Grid cell size (same units as coordinates) |
| `region` | (west, east, south, north) bounds |
| `shape` | (n_north, n_east) grid dimensions |
| `adjust` | 'spacing' or 'region' - which to adjust for exact fit |

## Gridder Comparison

| Gridder | Speed | Smoothness | Extrapolation |
|---------|-------|------------|---------------|
| `Spline` | Medium | High | Good |
| `Linear` | Fast | Low | None |
| `Cubic` | Fast | Medium | None |

## When to Use vs Alternatives

| Use Case | Tool | Why |
|----------|------|-----|
| General spatial gridding | **Verde** | ML-style API, pipelines, cross-validation |
| Basic 1D/2D interpolation | **scipy.interpolate** | Simpler API, no spatial focus |
| Potential field gridding | **Harmonica** | Equivalent sources designed for gravity/magnetics |
| Command-line batch gridding | **GMT** | Powerful CLI, good for automation scripts |
| Geostatistical interpolation | **scikit-gstat / pykrige** | Variogram-based with uncertainty |
| Very large datasets (10M+ pts) | **GMT / GDAL** | Better memory handling at scale |
| Vector data (GPS velocities) | **Verde** (`Vector`) | Built-in 2-component vector gridding |
| Trend removal + gridding | **Verde** (`Chain`) | Pipeline combines steps cleanly |

**Choose Verde when**: You need a Pythonic, scikit-learn-style API for gridding
scattered spatial data with built-in cross-validation, trend removal, and pipelines.
Ideal for exploratory analysis and reproducible workflows.

**Choose scipy.interpolate when**: You have a simple interpolation task without
spatial coordinates, projections, or need for validation.

**Choose GMT when**: You need command-line batch processing of large datasets or
are integrating with shell-based workflows and need `surface` or `nearneighbor`.

## Common Workflows

### Grid Scattered Spatial Data with Validation
- [ ] Load scattered point data (coordinates + values)
- [ ] Project geographic coordinates to Cartesian if needed
- [ ] Inspect data distribution and identify clusters or gaps
- [ ] Apply `BlockReduce` to decimate dense clusters
- [ ] Remove regional trend with `Trend(degree=1)` or `Trend(degree=2)`
- [ ] Cross-validate gridder parameters with `cross_val_score()`
- [ ] Tune `Spline(damping=...)` or `Spline(mindist=...)` based on CV scores
- [ ] Fit chosen gridder (Spline, Linear, or Cubic) on residuals
- [ ] Grid onto regular spacing with `.grid()`
- [ ] Add trend back to gridded residuals
- [ ] Apply `distance_mask()` to clip extrapolation artifacts
- [ ] Visualize grid with xarray plotting: `grid.elevation.plot()`
- [ ] Save result to NetCDF with `grid.to_netcdf()`

## Common Issues

| Issue | Solution |
|-------|----------|
| Poor extrapolation | Use `distance_mask()` to mask far from data |
| Slow with large data | Use `BlockReduce` first |
| Regional trends | Remove with `Trend` before gridding |
| Wrong spacing | Check coordinate units (degrees vs meters) |

## References

- **[Gridders](references/gridders.md)** - Available gridders and parameters
- **[Cross-Validation](references/cross_validation.md)** - Parameter tuning methods

## Scripts

- **[scripts/grid_data.py](scripts/grid_data.py)** - Grid scattered data to NetCDF

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/steadfastasart) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
