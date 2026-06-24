---
name: plotting
description: Creates publication-quality scientific plots. Use when making plots, figures, or visualizations. Applies tueplots TMLR styling, LaTeX rendering, no titles.
metadata:
  author: yallup
---

# Scientific Plotting

```python
from tueplots import bundles
import matplotlib.pyplot as plt

plt.rcParams.update(bundles.tmlr2023())
plt.rcParams.update({"text.usetex": True})
```

No titles. Save as PDF with `bbox_inches="tight"`.

Use latex to render formula
```python
plt.plot(x,y,label=r"$\sigma$)
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yallup) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
