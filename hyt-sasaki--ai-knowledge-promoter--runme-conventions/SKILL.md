---
name: runme-conventions
description: | Use when this capability is needed.
metadata:
  author: hyt-sasaki
---

# Runme.dev規約

## クイックスタート

コードブロックにJSON形式でセル設定を追加:

````markdown
```bash {"name":"deploy","cwd":"../mcp-server"}
npm run deploy
```
````

## 主要オプション

| オプション | 説明 | 例 |
|-----------|------|-----|
| `name` | タスク名（`runme run <name>`で実行） | `{"name":"deploy"}` |
| `cwd` | 作業ディレクトリ（mdファイルからの相対パス） | `{"cwd":"../mcp-server"}` |
| `excludeFromRunAll` | `runme run --all`から除外 | `{"excludeFromRunAll":"true"}` |
| `ignore` | CLI・ノートブック変換から完全除外（runme listにも表示されない） | `{"ignore":"true"}` |

## フォーマット

コミット前に`runme fmt`を実行してVSCode拡張機能との差分を防止:

```bash
runme fmt -w --filename <target.md>
```

## 詳細リファレンス

- **全セルオプション**: [references/cell-options.md](references/cell-options.md)
- **ベストプラクティス**: [references/best-practices.md](references/best-practices.md)
- **公式ドキュメント**: https://docs.runme.dev/configuration/cell-level

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/hyt-sasaki) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
