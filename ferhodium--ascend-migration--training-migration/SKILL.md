---
name: training-migration
description: Migrate PyTorch model training code for Ascend NPU. Use when converting training loops, optimizers, data loading, and distributed training from CUDA to NPU. Use when this capability is needed.
metadata:
  author: ferhodium
---

# Training Migration for Ascend NPU

You are migrating PyTorch training code from CUDA to Ascend NPU. This skill provides guidance for:

1. **Training loop modifications** for NPU optimization
2. **Data loading optimization** for NPU
3. **Mixed precision training** with torch_npu AMP
4. **Distributed training** with HCCL
5. **Gradient management** (accumulation, checkpointing, clipping)
6. **Learning rate scheduling** for NPU
7. **Checkpointing and resuming** training
8. **Performance monitoring** and profiling

## When to Use

Invoke this skill when:
- User asks to migrate training code from CUDA to NPU
- Implementing or modifying training loops
- Setting up distributed training on NPU
- Optimizing training performance for NPU
- Configuring data loaders for NPU training

## Core Training Migration

### 1. Training Loop Structure

**Basic Training Loop Migration:**

```python
# Before (CUDA)
device = torch.device('cuda')
model = Model().to(device).cuda()
optimizer = torch.optim.Adam(model.parameters())
scaler = torch.cuda.amp.GradScaler()

for epoch in range(epochs):
    for batch in dataloader:
        inputs, labels = batch
        inputs, labels = inputs.cuda(), labels.cuda()

        optimizer.zero_grad()

        with torch.cuda.amp.autocast():
            outputs = model(inputs)
            loss = criterion(outputs, labels)

        scaler.scale(loss).backward()
        scaler.step(optimizer)
        scaler.update()

# After (NPU)
import torch_npu

device = torch.device('npu')
model = Model().to(device)
optimizer = torch.optim.Adam(model.parameters())
scaler = torch_npu.amp.GradScaler()

for epoch in range(epochs):
    for batch in dataloader:
        inputs, labels = batch
        # Use automatic data migration or explicit
        inputs, labels = inputs.to(device), labels.to(device)

        optimizer.zero_grad()

        with torch_npu.amp.autocast():
            outputs = model(inputs)
            loss = criterion(outputs, labels)

        scaler.scale(loss).backward()
        scaler.step(optimizer)
        scaler.update()
```

### 2. Mixed Precision Training (AMP)

**NPU AMP Configuration:**

```python
import torch_npu

# Check BF16 support (preferred for NPU)
if torch_npu.npu.is_bf16_supported():
    dtype = torch.bfloat16
else:
    dtype = torch.float16

# Configure GradScaler
scaler = torch_npu.amp.GradScaler(
    init_scale=2.0**10,
    growth_factor=2.0,
    backoff_factor=0.5,
    growth_interval=2000,
    enabled=True
)

# Use in training loop
with torch_npu.amp.autocast(dtype=dtype):
    outputs = model(inputs)
    loss = criterion(outputs, labels)
```

**AMP Best Practices for NPU:**
- Prefer BF16 over FP16 when available (better numerical stability)
- Use GradScaler for loss scaling
- Enable AMP after model is on NPU
- Monitor for NaN/Inf in gradients
- Adjust loss scale if needed

### 3. Data Loading Optimization

**Optimized DataLoader for NPU:**

```python
from torch.utils.data import DataLoader

# Optimized configuration
train_loader = DataLoader(
    dataset,
    batch_size=batch_size,
    shuffle=True,
    num_workers=4,  # Increase for better performance
    pin_memory=True,  # Pin memory for faster NPU transfers
    prefetch_factor=2,  # Prefetch batches
    persistent_workers=True,  # Keep workers alive
    drop_last=True  # Drop last incomplete batch
)

# Collate function with device placement
def npu_collate_fn(batch):
    inputs, labels = zip(*batch)
    # Move to NPU in collate for efficiency
    return (
        torch.stack(inputs).to(device, non_blocking=True),
        torch.stack(labels).to(device, non_blocking=True)
    )

train_loader = DataLoader(
    dataset,
    batch_size=batch_size,
    collate_fn=npu_collate_fn,
    # ... other config
)
```

