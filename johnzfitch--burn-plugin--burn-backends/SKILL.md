---
name: burn-backends
description: This skill should be used when the user asks about "Burn backend", "WGPU", "NdArray", "Candle", "LibTorch", "custom kernel", "CubeCL", "quantization", "WebAssembly", "no_std", or backend selection and extension. Use when this capability is needed.
metadata:
  author: johnzfitch
---

# Burn Backends

Knowledge for selecting, configuring, and extending Burn compute backends.

## Available Backends

| Backend | Use Case | Features Flag |
|---------|----------|---------------|
| WGPU | GPU, cross-platform | `wgpu` |
| NdArray | CPU, testing | `ndarray` |
| Candle | Hugging Face ecosystem | `candle` |
| LibTorch | PyTorch interop | `tch` |

## Backend Selection

Choose in `Cargo.toml`:

```toml
[dependencies]
burn = { version = "0.16", features = ["wgpu"] }
```

Use in code:

```rust
use burn::backend::Wgpu;

type MyBackend = Wgpu;
// Or with autodiff:
type MyBackend = Autodiff<Wgpu>;

let device = WgpuDevice::default();
```

## Device Configuration

Each backend has its device type:

```rust
// WGPU
let device = WgpuDevice::default(); // Auto-select GPU
let device = WgpuDevice::Cpu;       // Force CPU

// NdArray (CPU only)
let device = NdArrayDevice::Cpu;

// LibTorch
let device = LibTorchDevice::Cuda(0); // GPU 0
let device = LibTorchDevice::Cpu;
```

## Backend Portability

Write backend-agnostic code with generics:

```rust
fn train<B: AutodiffBackend>(device: B::Device) {
    let model: Model<B> = ModelConfig::new().init(&device);
    // Training code works with any backend
}
```

## Custom Kernels (CubeCL)

For performance-critical operations:

```rust
use burn_cube::prelude::*;

#[cube(launch)]
fn custom_kernel<F: Float>(input: &Tensor<F>, output: &mut Tensor<F>) {
    let idx = ABSOLUTE_POS;
    output[idx] = input[idx] * F::new(2.0);
}
```

## Quantization

Reduce model size and improve inference speed:

```rust
use burn::module::Quantizer;

let quantizer = Quantizer::new(QuantizationScheme::Symmetric);
let quantized_model = quantizer.quantize(model);
```

## WebAssembly Deployment

Burn supports WASM targets:

```toml
[dependencies]
burn = { version = "0.16", features = ["wgpu", "wasm-bindgen"] }
```

Build with:
```bash
wasm-pack build --target web
```

## Additional Resources

Consult `references/topic-map-backends.md` for:
- Detailed backend comparison
- Custom WGPU kernel guide
- Quantization schemes and calibration
- No-std embedded deployment

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnzfitch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
