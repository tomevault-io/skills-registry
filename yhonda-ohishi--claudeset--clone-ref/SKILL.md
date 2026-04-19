---
name: clone-ref
description: 参照用リポジトリをクローンして .gitignore に追加します Use when this capability is needed.
metadata:
  author: yhonda-ohishi
---

$ARGUMENTS のリポジトリを参照用にクローンします：

1. リポジトリURLからリポジトリ名を抽出
2. `<repo-name>_ref` フォルダにクローン
3. `.gitignore` に `<repo-name>_ref/` を追加

例：
- `https://github.com/user/repo` → `repo_ref/` にクローンして `.gitignore` に追加

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yhonda-ohishi) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
