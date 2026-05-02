---
name: prime-intellect
description: Prime Intellect GPU cloud API reference. Use when working with Prime Intellect pods, GPUs, clusters, disks, SSH keys, inference, or any PI compute resources. Use when this capability is needed.
metadata:
  author: 13point5
---

# Prime Intellect API Reference

**Base URL:** `https://api.primeintellect.ai`
**Inference API:** `https://api.pinference.ai/api/v1`

All requests require `Authorization: Bearer <api_key>` header.
For team accounts, include `X-Prime-Team-ID: <team_id>` header.

## API Groups

- **[Availability](availability.md)** - Check GPU, cluster, and disk availability and pricing
- **[Pods](pods.md)** - Create, manage, and monitor GPU instances
- **[Disks](disks.md)** - Network-attached storage management
- **[SSH Keys](ssh-keys.md)** - Manage SSH keys for pod access
- **[Inference](inference.md)** - Models and chat completions API
- **[Sandboxes](sandboxes.md)** - Lightweight container environments
- **[Images](images.md)** - Custom image management
- **[Evals](evals.md)** - Evaluation management
- **[User](user.md)** - User info and teams

## Quick Reference

### Available GPU Types

`CPU_NODE, A10_24GB, A100_80GB, A100_40GB, A30_24GB, A40_48GB, B200_180GB, B300_262GB, RTX3090_24GB, RTX4090_24GB, RTX5090_32GB, H100_80GB, H200_96GB, GH200_96GB, L4_24GB, L40_48GB, L40S_48GB, A4000_16GB, A5000_24GB, A6000_48GB, V100_16GB, V100_32GB, T4_16GB`

### Available Regions

`africa, asia_south, asia_northeast, australia, canada, eu_east, eu_north, eu_west, middle_east, south_america, united_states`

### Available Images

`ubuntu_22_cuda_12, cuda_12_1_pytorch_2_2, cuda_11_8_pytorch_2_1, cuda_12_1_pytorch_2_3, cuda_12_1_pytorch_2_4, cuda_12_4_pytorch_2_4, cuda_12_4_pytorch_2_5, cuda_12_4_pytorch_2_6, cuda_12_6_pytorch_2_7, stable_diffusion, axolotl, bittensor, vllm_llama_8b, vllm_llama_70b, vllm_llama_405b, custom_template, flux, prime_rl`

### Provider Types

`runpod, fluidstack, lambdalabs, hyperstack, oblivus, cudocompute, scaleway, tensordock, datacrunch, latitude, crusoecloud, massedcompute, akash, primeintellect, primecompute, nebius, vultr, denvr`

### Socket Types

`PCIe, SXM2, SXM3, SXM4, SXM5, SXM6`

### Security Types

`secure_cloud, community_cloud`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/13point5) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
