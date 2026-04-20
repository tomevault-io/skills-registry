---
name: rust-check
description: 检查 Rust 代码是否能通过编译。在修改 .rs 或 Cargo.toml 后、提交前、或用户要求校验编译时使用。通过运行 cargo check 或 cargo build 验证并解读错误。当用户提到编译、构建、cargo check、cargo build、编译错误、能否编译时自动触发。 Use when this capability is needed.
metadata:
  author: liuhuo23
---

# Rust 编译检查

检查 Rust 项目是否能成功编译，并协助解读与修复编译错误。

## 使用时机

- 修改了 `.rs`、`Cargo.toml` 或 `Cargo.lock` 后
- 用户明确要求「检查编译」「能否编译」「有没有编译错误」
- 准备提交或合并代码前需要确认构建通过
- 出现 `cargo check`、`cargo build`、`compile`、`compilation`、`构建`、`编译` 等关键词时

## 执行编译检查

在项目根目录（含 `Cargo.toml` 的目录）运行：

```bash
cargo check
```

需要完整构建（含生成二进制）时用：

```bash
cargo build
```

如需在特定 target 下检查（如 release）：

```bash
cargo check --release
```

## 解读输出

### 编译成功

输出包含 `Finished`，无 `error[Exxxx]`，可简要告知用户「编译通过」。

### 编译失败

查找 `error[Exxxx]` 行，记录：
- 错误码（如 `E0308`、`E0382`）
- 所在文件与行号
- 编译器给出的简短说明与建议

## 报告与修复

向用户报告时，按优先级列出：

1. **error**：必须修复才能通过编译
2. **warning**：建议修复，`cargo check` 默认会显示；若用户只想看 error，可用 `cargo check 2>&1 | grep -E '^error'` 过滤（或说明「当前有 N 个 warning」）

对每个 error，给出：
- 文件:行号 + 错误码 + 一句话原因
- 可选：基于编译器提示的修复思路或直接给出修改建议

## 修复后复验

修改代码后必须再次运行 `cargo check` 或 `cargo build`，直到无 error 再结束。

## 约定

- 默认在项目根目录执行，不随意 `cd` 到子 crate 除非用户指定
- 不修改 `Cargo.lock` 除非用户要求升级依赖或解决 lock 冲突
- 遇到含糊的泛型、trait、生命周期错误时，可引用编译器给出的 `help:` 或 `note:` 来辅助说明

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/liuhuo23) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
