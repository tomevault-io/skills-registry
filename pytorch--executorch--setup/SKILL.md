---
name: setup
description: Set up ExecuTorch development environment. Use when installing dependencies, setting up conda environments, or preparing to develop with ExecuTorch. Use when this capability is needed.
metadata:
  author: pytorch
---

# Setup

1. Activate conda: `conda activate executorch`
   - If not found: `conda env list | grep -E "(executorch|et)"`

2. Install executorch: `./install_executorch.sh`

3. (Optional) For Huggingface integration:
   - Read commit from `.ci/docker/ci_commit_pins/optimum-executorch.txt`
   - Install: `pip install git+https://github.com/huggingface/optimum-executorch.git@<COMMIT>`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/pytorch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
