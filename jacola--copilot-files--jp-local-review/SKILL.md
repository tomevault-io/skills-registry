---
name: jp-local-review
description: git worktreeを使用して同僚のブランチをレビューするためのローカル環境をセットアップします。現在の作業を中断せずに他の人のコードをレビューする必要がある場合に使用します。 Use when this capability is needed.
metadata:
  author: jacola
---

# ローカルレビュー

同僚のブランチのローカルレビュー環境をセットアップするタスクです。worktreeの作成と依存関係のセットアップを含みます。

## プロセス

`gh_username:branchName` のようなパラメータで呼び出された場合：

### 1. 入力を解析する
- `username:branchname` の形式からGitHubユーザー名とブランチ名を抽出する
- パラメータが提供されていない場合、`gh_username:branchName` の形式で入力を求める

### 2. 識別情報を抽出する
- ブランチ名からチケット番号を探す（例：`eng-1696`、`ISSUE-1696`）
- これを使用して短いworktreeディレクトリ名を作成する
- チケットが見つからない場合、ブランチ名をサニタイズしたバージョンを使用する

### 3. リモートとworktreeをセットアップする

```bash
# リモートが既に存在するか確認する
git remote -v

# 存在しない場合、追加する
git remote add USERNAME git@github.com:USERNAME/REPO_NAME

# リモートからフェッチする
git fetch USERNAME

# worktreeを作成する
git worktree add -b BRANCHNAME ~/wt/REPO_NAME/SHORT_NAME USERNAME/BRANCHNAME
```

### 4. worktreeを構成する

```bash
# ローカル設定が存在する場合コピーする
cp .copilot/settings.local.json WORKTREE/.copilot/ 2>/dev/null || true

# プロジェクトに適したセットアップコマンドを実行する
cd WORKTREE && npm install  # または make setup、pip install など
```

## エラーハンドリング

- worktreeが既に存在する場合、ユーザーに先に削除が必要であることを通知する：
  ```bash
  git worktree remove ~/wt/REPO_NAME/SHORT_NAME
  ```
- リモートフェッチが失敗した場合、ユーザー名/リポジトリが存在するか確認する
- セットアップが失敗した場合、エラーを表示するが続行する

## 使用例

```
/local_review samdickson22:sam/eng-1696-hotkey-for-yolo-mode
```

これにより：
- 'samdickson22' をリモートとして追加する
- `~/wt/repo-name/eng-1696` にworktreeを作成する
- 環境をセットアップする

## クリーンアップ

レビュー完了後、以下でクリーンアップする：
```bash
git worktree remove ~/wt/REPO_NAME/SHORT_NAME
git remote remove USERNAME  # オプション、再度必要ない場合
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/jacola) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