### 4. Distributed Training (HCCL)

**Multi-NPU Training Setup:**

```python
import torch.distributed as dist
import os

def setup_distributed():
    # Initialize process group with HCCL
    dist.init_process_group(
        backend='hccl',  # Use HCCL for NPU
        init_method='env://'
    )

    local_rank = int(os.environ.get('LOCAL_RANK', 0))
    world_size = dist.get_world_size()

    # Set device for this process
    torch.npu.set_device(local_rank)
    device = torch.device(f'npu:{local_rank}')

    return device, local_rank, world_size

# Wrap model with DDP
from torch.nn.parallel import DistributedDataParallel as DDP

device, local_rank, world_size = setup_distributed()
model = model.to(device)
model = DDP(
    model,
    device_ids=[local_rank],
    output_device=local_rank,
    broadcast_buffers=True,
    find_unused_parameters=False
)

# Distributed sampler
from torch.utils.data.distributed import DistributedSampler

train_sampler = DistributedSampler(
    dataset,
    num_replicas=world_size,
    rank=local_rank,
    shuffle=True,
    drop_last=True
)

train_loader = DataLoader(
    dataset,
    batch_size=batch_size,
    sampler=train_sampler,
    num_workers=4,
    pin_memory=True
)

# Training loop with distributed sampler
for epoch in range(epochs):
    train_sampler.set_epoch(epoch)  # Shuffle each epoch
    for batch in train_loader:
        # ... training code
```

**Environment Setup for Multi-NPU:**
```bash
# Required environment variables
export ASCEND_VISIBLE_DEVICES=0,1,2,3
export HCCL_WHITELIST_DISABLE=1
export HCCL_INTRA_ROCE_SIZE=8  # Adjust based on network
export HCCL_INTER_ROCE_SIZE=8

# Launch with torchrun or torch.distributed.launch
torchrun --nproc_per_node=4 train.py
```

### 5. Gradient Accumulation

**Gradient Accumulation for Large Batch Sizes:**

```python
# Gradient accumulation setup
accumulation_steps = 4
effective_batch_size = batch_size * accumulation_steps * world_size

optimizer.zero_grad()
for i, batch in enumerate(train_loader):
    inputs, labels = batch
    inputs, labels = inputs.to(device), labels.to(device)

    with torch_npu.amp.autocast():
        outputs = model(inputs)
        loss = criterion(outputs, labels)
        loss = loss / accumulation_steps  # Scale loss

    scaler.scale(loss).backward()

    # Update weights every accumulation_steps
    if (i + 1) % accumulation_steps == 0:
        scaler.step(optimizer)
        scaler.update()
        optimizer.zero_grad()
```

### 6. Gradient Clipping

**Gradient Clipping with NPU AMP:**

```python
# Clip gradients before optimizer step
scaler.scale(loss).backward()

# Unscale gradients before clipping
scaler.unscale_(optimizer)

# Clip gradients
torch.nn.utils.clip_grad_norm_(
    model.parameters(),
    max_norm=1.0,
    norm_type=2.0
)

# Step optimizer
scaler.step(optimizer)
scaler.update()
```

### 7. Gradient Checkpointing

**Memory-Efficient Training with Gradient Checkpointing:**

```python
from torch.utils.checkpoint import checkpoint

class CheckpointedModel(nn.Module):
    def __init__(self, base_model):
        super().__init__()
        self.base_model = base_model

    def forward(self, x):
        # Checkpoint memory-intensive layers
        x = checkpoint(self.base_model.layer1, x)
        x = checkpoint(self.base_model.layer2, x)
        x = checkpoint(self.base_model.layer3, x)
        return self.base_model.layer4(x)

# Use in training
model = CheckpointedModel(base_model).to(device)
```

### 8. Learning Rate Scheduling

**Learning Rate Schedulers for NPU:**

