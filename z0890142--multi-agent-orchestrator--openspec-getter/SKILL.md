---
name: openspec-getter
description: 該 skill 用於取得尚未完成的 openspec proposal 清單 Use when this capability is needed.
metadata:
  author: z0890142
---

# openspec-getter

## 規則

1. 請使用以下 shell 抓取 openspec proposal 名稱
```
openspec list | awk '
  $0 ~ /^[[:space:]]+[a-z0-9-]+[[:space:]]+[0-9]+\/[0-9]+[[:space:]]+tasks/ {
    print $1
  }
'
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z0890142) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
