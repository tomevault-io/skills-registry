---
name: devito
description: | Use when this capability is needed.
metadata:
  author: steadfastasart
---

# Devito - Symbolic PDE Solver

## Quick Reference

```python
from devito import Grid, Function, TimeFunction, Eq, solve, Operator

# Create grid
grid = Grid(shape=(101, 101), extent=(1000., 1000.))

# Velocity model
v = Function(name='v', grid=grid, space_order=4)
v.data[:] = 1500.

# Wavefield
p = TimeFunction(name='p', grid=grid, time_order=2, space_order=4)

# Wave equation: d2p/dt2 = v^2 * laplacian(p)
stencil = Eq(p.forward, solve(p.dt2 - v**2 * p.laplace, p.forward))

# Compile and run
op = Operator([stencil])
op(time_M=100, dt=0.5)
```

## Key Classes

| Class | Purpose |
|-------|---------|
| `Grid` | Computational domain definition |
| `Function` | Spatial field on grid |
| `TimeFunction` | Time-dependent field |
| `SparseTimeFunction` | Point sources/receivers |
| `Operator` | Compiled computation kernel |

## Essential Operations

### Grid and Fields
```python
from devito import Grid, Function, TimeFunction

# 2D/3D Grid
grid = Grid(shape=(nx, nz), extent=(x_size, z_size))

# Velocity model (spatial field)
v = Function(name='v', grid=grid, space_order=4)
v.data[:] = 1500.

# Wavefield (time-dependent)
p = TimeFunction(name='p', grid=grid, time_order=2, space_order=4)
```

### Source and Receivers
```python
from examples.seismic import RickerSource, Receiver, TimeAxis

time_range = TimeAxis(start=0., stop=1000., step=dt)

# Source
src = RickerSource(name='src', grid=grid, f0=10., npoint=1, time_range=time_range)
src.coordinates.data[0, :] = [500., 20.]

# Receivers
rec = Receiver(name='rec', grid=grid, npoint=101, time_range=time_range)
rec.coordinates.data[:, 0] = np.linspace(0., 1000., 101)
rec.coordinates.data[:, 1] = 20.
```

### Build and Run
```python
# Wave equation
stencil = Eq(p.forward, solve(p.dt2 - v**2 * p.laplace, p.forward))
src_term = src.inject(field=p.forward, expr=src * dt**2 * v**2)
rec_term = rec.interpolate(expr=p)

# Compile and execute
op = Operator([stencil] + src_term + rec_term)
op(time_M=nt-1, dt=dt)

# Results
shot_record = rec.data        # (nt, nrec)
snapshot = p.data[0]          # Current wavefield
```

## Symbolic Derivatives

| Syntax | Description |
|--------|-------------|
| `p.dt`, `p.dt2` | First/second time derivative |
| `p.dx`, `p.dy`, `p.dz` | Spatial derivatives |
| `p.laplace` | Laplacian (auto-adapts to dims) |
| `p.forward` | p at t+dt (time stepping) |
| `p.backward` | p at t-dt (adjoint) |

## Stability and Accuracy

**CFL Condition**: `dt < dx / (v_max * sqrt(ndim))`

| Dims | Max dt |
|------|--------|
| 2D | dx / (v_max * 1.414) |
| 3D | dx / (v_max * 1.732) |

| Space Order | Stencil Points | Error |
|-------------|----------------|-------|
| 2 | 3 | O(h^2) |
| 4 | 5 | O(h^4) |
| 8 | 9 | O(h^8) |

Higher order = more accurate but slower. Use 4-8 for production.

## When to Use vs Alternatives

| Scenario | Recommendation |
|----------|---------------|
| Seismic wave propagation (acoustic/elastic) | **Devito** - symbolic PDE, auto-optimized code |
| Full Waveform Inversion (FWI) or RTM | **Devito** - adjoint support, GPU-ready |
| Legacy seismic processing pipelines | **Madagascar** - established, large script library |
| Simple 1D/2D wave demos | **Custom NumPy** - no dependencies, easier to debug |
| General-purpose PDE solving (non-wave) | **FEniCS** - FEM-based, broader PDE support |
| Production seismic imaging at scale | **Devito** - generates optimized C code, MPI support |

**Choose Devito when**: You need high-performance finite-difference wave propagation
with symbolic equation specification. It auto-generates optimized C/OpenMP/GPU code
from Python-level math, making it ideal for FWI, RTM, and research prototyping.

**Avoid Devito when**: You need finite-element methods (use FEniCS), or simple
pedagogical examples where NumPy suffices.

## Common Workflows

### Acoustic wave forward modelling with sources and receivers

- [ ] Define `Grid` with shape and physical extent matching the velocity model
- [ ] Create velocity `Function` and populate with model values
- [ ] Create `TimeFunction` for the wavefield (time_order=2, space_order=4+)
- [ ] Verify CFL condition: `dt < dx / (v_max * sqrt(ndim))`
- [ ] Build wave equation stencil: `Eq(p.forward, solve(p.dt2 - v**2 * p.laplace, p.forward))`
- [ ] Create source (`RickerSource`) and receivers, set coordinates
- [ ] Add source injection and receiver interpolation terms
- [ ] Compile `Operator` with stencil + source + receiver terms
- [ ] Run operator: `op(time_M=nt-1, dt=dt)`
- [ ] Extract shot record from `rec.data` and plot

## References

- **[Operators and Stencils](references/operators.md)** - Detailed operator construction
- **[Performance Optimization](references/performance.md)** - GPU execution and tuning

## Scripts

- **[scripts/acoustic_wave.py](scripts/acoustic_wave.py)** - Basic acoustic wave modeling

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/steadfastasart) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
