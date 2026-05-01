---
name: ollama-model-tuner
description: Optimize Ollama models/prompts using local datasets, eval metrics, and iterative tuning. No cloud needed. Use when this capability is needed.
metadata:
  author: openclaw
---

# Ollama Model Tuner v1.0.0

## 🎯 Purpose
- Prompt engineering & A/B testing
- Modelfile customization
- LoRA fine-tuning with local data
- Performance benchmarking

## 🚀 Quick Start
```
!ollama-model-tuner --model llama3 --dataset ./data.json --task classification
```

## Files
- `scripts/tune.py`: Python tuner with eval loop
- `prompts/system.md`: Base system prompts

## Supported
Ollama 0.3+, Python 3.10+, datasets in JSONL/CSV.

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/openclaw) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