```python
from torch.optim.lr_scheduler import (
    OneCycleLR,
    CosineAnnealingLR,
    LinearLR,
    SequentialLR
)

# OneCycleLR (recommended for NPU)
scheduler = OneCycleLR(
    optimizer,
    max_lr=1e-3,
    total_steps=total_steps,
    pct_start=0.3,
    anneal_strategy='cos',
    div_factor=25,
    final_div_factor=1e4
)

# Or cosine annealing with warmup
warmup_scheduler = LinearLR(
    optimizer,
    start_factor=0.1,
    total_iters=warmup_epochs
)
main_scheduler = CosineAnnealingLR(
    optimizer,
    T_max=total_epochs - warmup_epochs
)
scheduler = SequentialLR(
    optimizer,
    schedulers=[warmup_scheduler, main_scheduler],
    milestones=[warmup_epochs]
)

# Step scheduler after optimizer
for epoch in range(epochs):
    for batch in train_loader:
        # ... training code
        scaler.step(optimizer)
        scaler.update()
        scheduler.step()
```

### 9. Checkpointing and Resuming

**Save and Load Training State:**

```python
def save_checkpoint(state, filename, checkpoint_dir='./checkpoints'):
    os.makedirs(checkpoint_dir, exist_ok=True)
    filepath = os.path.join(checkpoint_dir, filename)
    torch.save(state, filepath)

def load_checkpoint(filename, model, optimizer, scaler, scheduler=None):
    checkpoint = torch.load(filename, map_location='npu')
    model.load_state_dict(checkpoint['model_state_dict'])
    optimizer.load_state_dict(checkpoint['optimizer_state_dict'])
    scaler.load_state_dict(checkpoint['scaler_state_dict'])
    if scheduler and 'scheduler_state_dict' in checkpoint:
        scheduler.load_state_dict(checkpoint['scheduler_state_dict'])
    return checkpoint.get('epoch', 0), checkpoint.get('best_metric', None)

# Save checkpoint
save_checkpoint({
    'epoch': epoch,
    'model_state_dict': model.state_dict(),
    'optimizer_state_dict': optimizer.state_dict(),
    'scaler_state_dict': scaler.state_dict(),
    'scheduler_state_dict': scheduler.state_dict(),
    'best_metric': best_accuracy,
}, f'checkpoint_epoch_{epoch}.pt')

# Load and resume
start_epoch, best_metric = load_checkpoint(
    'checkpoint_epoch_10.pt',
    model,
    optimizer,
    scaler,
    scheduler
)
```

### 10. Performance Monitoring

**Training Monitoring and Profiling:**

```python
import time
from contextlib import contextmanager

@contextmanager
def npu_profiler(enabled=True):
    if not enabled:
        yield
        return

    torch.npu.synchronize()
    start = time.time()
    yield
    torch.npu.synchronize()
    end = time.time()

# Profile training
for epoch in range(epochs):
    epoch_start = time.time()

    for i, batch in enumerate(train_loader):
        with npu_profiler():
            # Forward pass
            with torch_npu.amp.autocast():
                outputs = model(inputs)
                loss = criterion(outputs, labels)

            # Backward pass
            scaler.scale(loss).backward()
            scaler.step(optimizer)
            scaler.update()

        # Logging
        if i % 100 == 0:
            # Memory usage
            mem_allocated = torch.npu.memory_allocated() / 1e9
            mem_reserved = torch.npu.memory_reserved() / 1e9

            print(f"Epoch {epoch}, Step {i}: "
                  f"Loss: {loss.item():.4f}, "
                  f"Mem: {mem_allocated:.2f}GB allocated, "
                  f"{mem_reserved:.2f}GB reserved")

    epoch_time = time.time() - epoch_start
    print(f"Epoch {epoch} completed in {epoch_time:.2f}s")
```

**Use msprof for detailed profiling:**
```bash
# Enable profiling
export ASCEND_GLOBAL_LOG_LEVEL=1
export ASCEND_SLOG_PRINT_TO_STDOUT=1

# Run with msprof
msprof --application="python train.py" --output="./profiler_data"
```

## Common Training Patterns

### Pattern 1: Basic Training Loop with All Optimizations

