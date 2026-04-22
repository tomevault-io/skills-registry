---
name: publish
description: Publish Dart/Flutter packages to pub.dev using Melos. Use when user wants to publish packages, run publish dry-run, or check package validation. Triggers on "publish package", "pub publish", "publish dry run", "melos publish". Use when this capability is needed.
metadata:
  author: aoeiuv020
---

# 发布 Dart/Flutter 包

使用 Melos 发布工作区中的 Dart/Flutter 包到 pub.dev。

## 前置条件

需要先安装 Melos（参考 `init-melos` skill）并执行 `melos bootstrap`。

## 验证发布（dry run）

```bash
melos publish -y --dry-run
```

## 正式发布

```bash
melos publish -y --no-dry-run
```

## 注意事项

- 正式发布需要 pub.dev 认证（CI 中通过 OIDC JWT 实现）
- dry run 会验证包但不会上传

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/aoeiuv020) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
