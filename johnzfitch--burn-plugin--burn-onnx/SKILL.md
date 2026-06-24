---
name: burn-onnx
description: This skill should be used when the user asks about "ONNX import", "PyTorch to Burn", "safetensors", "model conversion", "burn-import", "unsupported operator", "opset version", or importing external models into Burn. Use when this capability is needed.
metadata:
  author: johnzfitch
---

# Burn Model Import

Knowledge for importing ONNX, PyTorch, and Safetensors models into Burn.

## ONNX Import Overview

Burn uses `burn-import` crate for ONNX model conversion at build time.

## Setup

Add to `Cargo.toml`:

```toml
[dependencies]
burn = { version = "0.16", features = ["wgpu"] }

[build-dependencies]
burn-import = "0.16"
```

## Build Script

Create `build.rs`:

```rust
use burn_import::onnx::ModelGen;

fn main() {
    ModelGen::new()
        .input("models/my_model.onnx")
        .out_dir("model/")
        .run_from_script();
}
```

## Generated Code

The build generates a Rust module with:
- Model struct matching ONNX architecture
- Weights embedded or loadable
- Type-safe forward method

```rust
mod model {
    include!(concat!(env!("OUT_DIR"), "/model/my_model.rs"));
}

use model::Model;

let model = Model::<Backend>::default();
let output = model.forward(input);
```

## Operator Coverage

burn-import supports common operators. Check coverage before import:
- Fully supported: Conv, Linear, ReLU, BatchNorm, MaxPool, etc.
- Partial: Some transformer ops, dynamic shapes
- Unsupported: Custom ops, some recent opset additions

## Troubleshooting

### Unsupported Operator

If import fails with unsupported op:

1. Check if newer burn-import version supports it
2. Consider opset conversion (downgrade to supported version)
3. Implement manually and splice into model

### Shape Mismatches

ONNX uses dynamic shapes, Burn uses static:

1. Check input shapes match expected
2. Use `reshape` to convert dynamic to static
3. Verify batch dimension handling

## PyTorch Import

For PyTorch models without ONNX:

1. Export from PyTorch: `torch.onnx.export(model, dummy_input, "model.onnx")`
2. Use ONNX import flow above

Or manual weight loading:

1. Save PyTorch weights as safetensors
2. Define matching Burn architecture
3. Load weights with key remapping

## Additional Resources

Consult `references/topic-map-onnx.md` for:
- Complete operator coverage list
- Opset version compatibility
- Advanced import options
- Weight remapping patterns

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnzfitch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