```python
import torch_npu
from torch.nn.parallel import DistributedDataParallel as DDP
from torch.utils.data.distributed import DistributedSampler

# Setup
device = torch.device('npu')
model = Model().to(device)
model = DDP(model, device_ids=[local_rank])

optimizer = torch.optim.AdamW(model.parameters(), lr=1e-3, weight_decay=0.01)
scaler = torch_npu.amp.GradScaler()
scheduler = OneCycleLR(optimizer, max_lr=1e-3, total_steps=total_steps)

# Training
model.train()
for epoch in range(epochs):
    train_sampler.set_epoch(epoch)

    for batch in train_loader:
        inputs, labels = batch
        inputs, labels = inputs.to(device, non_blocking=True), labels.to(device, non_blocking=True)

        optimizer.zero_grad()

        with torch_npu.amp.autocast(dtype=torch.bfloat16):
            outputs = model(inputs)
            loss = criterion(outputs, labels)

        scaler.scale(loss).backward()
        scaler.unscale_(optimizer)
        torch.nn.utils.clip_grad_norm_(model.parameters(), max_norm=1.0)
        scaler.step(optimizer)
        scaler.update()
        scheduler.step()
```

### Pattern 2: Validation Loop

```python
def validate(model, val_loader, device):
    model.eval()
    total_loss = 0
    correct = 0
    total = 0

    with torch.no_grad():
        for batch in val_loader:
            inputs, labels = batch
            inputs, labels = inputs.to(device, non_blocking=True), labels.to(device, non_blocking=True)

            with torch_npu.amp.autocast(dtype=torch.bfloat16):
                outputs = model(inputs)
                loss = criterion(outputs, labels)

            total_loss += loss.item()
            _, predicted = outputs.max(1)
            total += labels.size(0)
            correct += predicted.eq(labels).sum().item()

    avg_loss = total_loss / len(val_loader)
    accuracy = 100. * correct / total
    return avg_loss, accuracy
```

## Common Pitfalls

### 1. Missing NPU Synchronization

```python
# Wrong - may measure incorrect time
start = time.time()
output = model(input)
end = time.time()

# Correct - synchronize before timing
torch.npu.synchronize()
start = time.time()
output = model(input)
torch.npu.synchronize()
end = time.time()
```

### 2. Incorrect Pin Memory

```python
# Wrong - pin_memory without num_workers
loader = DataLoader(dataset, pin_memory=True)

# Correct - pin_memory with num_workers
loader = DataLoader(dataset, num_workers=4, pin_memory=True)
```

### 3. Forgetting to Set Model to Train/Eval

```python
# Wrong - model stays in eval mode during training
model.eval()
for batch in train_loader:
    # training code

# Correct - set to train mode
model.train()
for batch in train_loader:
    # training code

model.eval()
with torch.no_grad():
    # validation code
```

### 4. Not Handling GradScaler State in Checkpoints

```python
# Wrong - doesn't save scaler
torch.save({'model': model.state_dict()}, 'checkpoint.pt')

# Correct - saves scaler
torch.save({
    'model': model.state_dict(),
    'optimizer': optimizer.state_dict(),
    'scaler': scaler.state_dict()
}, 'checkpoint.pt')
```

## Tools to Use

**Documentation First:**
- Read official Ascend documentation before migration:
  - https://www.hiascend.com/doc_center/source/zh/Pytorch/730/ptmoddevg/trainingmigrguide/PT_LMTMOG_0002.html

**Code Analysis:**
- Use `Grep` to find training-related patterns
- Use `Read` to examine training scripts
- Use `Glob` to find training files

## Output Requirements

When generating migrated training code:
1. All Python files must be syntactically correct
2. Include comprehensive error handling
3. Add logging for monitoring
4. Include checkpointing/resuming functionality
5. Support distributed training with HCCL
6. Enable mixed precision training with torch_npu AMP
7. Optimize data loading for NPU
8. Include performance profiling hooks
9. Document all NPU-specific changes
10. Provide training configuration files (YAML/JSON)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ferhodium) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
