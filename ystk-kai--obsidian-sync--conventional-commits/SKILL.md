---
name: conventional-commits
description: Conventional Commits 1.0.0 に準拠した日本語コミットメッセージを作成します。タイプ（feat/fix/docs/style/refactor/perf/test/build/ci/chore）の選択、スコープの設定、破壊的変更の記載を行います。git commit を行う際、コミットメッセージの作成を依頼されたとき、変更内容を要約してコミットしたいときに使用してください。 Use when this capability is needed.
metadata:
  author: ystk-kai
---

# Conventional Commits for Obsidian LiveSync

## Overview

このスキルは、Conventional Commits 1.0.0 仕様に準拠したコミットメッセージの作成を支援します。このプロジェクトでは**日本語での説明文**を標準としています。

## Instructions

### 1. コミットメッセージの形式

```
<type>[optional scope]: <description>

[optional body]

[optional footer(s)]
```

### 2. タイプ一覧と使い分け

| タイプ | 用途 | 例 |
|--------|------|-----|
| `feat` | 新機能の追加 | `feat: Prometheus メトリクスエンドポイントを追加` |
| `fix` | バグ修正 | `fix: CouchDB 接続タイムアウトのエラーハンドリングを修正` |
| `docs` | ドキュメント更新 | `docs: README にセットアップ手順を追加` |
| `style` | コードフォーマット変更（機能に影響なし） | `style: cargo fmt による整形` |
| `refactor` | リファクタリング（機能追加・バグ修正でない） | `refactor: CouchDbClient のエラー処理を共通化` |
| `perf` | パフォーマンス改善 | `perf: HTTP プロキシのバッファサイズを最適化` |
| `test` | テスト追加・修正 | `test: LiveSyncService のユニットテストを追加` |
| `build` | ビルドシステム・依存関係の変更 | `build: axum を 0.8.4 にアップデート` |
| `ci` | CI 設定の変更 | `ci: GitHub Actions にキャッシュを追加` |
| `chore` | その他の作業 | `chore: .gitignore を更新` |

### 3. スコープの指定

スコープはオプションですが、変更箇所を明確にするために推奨されます。

**推奨スコープ**:
- `domain` - ドメイン層
- `application` - アプリケーション層
- `infrastructure` - インフラ層
- `web` - Web インターフェース
- `couchdb` - CouchDB クライアント
- `config` - 設定関連
- `docker` - Docker 関連
- `backup` - バックアップ機能

**例**:
```
feat(web): ヘルスチェックにバックオフ戦略を実装

fix(couchdb): longpoll リクエストのタイムアウト処理を改善
```

### 4. 日本語での説明文

このプロジェクトでは説明文を**日本語**で記載します。

**良い例**:
```
feat: HTTP リクエストのメトリクス収集を追加

リクエスト数、レスポンス時間、エラー率を Prometheus 形式で
エクスポートする機能を実装した。
```

**避けるべき例**:
```
feat: Add HTTP request metrics collection  ← 英語は使用しない
```

### 5. 破壊的変更の記載

**方法1**: フッターに `BREAKING CHANGE:` を記載

```
feat(couchdb)!: CouchDbRepository トレイトを変更

BREAKING CHANGE: forward_request メソッドのシグネチャを変更。
クエリパラメータを Option<String> から Option<HashMap<String, String>> に変更。
```

**方法2**: タイプの後に `!` を付与

```
refactor(domain)!: DomainError のバリアントを再構成
```

## Examples

### 新機能追加

```
feat(web): ヘルスチェックエンドポイントを追加

/health エンドポイントで以下の情報を提供:
- サーバー稼働時間
- CouchDB 接続状態
- アプリケーションバージョン
```

### バグ修正

```
fix(infrastructure): CouchDB 接続失敗時のパニックを修正

接続失敗時に unwrap() でパニックしていた問題を修正。
エラーをログに記録し、degraded 状態として継続するように変更。

Fixes #123
```

### リファクタリング

```
refactor(application): LiveSyncService のエラーハンドリングを改善

- anyhow から thiserror ベースの DomainError に移行
- エラーメッセージをより詳細に
```

### 破壊的変更

```
feat(domain)!: CouchDbDocument の構造を変更

BREAKING CHANGE: data フィールドを serde_json::Value から
構造化された ContentData 型に変更。既存のドキュメント操作
コードの更新が必要。
```

### 複数の変更をまとめる場合

```
refactor: HTTP プロキシの構造を整理

- CouchDbClient から http_forward_request を分離
- エラーハンドリングを共通化
- longpoll リクエストの検出ロジックを改善
```

## Reference

### Conventional Commits 仕様
- https://www.conventionalcommits.org/ja/v1.0.0/

### このプロジェクトのルール
- 説明文は日本語で記載
- スコープは推奨（必須ではない）
- 破壊的変更は必ず明記
- 関連 Issue がある場合は `Fixes #123` でリンク

### コミット前チェック
```bash
cargo fmt --all -- --check  # フォーマット確認
cargo clippy -- -D warnings # リンター
cargo test --verbose        # テスト実行
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ystk-kai) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
