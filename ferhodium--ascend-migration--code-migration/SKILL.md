---
name: code-migration
description: Migrate CUDA code to Ascend NPU code. Use when implementing PyTorch to NPU migration, replacing CUDA APIs with torch_npu equivalents. Use when this capability is needed.
metadata:
  author: ferhodium
---

# Code Migration: CUDA to Ascend NPU

You are implementing CUDA to NPU code migration. This skill provides guidance for:

1. **API replacements** from CUDA to NPU
2. **Code patterns** that need changes
3. **Best practices** for NPU migration
4. **Common pitfalls** to avoid
5. **Testing and validation** approach

## When to Use

Invoke this skill when:
- User asks to migrate code from CUDA to NPU
- Implementing torch_npu API changes
- Writing NPU-specific code
- Fixing NPU migration issues

## Core API Mappings

### Device Placement

```python
# Old (CUDA)
device = torch.device('cuda')
model = model.cuda()
tensor = tensor.to('cuda')
tensor = tensor.cuda()

# New (NPU)
device = torch.device('npu')
model = model.npu()  # or model.to('npu')
tensor = tensor.to('npu')
tensor = tensor.npu()
```

### Device Properties

```python
# CUDA → NPU
torch.cuda.device_count() → torch.npu.device_count()
torch.cuda.current_device() → torch.npu.current_device()
torch.cuda.get_device_name(i) → torch.npu.get_device_name(i)
torch.cuda.set_device(i) → torch.npu.set_device(i)
torch.cuda.current_stream() → torch.npu.current_stream()
torch.cuda.Stream() → torch.npu.Stream()
torch.cuda.Event() → torch.npu.Event()
```

### Memory Management

```python
# CUDA → NPU
torch.cuda.empty_cache() → torch.npu.empty_cache()
torch.cuda.memory_allocated() → torch.npu.memory_allocated()
torch.cuda.max_memory_allocated() → torch.npu.max_memory_allocated()
torch.cuda.memory_reserved() → torch.npu.memory_reserved()
torch.cuda.ipc_collect() → torch.npu.ipc_collect()
torch.cuda.synchronize() → torch.npu.synchronize()
```

### Automatic Mixed Precision (AMP)

```python
# CUDA
from torch.cuda.amp import autocast, GradScaler
scaler = GradScaler()
with autocast():
    output = model(input)
scaler.scale(loss).backward()
scaler.step(optimizer)
scaler.update()

# NPU
from torch_npu import amp
scaler = amp.GradScaler()
with amp.autocast():
    output = model(input)
scaler.scale(loss).backward()
scaler.step(optimizer)
scaler.update()
```

### Distributed Training

```python
# CUDA (NCCL backend)
torch.distributed.init_process_group(
    backend='nccl',
    init_method='env://'
)
model = torch.nn.parallel.DistributedDataParallel(model)

# NPU (HCCL backend)
torch.distributed.init_process_group(
    backend='hccl',
    init_method='env://'
)
model = torch.nn.parallel.DistributedDataParallel(model)

# Note: torch_npu automatically uses HCCL when on NPU
```

### Custom Operations

```python
# Flash Attention → NPU Fusion Attention
# Old
from flash_attn import flash_attn_func
output = flash_attn_func(q, k, v)

# New
import torch_npu
output = torch_npu.npu_fusion_attention(q, k, v)[0]

# Or use PyTorch native (works on NPU)
output = F.scaled_dot_product_attention(q, k, v)
```

## Migration Patterns

### Pattern 1: Model Initialization

```python
# Before
class MyModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(3, 64, 3)
        self.fc = nn.Linear(64, 10)

    def forward(self, x):
        return self.fc(self.conv1(x).cuda())

# After
class MyModel(nn.Module):
    def __init__(self):
        super().__init__()
        self.conv1 = nn.Conv2d(3, 64, 3)
        self.fc = nn.Linear(64, 10)

    def forward(self, x):
        # Remove explicit .cuda(), rely on automatic data migration
        return self.fc(self.conv1(x))
```

### Pattern 2: Training Loop

```python
# Before
device = torch.device('cuda')
model = Model().to(device).cuda()
optimizer = torch.optim.Adam(model.parameters())

for epoch in range(epochs):
    for batch in dataloader:
        inputs, labels = batch
        inputs, labels = inputs.cuda(), labels.cuda()

        with torch.cuda.amp.autocast():
            outputs = model(inputs)
            loss = criterion(outputs, labels)

        scaler.scale(loss).backward()
        scaler.step(optimizer)
        scaler.update()
        torch.cuda.empty_cache()

# After
device = torch.device('npu')
model = Model().to(device).npu()  # Explicitly place on NPU
optimizer = torch.optim.Adam(model.parameters())

from torch_npu import amp
scaler = amp.GradScaler()

for epoch in range(epochs):
    for batch in dataloader:
        inputs, labels = batch
        # Automatic data migration handles this
        # But explicit is also fine
        inputs, labels = inputs.to(device), labels.to(device)

        with amp.autocast():
            outputs = model(inputs)
            loss = criterion(outputs, labels)

        scaler.scale(loss).backward()
        scaler.step(optimizer)
        scaler.update()
        torch.npu.empty_cache()
```

