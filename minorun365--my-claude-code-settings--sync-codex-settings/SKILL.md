---
name: sync-codex-settings
description: Codexの共通設定（AGENTS.md、config.toml、agents、skills）をGitHubリポジトリと双方向同期する Use when this capability is needed.
metadata:
  author: minorun365
---

# Codex設定同期

Codex の **共通設定のみ** を GitHub リポジトリと双方向同期します。
`/sync-dotfiles` から呼び出されることもあります。

## 同期対象（共通設定）

| ローカル | リポジトリ | 備考 |
|----------|------------|------|
| `~/.codex/AGENTS.md` | `codex/AGENTS.md` | グローバル作業ガイド |
| `~/.codex/config.toml` | `codex/config.toml` | model / plugins / MCP Server 設定 |
| `~/.codex/agents/` | `codex/agents/` | custom agents |
| `~/.codex/skills/` | `codex/skills/` | `.system` は除外 |

## 同期対象外（PC固有設定）

以下は PC 固有またはキャッシュのため **同期しない**：

- `~/.codex/auth.json`
- `~/.codex/plugins/`
- `~/.codex/vendor_imports/`
- `~/.codex/memories/`
- `~/.codex/sessions/`
- `~/.codex/shell_snapshots/`
- `~/.codex/*.sqlite*`
- `~/.codex/.codex-global-state.json`
- `~/.codex/models_cache.json`
- `~/.codex/tmp/`
- `~/.codex/cache/`
- `~/.codex/.tmp/`
- `~/.codex/skills/.system/` - Codex 同梱スキル

## リポジトリパス

```bash
~/git/minorun365/dotfiles/
```

## 実行手順

### Push（ローカル → リポジトリ）

1. **差分確認**
   ```bash
   diff ~/.codex/AGENTS.md ~/git/minorun365/dotfiles/codex/AGENTS.md
   diff ~/.codex/config.toml ~/git/minorun365/dotfiles/codex/config.toml
   diff -rq ~/.codex/agents/ ~/git/minorun365/dotfiles/codex/agents/
   diff -rq ~/.codex/skills/ ~/git/minorun365/dotfiles/codex/skills/
   ```
   - `.system` 配下の差分は無視してよい

2. **同期実行**
   ```bash
   cd ~/git/minorun365/dotfiles
   ./scripts/sync-codex-settings.sh push
   ```

3. **差分確認**
   ```bash
   cd ~/git/minorun365/dotfiles
   git status
   git diff
   ```

4. **コミット・プッシュ**（ユーザー確認後）
   ```bash
   cd ~/git/minorun365/dotfiles
   git add -A
   git commit -m "Codex設定同期"
   git push
   ```

### Pull（リポジトリ → ローカル）

1. **リポジトリを最新化**
   ```bash
   cd ~/git/minorun365/dotfiles
   git pull
   ```

2. **同期実行**
   ```bash
   cd ~/git/minorun365/dotfiles
   ./scripts/sync-codex-settings.sh pull
   ```

3. **反映確認**
   ```bash
   diff ~/.codex/AGENTS.md ~/git/minorun365/dotfiles/codex/AGENTS.md
   diff ~/.codex/config.toml ~/git/minorun365/dotfiles/codex/config.toml
   ```

4. **必要なら Codex を再起動**
   - `config.toml` や MCP Server を変更した場合は Codex 再起動を案内する

## Codex 設定で注意する点

- MCP Server は `~/.codex/config.toml` の `[mcp_servers.<name>]` で管理する
- 機密値は repo に埋め込まず、環境変数から渡す
- 現在の想定環境変数:
  - `GITHUB_PERSONAL_ACCESS_TOKEN`
  - `GOOGLE_OAUTH_CLIENT_SECRET`
- `google-workspace` の `command` は端末ごとのホームパス差分に注意する
- `.system` スキルは Codex 同梱なので repo にコピーしない

## 自動実行ルール

このスキルでは以下のルールで判断する：

- **自動で進めてよい場合**:
  - Push/Pull 方向が明確
  - 差分が Codex 設定の同期として自然
- **ユーザーに確認する場合**:
  - Push/Pull どちらか判断できない
  - ローカルとリポジトリの両方に別の変更がありそう
  - `config.toml` に意図不明な削除がある
  - 端末依存パスの変更が必要

## 注意事項

- 新しい PC で Pull する前に既存の `~/.codex/` をバックアップ推奨
- `scripts/sync-codex-settings.sh pull` は `AGENTS.md` と `config.toml` をバックアップしてから上書きする
- スキル実行後に Codex の MCP が反映されない場合は再起動で解決することが多い

---
> Source: [minorun365/my-claude-code-settings](https://github.com/minorun365/my-claude-code-settings) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-05-23 -->
