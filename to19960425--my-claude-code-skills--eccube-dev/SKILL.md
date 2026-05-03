---
name: eccube-dev
description: > Use when this capability is needed.
metadata:
  author: to19960425
---

# EC-CUBE 4 開発ガイド

公式ドキュメント: https://doc4.ec-cube.net/

## アーキテクチャ概要

EC-CUBE 4はSymfony 4/5ベースのPHPフレームワーク。カスタマイズは `app/Customize/` 配下で行い、コア (`src/Eccube/`) は編集しない。

```
app/Customize/          # カスタマイズコード（主な開発対象）
  ├── Controller/       # カスタムコントローラー（@Routeアノテーション）
  ├── Entity/           # Traitによるエンティティ拡張
  ├── Repository/       # カスタムリポジトリ
  ├── Service/          # ビジネスロジック
  ├── Command/          # CLIバッチコマンド
  ├── Form/Extension/   # フォーム拡張
  │   └── Admin/        # 管理画面用フォーム拡張
  └── EventListener/    # イベントリスナー
app/template/           # Twigテンプレートオーバーライド
  ├── default/          # フロント用
  └── admin/            # 管理画面用
app/config/eccube/      # Symfony/EC-CUBE設定
app/DoctrineMigrations/ # DBマイグレーション
app/proxy/entity/       # 自動生成プロキシ（手動編集不可）
app/Plugin/             # プラグイン
```

`Customize\` 名前空間はautowiringが有効。サービス登録は `app/config/eccube/services.yaml` で管理。

## カスタマイズ領域別リファレンス

タスクに応じて以下のリファレンスを参照する。

### エンティティ拡張（Trait / 新規テーブル）

既存エンティティへのカラム追加、リレーション定義、新規テーブル作成。
-> [references/entity-customization.md](references/entity-customization.md)

### フォーム拡張

管理画面・フロント画面へのフォームフィールド追加、バリデーション。
-> [references/form-extension.md](references/form-extension.md)

### ページ追加

新規ページの作成（コントローラー、テンプレート、dtb_page/dtb_page_layout登録）。
-> [references/page-addition.md](references/page-addition.md)

### マイグレーション

スキーマ変更、データ移行、デプロイ時のDB操作。
-> [references/migration.md](references/migration.md)

### プラグイン開発

プラグインの新規作成、PluginManager、管理画面ナビ、イベント、PurchaseFlow拡張。
-> [references/plugin-development.md](references/plugin-development.md)

## 共通コマンド

```bash
# プロキシ再生成（Trait変更後に必須）
bin/console eccube:generate:proxies

# キャッシュクリア
bin/console cache:clear --no-warmup

# スキーマ差分確認
bin/console doctrine:schema:update --dump-sql

# マイグレーション実行
bin/console doctrine:migrations:migrate

# ルート一覧
bin/console debug:router
```

## デプロイ順序（Trait変更時）

**順序厳守**: 間違えるとDoctrineメタデータ不整合でエラーになる。

1. `bin/console eccube:generate:proxies` — プロキシ再生成
2. `bin/console cache:clear --no-warmup` — キャッシュクリア
3. スキーマ反映（`doctrine:schema:update --force` / `doctrine:migrations:migrate` / 手動SQL）

## 命名規則

| 対象 | 規則 | 例 |
|-----|------|-----|
| データテーブル | `dtb_` プレフィックス | `dtb_customer`, `dtb_order` |
| マスタテーブル | `mtb_` プレフィックス | `mtb_sex`, `mtb_pref` |
| Trait | `{Target}Trait` | `CustomerTrait`, `OrderTrait` |
| FormExtension | `{Target}TypeExtension` | `CustomerTypeExtension` |
| マイグレーション | `Version{YYYYMMDDHHmmss}` | `Version20260206120000` |
| プラグインテーブル | `plg_` プレフィックス | `plg_chatwork_api_config` |
| プラグインコード | PascalCase | `FaqManager`, `SalesReport42` |

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/to19960425) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
