---
name: golang-tester
description: 檢查專案內的整體 golang unit test 是否都通過 Use when this capability is needed.
metadata:
  author: z0890142
---

# golang-tester

## 規則
1. 透過 makefile 內的 testing 執行專案內的 golang unit test
```bash
make testing
```

2. 利用以下 bash 確認是否有失敗的測試案例
```bash
if grep -q '"Action":"fail"' cover/combined_test.json; then
    echo "Test failures detected, stopping CI..."
    exit 1
fi
```

3. 如果有錯誤案例請依序提供錯誤案例與錯誤原因

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/z0890142) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
