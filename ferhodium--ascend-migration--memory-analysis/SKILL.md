---
name: memory-analysis
description: Analyze memory access patterns and optimization opportunities for Ascend NPU. Use when examining data loading, host-device transfers, mixed precision training, and memory efficiency. Use when this capability is needed.
metadata:
  author: ferhodium
---

# Memory Analysis for Ascend NPU

You are analyzing memory patterns for Ascend NPU optimization. This skill helps identify:

1. **Data loading patterns** and optimization opportunities
2. **Host-device transfers** (CPU ↔ NPU)
3. **Automatic data migration** effectiveness
4. **Mixed precision** opportunities (FP16/BF16)
5. **Memory efficiency** improvements

## When to Use

Invoke this skill when:
- User asks about memory optimization for NPU
- Examining data loading and training pipelines
- Looking for memory inefficiencies
- Planning mixed precision training strategy

## Analysis Approach

### 1. Data Loading Analysis

Examine:
- DataLoader configuration
- Batch size settings
- Number of workers
- Pin memory usage (`.pin_memory=True`)
- Prefetching strategies

**Search Patterns:**
```bash
grep -rn "DataLoader" <repo_path>
grep -rn "num_workers" <repo_path>
grep -rn "pin_memory" <repo_path>
```

### 2. Host-Device Transfer Patterns

Identify data movement:
```python
# Explicit transfers
tensor = tensor.to('cuda')  # → .to('npu') or automatic
model = model.cuda()  # → .npu() or automatic

# Check for inefficient patterns
# - Redundant transfers
# - Transferring large unused data
# - Frequent CPU↔GPU bouncing
```

### 3. Automatic Data Migration

torch_npu provides automatic data migration:
- Tensors automatically move to NPU when needed
- Reduces explicit `.to('npu')` calls
- But may not cover all cases

**Analyze:**
- Will automatic migration work for this codebase?
- Are there cases preventing automatic migration?
- Performance impact of automatic vs explicit

### 4. Mixed Precision Training

**FP16/BF16 Benefits on Ascend:**
- 50% memory reduction
- 2-4x speedup on supported operations
- Better NPU utilization

**Check for:**
```python
# Existing AMP usage
torch.cuda.amp.autocast  # → torch.npu.amp.autocast
torch.cuda.amp.GradScaler  # → torch.npu.amp.GradScaler

# Opportunities for AMP
# - Float32 models that can use FP16
# - Loss scaling requirements
```

### 5. Memory Efficiency Techniques

Identify opportunities:
- **Gradient checkpointing**: Trade compute for memory
- **Gradient accumulation**: Simulate larger batch sizes
- **Memory pool reuse**: NPU-specific memory optimization
- **Tensor lifecycle**: Proper cleanup to avoid leaks

## Output Format

### Data Loading Analysis
- DataLoader configuration summary
- Optimization opportunities:
  - Increase workers
  - Enable pin_memory (for NPU)
  - Adjust prefetch_factor
  - Use persistent_workers

### Host-Device Transfer
- Current transfer patterns
- Redundant or inefficient transfers
- Automatic migration compatibility
- Recommendations:
  - Use automatic data migration where possible
  - Minimize explicit transfers
  - Keep frequently accessed data on NPU

### Mixed Precision Opportunities
- Current precision usage (FP32/FP16)
- FP16/BF16 compatibility with torch.npu.amp
- Expected memory savings (up to 50%)
- Expected speedup (2-4x on supported ops)
- Implementation:
  ```python
  # Enable torch_npu AMP
  from torch_npu import amp
  scaler = amp.GradScaler()
  with amp.autocast():
      output = model(input)
  ```

### Memory Efficiency
- Gradient checkpointing opportunities
- Gradient accumulation for large effective batch sizes
- Memory pool optimization strategies
- Potential memory leaks or improper cleanup

### Specific torch_npu APIs

Recommend specific APIs:
```python
# Memory management
torch.npu.empty_cache()  # Clear unused memory
torch.npu.set_memory_strategy()  # Memory allocation strategy
torch.npu.memory_allocated()  # Current memory usage
torch.npu.max_memory_allocated()  # Peak memory

# AMP
from torch_npu import amp
amp.autocast()  # Automatic mixed precision
amp.GradScaler()  # Loss scaling
```

## Tools to Use

**Documentation First:**
- Read official Ascend documentation before analysis:
  - https://www.hiascend.com/doc_center/source/zh/Pytorch/730/ptmoddevg/trainingmigrguide/PT_LMTMOG_0002.html

**Memory Analysis:**
- Use `Grep` to search for memory-related patterns
- Use `Read` to examine data loading code

## Notes

- Ascend NPU has different memory hierarchy than GPU
- HBM (High Bandwidth Memory) is precious resource
- Automatic data migration reduces code changes
- Mixed precision training highly recommended for NPU
- Profile actual memory usage with npu-smi

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ferhodium) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
