---
name: dependency-analysis
description: Analyze Python package dependencies for Ascend NPU compatibility. Use when examining requirements.txt, setup.py, environment files, and checking for CUDA-dependent packages. Use when this capability is needed.
metadata:
  author: ferhodium
---

# Dependency Analysis for Ascend NPU

You are analyzing dependencies for Ascend NPU compatibility. This skill helps identify:

1. **CUDA-dependent packages** that need replacement
2. **Version compatibility** with torch_npu and CANN
3. **Conflicts** with Ascend software stack
4. **NPU-compatible alternatives** to CUDA packages

## When to Use

Invoke this skill when:
- User asks about dependency compatibility
- Examining requirements files or environment configurations
- Checking for CUDA-specific package dependencies
- Planning environment setup for Ascend

## Analysis Approach

### 1. Examine Dependency Files

Read these files from the repository:
- `requirements.txt`
- `setup.py` (check `install_requires`)
- `pyproject.toml` (check `dependencies`)
- `environment.yml` (conda environments)
- `Pipfile`

### 2. Key Compatibility Checks

**PyTorch Version:**
- torch_npu 2.1.0+ requires PyTorch 2.1.0+
- Check PyTorch version in dependencies
- Verify torch_npu compatibility

**CUDA-Dependent Packages to Flag:**
- `cupy` - CUDA NumPy replacement
- `cudf` - CUDA DataFrame library
- `cuml` - CUDA ML library
- `spacy-cuda` - CUDA-accelerated spaCy
- `flash-attn` - Flash Attention (has NPU equivalent)
- `apex` - NVIDIA APEX utilities
- `xformers` - Transformer optimizations
- `triton` - GPU programming language

**Known Incompatibilities:**
- Packages with hard-coded CUDA kernels
- Libraries requiring NVIDIA-specific cuDNN/cuBLAS
- Packages with no NPU support

### 3. Version Constraints

**Ascend Stack Requirements:**
- CANN: 8.0+ (typically 8.5.0 recommended)
- torch_npu: 2.1.0+ (match PyTorch minor version)
- Python: 3.8-3.10 (check torch_npu compatibility)
- Drivers: Ascend 910/310P driver versions

## Output Format

Provide analysis in this structure:

### Core Dependencies
- PyTorch version and torch_npu compatibility
- Key dependencies and their versions
- Critical version constraints

### CUDA-Dependent Dependencies
List packages requiring replacement:
| Package | Version | Issue | Suggested Alternative |
|---------|---------|-------|----------------------|
| flash-attn | 2.x | CUDA-specific | torch_npu.npu_fusion_attention |
| cupy | 12.x | CUDA-specific | numpy (or remove) |

### Version Constraints
- Specific version requirements for Ascend stack
- Pinning recommendations
- Dependency conflicts identified

### Environment Requirements
- CANN version requirements
- Driver/firmware requirements
- torch_npu version requirements
- Installation order considerations

### Recommended Replacements

**Common CUDA → NPU Replacements:**
```
flash-attn → torch_npu (built-in fusion attention)
torch.cuda.amp → torch.npu.amp
torch.distributed.nccl → torch.distributed.hccl
apex → torch_npu (AMP built-in)
```

## Tools to Use

**Documentation First:**
- Read official Ascend documentation before analysis:
  - https://www.hiascend.com/doc_center/source/zh/Pytorch/730/ptmoddevg/trainingmigrguide/PT_LMTMOG_0002.html
  - https://www.hiascend.com/doc_center/source/zh/canncommercial/850/API/aolapi/operatorlist_00001.html

**Dependency Analysis:**
- Use `Read` to examine dependency files

## Notes

- Not all CUDA dependencies need exact replacements
- Some packages work on CPU (performance impact)
- Prioritize critical dependencies first
- Consider transitive dependencies
- Suggest version pinning for reproducibility

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ferhodium) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
