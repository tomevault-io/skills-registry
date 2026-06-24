---
name: maishou
description: uv run {baseDir}/scripts/main.py search --source={source} --keyword='{keyword}' Use when this capability is needed.
metadata:
  author: kbarbel640-del
---

# Skill
通过执行Shell命令获取。

```yaml
# 参数解释
source:
  1: 淘宝/天猫
  2: 京东
  3: 拼多多
  7: 抖音
  8: 快手
```

## 搜索商品
```shell
uv run {baseDir}/scripts/main.py search --source={source} --keyword='{keyword}'
uv run {baseDir}/scripts/main.py search --source={source} --keyword='{keyword}' --page=2
```

## 商品详情及购买链接
```shell
uv run {baseDir}/scripts/main.py detail --source={source} --id={goodsId}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/kbarbel640-del) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