### Pattern 3: Multi-GPU/NPU Training

```python
# Before
import torch.distributed as dist
dist.init_process_group(backend='nccl')
local_rank = int(os.environ['LOCAL_RANK'])
torch.cuda.set_device(local_rank)
model = model.to(local_rank)
model = torch.nn.parallel.DistributedDataParallel(
    model, device_ids=[local_rank]
)

# After
import torch.distributed as dist
dist.init_process_group(backend='hccl')
local_rank = int(os.environ['LOCAL_RANK'])
torch.npu.set_device(local_rank)
model = model.to(local_rank)
model = torch.nn.parallel.DistributedDataParallel(
    model, device_ids=[local_rank]
)

# Environment variables needed:
# export ASCEND_VISIBLE_DEVICES=0,1,2,3
# export HCCL_WHITELIST_DISABLE=1
```

## Common Pitfalls

### 1. Missing torch_npu Import

```python
# Wrong - will fail
import torch
from torch.cuda.amp import autocast  # CUDA-specific

# Correct
import torch
import torch_npu  # Import torch_npu
from torch_npu import amp  # NPU-specific
```

### 2. Hardcoded CUDA Strings

```python
# Wrong
device = torch.device('cuda:0')
tensor = tensor.to('cuda')

# Correct - use 'npu'
device = torch.device('npu:0')
tensor = tensor.to('npu')

# Or use dynamic device detection
device = torch.device('npu' if torch.npu.is_available() else 'cpu')
```

### 3. Forgetting Backend Change in Distributed

```python
# Wrong - still uses NCCL
dist.init_process_group(backend='nccl')

# Correct - use HCCL for NPU
dist.init_process_group(backend='hccl')
```

### 4. CUDA-specific Checks

```python
# Wrong
if torch.cuda.is_available():
    device = torch.device('cuda')

# Correct
if torch.npu.is_available():
    device = torch.device('npu')
elif torch.cuda.is_available():
    device = torch.device('cuda')
else:
    device = torch.device('cpu')
```

## Best Practices

1. **Use automatic data migration** when possible
   - Reduces explicit `.to('npu')` calls
   - torch_npu handles data movement automatically

2. **Enable mixed precision training**
   - Provides 2-4x speedup
   - Reduces memory usage by 50%
   - Use `torch_npu.amp.autocast`

3. **Profile before optimizing**
   - Use npu-smi to monitor NPU usage
   - Identify actual bottlenecks
   - Don't optimize prematurely

4. **Test on small batches first**
   - Verify correctness
   - Check for errors
   - Then scale up

5. **Handle NPU unavailability**
   - Provide CPU fallback
   - Clear error messages
   - Graceful degradation

## Testing Strategy

### 1. Correctness Validation

```python
# Compare NPU vs CPU outputs
model_cpu = Model()
model_npu = Model().to('npu')

model_cpu.load_state_dict(model_npu.cpu().state_dict())

with torch.no_grad():
    output_cpu = model_cpu(inputs)
    output_npu = model_npu(inputs.to('npu'))

# Check numerical accuracy
assert torch.allclose(output_cpu, output_npu.cpu(), rtol=1e-3, atol=1e-5)
```

### 2. Performance Testing

```python
import time

# Benchmark NPU performance
model = Model().to('npu')
inputs = inputs.to('npu')

# Warmup
for _ in range(10):
    _ = model(inputs)

# Measure
torch.npu.synchronize()
start = time.time()
for _ in range(100):
    _ = model(inputs)
torch.npu.synchronize()
end = time.time()

avg_time = (end - start) / 100
print(f"Average time: {avg_time:.4f}s")
print(f"Throughput: {1/avg_time:.2f} samples/sec")
```

### 3. Memory Testing

```python
# Monitor memory usage
print(f"Memory allocated: {torch.npu.memory_allocated() / 1e9:.2f} GB")
print(f"Max memory: {torch.npu.max_memory_allocated() / 1e9:.2f} GB")

# Check for leaks
torch.npu.empty_cache()
```

## Tools to Use

**Documentation First:**
- Read official Ascend documentation before migration:
  - https://www.hiascend.com/doc_center/source/zh/Pytorch/730/ptmoddevg/trainingmigrguide/PT_LMTMOG_0002.html
  - https://www.hiascend.com/doc_center/source/zh/canncommercial/850/API/aolapi/operatorlist_00001.html

**Code Modification:**
- Use `Read` and `Edit` for code modifications
- Use `Grep` to find CUDA patterns
- Use `Bash` to test migrated code

## Output Requirements

When generating migrated code:
1. All Python files must be syntactically correct
2. Include docstrings explaining NPU changes
3. Add inline comments for critical modifications
4. Import torch_npu where needed
5. Include error handling for NPU operations
6. Provide migration summary document

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ferhodium) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
