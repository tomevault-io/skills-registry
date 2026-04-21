---
name: burn-contrib
description: This skill should be used when the user asks about "contributing to Burn", "adding an operation", "burn internals", "burn-tensor", "burn-autodiff", "ONNX converter", "NodeProcessor", or Burn framework development. Use when this capability is needed.
metadata:
  author: johnzfitch
---

# Burn Contribution

Knowledge for contributing to the Burn framework itself.

## Project Architecture

Burn is organized into crates:

| Crate | Purpose |
|-------|---------|
| `burn-tensor` | Tensor API and operations |
| `burn-autodiff` | Automatic differentiation |
| `burn-wgpu` | WGPU backend |
| `burn-ndarray` | NdArray backend |
| `burn-import` | ONNX/model import |
| `burn-core` | Core traits and types |
| `burn` | User-facing re-exports |

## Adding a New Operation

### Step 1: burn-tensor

Define the operation API:

```rust
// burn-tensor/src/tensor/api/float.rs
impl<B: Backend, const D: usize> Tensor<B, D> {
    pub fn my_op(self, other: Self) -> Self {
        Tensor::new(B::float_my_op(self.primitive, other.primitive))
    }
}
```

Add backend trait method:

```rust
// burn-tensor/src/tensor/backend/base.rs
pub trait Backend {
    fn float_my_op(lhs: FloatTensor<Self>, rhs: FloatTensor<Self>) -> FloatTensor<Self>;
}
```

### Step 2: burn-autodiff

Implement gradient computation:

```rust
// burn-autodiff/src/ops/tensor.rs
impl<B: Backend> AutodiffBackend for Autodiff<B> {
    fn float_my_op(lhs: FloatTensor<Self>, rhs: FloatTensor<Self>) -> FloatTensor<Self> {
        // Track operation for backward pass
        // Implement gradient formulas
    }
}
```

### Step 3: Backend Implementations

Implement for each backend (NdArray, WGPU, etc.):

```rust
// burn-ndarray/src/ops/tensor.rs
impl Backend for NdArray {
    fn float_my_op(lhs: NdArrayTensor<f32>, rhs: NdArrayTensor<f32>) -> NdArrayTensor<f32> {
        // Actual computation
    }
}
```

## ONNX Converter

Adding support for ONNX operators:

### NodeProcessor Trait

```rust
impl NodeProcessor for MyOpProcessor {
    fn process(&self, node: &Node, graph: &mut Graph) -> Result<()> {
        // Parse ONNX node attributes
        // Generate Burn operation
    }
}
```

### Registration

Register processor in operator registry:

```rust
registry.register("MyOp", Box::new(MyOpProcessor::new()));
```

## Testing

All new operations need tests:

```rust
#[test]
fn test_my_op() {
    let tensor1 = TestTensor::from([1.0, 2.0]);
    let tensor2 = TestTensor::from([3.0, 4.0]);
    let result = tensor1.my_op(tensor2);
    result.into_data().assert_approx_eq(&[...], 4);
}
```

## Additional Resources

Consult `references/topic-map-contrib.md` for:
- Detailed crate dependency graph
- Autodiff implementation patterns
- Backend fusion/JIT integration
- PR submission guidelines

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/johnzfitch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
