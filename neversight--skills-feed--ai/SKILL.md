---
name: ai
description: > Use when this capability is needed.
metadata:
  author: neversight
---

## Critical Patterns

### Model Development (REQUIRED)

```python
# ✅ ALWAYS: Version your models and data
from datetime import datetime

model_config = {
    "version": "1.2.0",
    "trained_at": datetime.now().isoformat(),
    "dataset_hash": compute_hash(training_data),
    "hyperparameters": {...}
}
```

### Reproducibility (REQUIRED)

```python
# ✅ ALWAYS: Set seeds for reproducibility
import random
import numpy as np
import torch

def set_seed(seed: int = 42):
    random.seed(seed)
    np.random.seed(seed)
    torch.manual_seed(seed)
    if torch.cuda.is_available():
        torch.cuda.manual_seed_all(seed)
```

---

## Decision Tree

```
Need classification?       → Start with simple baseline
Need embeddings?           → Use pre-trained models
Need fine-tuning?          → Start with small learning rate
Need deployment?           → Consider ONNX export
Need monitoring?           → Track drift metrics
```

---

## Resources

- **ML Development**: [ml-development.md](ml-development.md)
- **Cognee Integration**: [cognee/](cognee/)

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/neversight) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-11 -->
