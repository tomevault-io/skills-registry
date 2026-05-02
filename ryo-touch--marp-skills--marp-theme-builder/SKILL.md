---
name: marp-theme-builder
description: Marpテーマのビルドパイプライン構築。SCSS→CSSコンパイル、サンプル画像生成、Makefile設定、Git hook、GitHub Actions CI設定を支援。「テーマのビルド環境を整備」などのリクエスト時に使用。 Use when this capability is needed.
metadata:
  author: ryo-touch
---

# Marp Theme Builder スキル

Marpテーマのビルドパイプラインを構築するスキル。

## 使用タイミング

- 「テーマのビルド環境を整備」
- 「CIを設定したい」
- 「Makefileを作成」
- 「pre-commitフックを設定」
- 「画像差分テストを導入」

## ワークフロー

1. **プロジェクト初期化**: ディレクトリ構造、package.json
2. **Makefile作成**: ビルドコマンドの定義
3. **Git hook設定**: pre-commitでビルド確認
4. **CI設定**: GitHub Actionsワークフロー
5. **動作確認**: ビルドとテスト実行

## 参照スキル

- **marp-theme-creator**: テーマ作成（ビルド対象）

## スクリプト

- [init_theme_project.sh](scripts/init_theme_project.sh) - プロジェクト初期化
- [build.sh](scripts/build.sh) - ビルド実行
- [image_diff.py](scripts/image_diff.py) - 画像差分検出

## リファレンス

- [makefile-patterns.md](references/makefile-patterns.md) - Makefile構成
- [ci-setup.md](references/ci-setup.md) - GitHub Actions設定
- [pre-commit-hook.md](references/pre-commit-hook.md) - Git hook設定

## アセット（テンプレート）

- [Makefile.template](assets/Makefile.template) - Makefile
- [test.yml.template](assets/test.yml.template) - GitHub Actionsワークフロー
- [pre-commit.template](assets/pre-commit.template) - pre-commitフック

## プロジェクト構造

```
my-marp-theme/
├── .github/
│   └── workflows/
│       └── test.yml        # CI設定
├── .githooks/
│   └── pre-commit          # pre-commitフック
├── marp-themes/
│   ├── theme.scss          # テーマソース
│   └── theme.css           # 生成されるCSS
├── example/
│   ├── example.md          # サンプルスライド
│   └── example.001.png     # 生成される画像
├── Makefile
├── package.json            # (オプション)
└── README.md
```

## ビルドフロー

```
SCSS ─────> CSS ─────> PNG画像
         (sass)    (marp-cli)
                      │
                      ▼
                   Git commit
                      │
                      ▼
                 pre-commit hook
                  (make実行)
                      │
                      ▼
                   CI (GitHub Actions)
                  (画像差分テスト)
```

$ARGUMENTS

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ryo-touch) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
