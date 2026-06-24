---
name: uv-best-practices
description: uvパッケージマネージャーの最適活用とワークフロー管理スキル Use when this capability is needed.
metadata:
  author: koboriakira
---

# uv Best Practices

uvパッケージマネージャーを最大限活用するためのベストプラクティスとワークフロー。

## 基本ワークフロー

### プロジェクト初期化
```bash
# 新規プロジェクト作成
uv init my-project
cd my-project

# 既存プロジェクトでのuv導入
uv init --no-readme
uv add --group dev ruff mypy pytest
```

### 依存関係管理の最適化

#### 適切なグループ分類
```bash
# 本番依存関係
uv add pydantic httpx rich typer

# 開発依存関係
uv add --group dev ruff mypy pytest pytest-cov

# テスト専用依存関係
uv add --group test pytest-asyncio pytest-mock factory-boy

# ドキュメント依存関係
uv add --group docs mkdocs mkdocs-material
```

#### pyproject.tomlでの管理
```toml
[dependency-groups]
dev = [
    "ruff>=0.1.0",
    "mypy>=1.7.0",
    "pytest>=7.4.0",
    "pytest-cov>=4.1.0",
]
test = [
    "pytest-asyncio>=0.21.0",
    "pytest-mock>=3.12.0",
    "httpx>=0.25.0",  # テスト用HTTPクライアント
]
docs = [
    "mkdocs>=1.5.0",
    "mkdocs-material>=9.4.0",
]
```

## パフォーマンス最適化

### キャッシュ活用
```bash
# キャッシュ状態確認
uv cache info

# キャッシュクリア（必要時）
uv cache clean

# 特定パッケージのキャッシュクリア
uv cache clean requests
```

### 並列インストール
```bash
# 高速インストール（デフォルトで並列）
uv sync

# 全てのグループを同時インストール
uv sync --all-groups

# 特定グループのみ
uv sync --group dev --group test
```

## 仮想環境管理

### 自動仮想環境
```bash
# uvは自動で仮想環境を管理
# 明示的な有効化不要

# 仮想環境情報確認
uv venv --python-preference only-managed

# Python バージョン指定
uv venv --python 3.12
uv sync
```

### 複数環境管理
```bash
# 開発環境
uv sync --group dev

# 本番環境シミュレーション
uv sync --no-dev

# CI/CD環境
uv sync --frozen  # uv.lockの厳密な再現
```

## バージョン管理戦略

### セマンティックバージョニング
```toml
# pyproject.toml
[project]
dependencies = [
    "pydantic>=2.5.0,<3.0.0",     # メジャー固定、マイナー許可
    "httpx>=0.25.0,<0.26.0",      # マイナー固定、パッチ許可
    "rich~=13.7.0",               # パッチのみ許可
]
```

### ロックファイル管理
```bash
# ロックファイル生成・更新
uv lock

# 依存関係アップグレード
uv lock --upgrade

# 特定パッケージのみアップグレード
uv lock --upgrade-package pydantic

# ロックファイル検証
uv lock --check
```

## CI/CDでの活用

### GitHub Actions最適化
```yaml
- name: Install uv
  uses: astral-sh/setup-uv@v3
  with:
    enable-cache: true
    cache-dependency-glob: "uv.lock"

- name: Install dependencies
  run: uv sync --frozen

- name: Run tests
  run: uv run pytest
```

### Dockerfile最適化
```dockerfile
FROM python:3.12-slim

# uvインストール
COPY --from=ghcr.io/astral-sh/uv:latest /uv /bin/uv

# プロジェクトファイル
COPY pyproject.toml uv.lock ./

# 依存関係インストール
RUN uv sync --frozen --no-dev

# アプリケーション
COPY src ./src
CMD ["uv", "run", "python", "-m", "myapp"]
```

## トラブルシューティング

### よくある問題と解決法

#### 1. 依存関係競合
```bash
# 詳細な解決情報表示
uv add package-name --verbose

# 依存関係ツリー確認
uv tree

# 競合解決
uv lock --resolution lowest-direct  # 最低限のバージョン使用
```

#### 2. キャッシュ問題
```bash
# キャッシュ破損時
uv cache clean
uv sync --reinstall

# ネットワーク問題時
uv sync --offline  # キャッシュのみ使用
```

#### 3. Python バージョン問題
```bash
# 利用可能なPython確認
uv python list

# 特定バージョンインストール
uv python install 3.12

# プロジェクトのPython固定
uv python pin 3.12
```

## 開発効率化Tips

### スクリプト実行
```bash
# setup.pyコマンド不要
uv run python -m myapp

# 開発サーバー起動
uv run python -m myapp.server

# テスト実行
uv run pytest
uv run pytest --cov

# リンティング
uv run ruff check .
uv run mypy
```

### 依存関係分析
```bash
# 依存関係ツリー表示
uv tree

# セキュリティ監査
uv run safety check

# 古いパッケージ確認
uv lock --upgrade --dry-run
```

### 開発環境構築自動化
```bash
# 開発環境セットアップスクリプト
#!/bin/bash
set -e

echo "🚀 開発環境セットアップ開始..."

# uv環境構築
uv sync --all-groups

# pre-commit設定
uv run pre-commit install

# 初回テスト実行
uv run pytest

echo "✅ 開発環境セットアップ完了!"
```

このスキルにより、uvの強力な機能を最大限活用した効率的な開発環境を構築できます。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/koboriakira) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
