---
name: performance
description: > Use when this capability is needed.
metadata:
  author: stormingluke
---

## Go Performance Principles

### Profiling First
- Never optimise without profiling — measure before changing
- See `PERF.md` for the full profiling workflow, platform-specific notes,
  and containerised tooling setup (Pyroscope, Grafana)
- CPU profile: `go tool pprof cpu.prof`
- Memory profile: `go tool pprof -alloc_space mem.prof`
- Goroutine profile: check for leaks and excessive goroutines
- Trace: `go tool trace trace.out` for latency analysis
- Use `net/http/pprof` endpoints in services for live profiling
- Use `fgprof` for wall-clock profiling (captures I/O wait + CPU)

### Benchmarking
- Use `testing.B` with `b.ReportAllocs()`
- Run with `go test -bench=. -benchmem -count=10`
- Compare with `benchstat old.txt new.txt`
- Benchmark realistic data sizes, not just small inputs
- Use `b.ResetTimer()` after expensive setup
- Store benchmark results: `tee .perf/$(date +%Y-%m-%d)/bench.txt`

### Allocation Reduction (Uber style)
- Pre-allocate slices: `make([]T, 0, expectedCap)`
- Pre-allocate maps: `make(map[K]V, expectedSize)`
- Use `sync.Pool` for frequently allocated/deallocated objects
- Prefer `strconv` over `fmt` for primitive conversions (significantly faster)
- Use `strings.Builder` for string concatenation in loops
- Avoid `fmt.Sprintf` in hot paths
- Avoid interface boxing in hot paths (causes heap allocation)
- Use pointer receivers to avoid copying large structs
- Check escape analysis: `go build -gcflags='-m'`
- Avoid repeated string-to-byte conversions — cache the result

### Concurrency Performance
- Use `sync.Map` only for read-heavy workloads with stable keys
- Prefer sharded maps with `sync.RWMutex` for write-heavy workloads
- Bound goroutine creation with worker pools or semaphores
- Use `runtime.GOMAXPROCS()` awareness for CPU-bound work
- Channels should have size zero or one — profile before using larger buffers
- Use `go.uber.org/goleak` in tests to detect goroutine leaks

### HTTP / Network
- Reuse `http.Client` and `http.Transport` — never create per-request
- Set `MaxIdleConns`, `MaxIdleConnsPerHost`, `IdleConnTimeout`
- Use `context.WithTimeout` for all outbound calls
- Enable HTTP/2 where supported
- Use connection pooling for database clients

### Kubernetes Controller Performance
- Use informer caches — never list from the API server in reconcile
- Use predicates to filter events before they reach the reconciler
- Set appropriate `MaxConcurrentReconciles` for the workload
- Use `client.Reader` (cached) for reads, `client.Writer` for writes
- Avoid unnecessary status updates — compare before writing

### Continuous Profiling (Production)
- Use Grafana Pyroscope for continuous profiling in production
- Push mode: `github.com/grafana/pyroscope-go` SDK
- Pull mode: Grafana Alloy scraping pprof endpoints
- See `PERF.md` for Docker Compose and Kubernetes deployment
- Record findings in beads: `bd create "Perf: finding" --type note --labels perf`

### Recording Results
After profiling, record findings in beads for persistent memory:
```bash
bd create "Perf: description of finding" --type note --labels perf,context
bd create "Baseline: metric at N resources" --type note --labels perf,baseline
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stormingluke) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
