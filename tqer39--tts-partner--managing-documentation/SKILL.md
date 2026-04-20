---
name: managing-documentation
description: バイリンガルドキュメント（英語/日本語）を作成・管理します。READMEや技術ドキュメントの多言語対応を求められた場合に使用してください。 Use when this capability is needed.
metadata:
  author: tqer39
---

# バイリンガルドキュメント管理

## ドキュメント構造

```text
tts-partner/
├── README.md              # 英語（デフォルト）
├── CLAUDE.md              # 英語（デフォルト）
├── docs/
│   ├── README.ja.md       # 日本語版 README
│   ├── CLAUDE.ja.md       # 日本語版 CLAUDE.md
│   ├── setup.md           # 英語
│   ├── setup.ja.md        # 日本語
│   └── ...
```

## 言語リンクの形式

各ドキュメントの先頭に相互リンクを設置:

**英語版（ルート）**:

```markdown
# Title

[🇯🇵 日本語](docs/FILENAME.ja.md)
```

**日本語版（docs/）**:

```markdown
# タイトル

[🇺🇸 English](../FILENAME.md)
```

## 作成ルール

1. **デフォルト言語**: 英語（ルートに配置）
2. **日本語版**: `docs/` 配下に `.ja.md` 拡張子で作成
3. **相互リンク**: 国旗 + 言語名で先頭に配置
4. **内容の同期**: 両言語で同じ構造・情報を維持

## 手順

### 新規ドキュメント作成時

1. 英語版をルートまたは `docs/` に作成
2. 対応する日本語版を `docs/*.ja.md` に作成
3. 両ファイルに相互リンクを追加

### 既存ドキュメント更新時

1. 片方を更新したら、もう一方も更新
2. 構造の変更は両方に反映

## 対象ファイル

| 英語             | 日本語              |
|------------------|---------------------|
| `README.md`      | `docs/README.ja.md` |
| `CLAUDE.md`      | `docs/CLAUDE.ja.md` |
| `docs/setup.md`  | `docs/setup.ja.md`  |
| `docs/api.md`    | `docs/api.ja.md`    |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tqer39) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
