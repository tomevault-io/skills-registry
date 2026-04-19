---
name: dsp-engineering
description: Audio DSP programming guidance for plugins, synthesis, and effects. Use when writing realtime audio code, implementing DSP algorithms, or building audio plugins in Rust or C++. Use when this capability is needed.
metadata:
  author: maxwellmattryan
---

# DSP Engineering

## The Audio Thread Contract

On the audio thread, you MUST NOT:
- **Allocate/deallocate memory** (no `new`, `malloc`, `Vec::push`, `Box`)
- **Lock mutexes** (use lock-free alternatives)
- **Make system calls** (no file I/O, no logging, no printing)
- **Throw/catch exceptions** (C++)
- **Call virtual functions in hot paths** (C++)

## Cardinal Rules

1. **Pre-allocate everything** before audio starts
2. **Use fixed-size buffers** - size known at initialization
3. **Smooth parameters** - never jump values (causes clicks)
4. **Process in blocks** - batch operations for cache efficiency
5. **Denormals kill performance** - flush to zero

## Quick Reference

| Task | Reference |
|------|-----------|
| Lock-free patterns, thread safety | [realtime-safety.md](realtime-safety.md) |
| Vectorization, SIMD | [simd-optimization.md](simd-optimization.md) |
| Buffer allocation, ring buffers | [buffer-management.md](buffer-management.md) |
| Avoiding clicks, interpolation | [parameter-smoothing.md](parameter-smoothing.md) |
| Biquads, IIR/FIR filters | [filter-design.md](filter-design.md) |
| Waveforms, anti-aliasing | [oscillator-design.md](oscillator-design.md) |
| Circular buffers, chorus/flanger | [delay-lines.md](delay-lines.md) |
| Compressors, limiters, gates | [dynamics-processing.md](dynamics-processing.md) |
| FFT, convolution, spectral | [fft-spectral.md](fft-spectral.md) |
| Panning, stereo, mid-side | [spatial-audio.md](spatial-audio.md) |
| Testing DSP code | [dsp-testing](../dsp-testing/SKILL.md) |
| Memory layouts, cache optimization | [data-oriented-design.md](data-oriented-design.md) |
| Mathematical foundations, theory | [dsp-mathematics](../dsp-mathematics/SKILL.md) |

## Common Formulas

```
// Frequency to angular frequency
omega = 2 * PI * freq / sample_rate

// dB to linear gain
gain = 10^(dB / 20)

// Linear gain to dB
dB = 20 * log10(gain)

// MIDI note to frequency
freq = 440 * 2^((note - 69) / 12)

// Smoothing coefficient from time constant
coeff = 1 - exp(-1 / (time_ms * sample_rate / 1000))
```

## Typical Processing Loop

```rust
fn process(&mut self, output: &mut [f32]) {
    for sample in output.iter_mut() {
        // 1. Get smoothed parameters
        let freq = self.freq_smoother.next();

        // 2. Generate/process audio
        let osc = self.oscillator.next(freq);

        // 3. Apply effects chain
        let filtered = self.filter.process(osc);

        // 4. Output with gain
        *sample = filtered * self.gain_smoother.next();
    }
}
```

## Denormal Prevention

```rust
// Rust: flush denormals
fn flush_denormal(x: f32) -> f32 {
    if x.abs() < 1e-15 { 0.0 } else { x }
}

// Or use a small DC offset in feedback paths
const DC_OFFSET: f32 = 1e-25;
```

```cpp
// C++: set FTZ/DAZ flags at plugin init
#include <xmmintrin.h>
_MM_SET_FLUSH_ZERO_MODE(_MM_FLUSH_ZERO_ON);
_MM_SET_DENORMALS_ZERO_MODE(_MM_DENORMALS_ZERO_ON);
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/maxwellmattryan) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
