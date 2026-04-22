---
name: doc-code-sync
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# doc-code-sync: ドキュメント・コード整合性チェックスキル

プロジェクトのドキュメントとコードの不整合を6カテゴリで検出し、レポートを生成する。

## 検出カテゴリ

| カテゴリ | 説明 | 重要度デフォルト |
|---------|------|----------------|
| `BROKEN_REF` | ドキュメントが参照するコード（ファイルパス、関数名等）が存在しない | CRITICAL |
| `UNDOCUMENTED` | 公開コード（export された関数/クラス/型）にドキュメントがない | WARNING |
| `STALE_EXAMPLE` | ドキュメント内のコード例のシグネチャが実際のコードと不一致 | CRITICAL |
| `CONFIG_DRIFT` | 設定値・環境変数のドキュメント記載と実際の不一致 | WARNING |
| `API_DRIFT` | API エンドポイントのドキュメント記載と実際の不一致 | CRITICAL |
| `VERSION_DRIFT` | バージョン番号・依存関係のドキュメント記載と実際の不一致 | INFO |

## ワークフロー

### Step 1: プロジェクト構造把握

1. プロジェクトの言語・フレームワークを判定する:
   - `package.json`, `go.mod`, `Cargo.toml`, `pyproject.toml`, `requirements.txt` 等の存在をチェック。
   - Solidity プロジェクト検出: `hardhat.config.*`, `foundry.toml`, `truffle-config.js` の存在をチェック。
2. ドキュメントファイルを Glob でスキャンする:
   - `docs/**/*.md`, `README.md`, `**/*.md`（`.git/`, `node_modules/`, `.docstore/extracted/` は除外）
3. コードファイルを Glob でスキャンする:
   - 検出した言語に応じた拡張子でスキャン。
4. `.claude/skills/doc-code-sync/references/check-patterns.md` を読み込み、言語別の Grep パターンを取得する。
5. `.claude/skills/doc-code-sync/references/extraction-strategy.md` を読み込み、抽出戦略の仕様を確認する。
6. 言語ごとの AST ランタイム検出を実行する:
   - **TypeScript/JS**: `node -e "require('typescript')"` を対象プロジェクトの `node_modules` で実行。
   - **Solidity**: `node -e "require('@solidity-parser/parser')"` または `solc --version` を実行。
   - **Python**: `python3 -c "import ast"` を実行。
   - **Go**: `go version` を実行。
   - **Rust**: `cargo --version` を実行。
   - 各言語の利用可能な戦略（`ast` / `grep`）を記録する。
   - `--strategy=grep` / `--strategy=ast` 引数でユーザーオーバーライド可能。

### Step 2: コードシグネチャ抽出

言語に応じた方法で公開シンボルを抽出する。Step 1 で決定した戦略に基づき、AST 戦略または Grep 戦略を使用する。

#### AST 戦略（高精度）

AST ランタイムが利用可能な言語では、エクストラクタスクリプトを Bash 経由で実行する:

```bash
# TypeScript
node .claude/skills/doc-code-sync/references/extractors/typescript-ast.js <project_root> "src/**/*.ts,src/**/*.tsx"

# Solidity
node .claude/skills/doc-code-sync/references/extractors/solidity-ast.js <project_root> "contracts/**/*.sol"
```

エクストラクタは標準化 JSON を stdout に出力する（スキーマは `extraction-strategy.md` 参照）。
タイムアウト: **60秒**。失敗時（exit code ≠ 0）は stderr をログに記録し、Grep 戦略にフォールバックする。

#### Grep 戦略（フォールバック）

AST ランタイムが不在、または AST 戦略が失敗した場合、Grep ツールで `check-patterns.md` に定義されたパターンを使用する。

#### 共通抽出対象

1. **関数/メソッド**: export された関数、public メソッドの名前とシグネチャ。
2. **クラス/型**: export されたクラス、インターフェース、型定義。
3. **設定キー**: `.env.example`, 設定ファイルのキー名。
4. **CLI コマンド**: CLI ツールの場合、コマンド名とオプション。
5. **API エンドポイント**: ルート定義（Express, FastAPI, Gin 等）。
6. **Solidity 固有**: コントラクト、イベント、修飾子、カスタムエラー、NatSpec コメント。

#### フォールバックチェーン

```
AST 戦略 → (失敗/タイムアウト) → Grep 戦略 → (パターンなし) → 汎用パターン
```

### Step 3: ドキュメント参照抽出

ドキュメントファイルから以下を抽出する:

1. **コードブロック**: ` ```言語 ` で囲まれたコード例の中の関数呼び出し・型参照。
2. **インラインコード**: `` `code` `` 形式のコード参照。
3. **ファイルパス参照**: ドキュメント内で言及されているファイルパス（`src/`, `./` 等で始まるもの）。
4. **API 参照**: `GET /api/...`, `POST /api/...` 等のパターン。
5. **環境変数参照**: `$ENV_VAR`, `process.env.VAR`, `os.getenv("VAR")` 等。

### Step 4: 整合性チェック（6カテゴリ）

#### BROKEN_REF: 参照切れ
- ドキュメント内のファイルパス参照が実際に存在するかチェック。
- インラインコードの関数名・クラス名がコードベースに存在するかチェック。

#### UNDOCUMENTED: 未ドキュメント化
- Step 2 で抽出した公開シンボルが、いずれかのドキュメントで言及されているかチェック。
- README.md またはメインドキュメントに記載がないものを検出。

#### STALE_EXAMPLE: コード例の陳腐化
- ドキュメント内のコード例に含まれる関数呼び出しのシグネチャ（引数の数・型）が、実際のコードと一致するかチェック。

#### CONFIG_DRIFT: 設定値の不一致
- `.env.example` のキーとドキュメント記載の環境変数を比較。
- 設定ファイル（`config.*`）のキーとドキュメント記載を比較。

#### API_DRIFT: API エンドポイントの不一致
- コード内のルート定義とドキュメント記載の API エンドポイントを比較。
- メソッド（GET/POST等）とパスの両方をチェック。

#### NATSPEC_DRIFT: NatSpec コメントの不一致（Solidity 固有）
- NatSpec の `@param` 名がコード上の引数名と一致するかチェック。
- NatSpec の `@return` が戻り値型と整合するかチェック。
- AST 戦略で抽出された `natspec` フィールドと `params` / `returnType` を比較する。

#### MISSING_NATSPEC: NatSpec 不足（Solidity 固有）
- `public` / `external` 関数に NatSpec（`@notice` または `@dev`）が存在するかチェック。
- 存在しない場合は `UNDOCUMENTED` カテゴリとして報告する。

#### VERSION_DRIFT: バージョンの不一致
- `package.json` 等のバージョン番号とドキュメント記載を比較。
- 依存関係のバージョンとドキュメント記載を比較。

### Step 5: 重要度判定

各検出項目に重要度を割り当てる:

- **CRITICAL**: 即座に対応が必要。ユーザーが誤った情報に基づいて行動する可能性がある。
  - BROKEN_REF（存在しないファイルへの参照）
  - STALE_EXAMPLE（動作しないコード例）
  - API_DRIFT（実在しない API エンドポイント）
- **WARNING**: 対応推奨。不完全だが致命的ではない。
  - UNDOCUMENTED（公開 API のドキュメント不足）
  - CONFIG_DRIFT（環境変数の記載漏れ）
- **INFO**: 参考情報。改善の余地がある。
  - VERSION_DRIFT（バージョン番号のずれ）

### Step 6: レポート生成

`.docstore/sync-report.md` に詳細レポートを出力する:

```markdown
# Doc-Code Sync Report

**生成日**: <YYYY-MM-DD>
**プロジェクト**: <project_name>
**スキャン対象**: コードファイル <n>件, ドキュメントファイル <n>件
**抽出戦略**: TypeScript=ast, Solidity=grep, ... （言語ごとに使用した戦略を表示）

## サマリー

| 重要度 | 件数 |
|--------|------|
| CRITICAL | <n> |
| WARNING | <n> |
| INFO | <n> |

## CRITICAL

### BROKEN_REF
- [ ] `docs/guide.md:15` → `src/old-module.ts` は存在しません
...

### STALE_EXAMPLE
- [ ] `docs/api.md:42` → `createUser(name)` は `createUser(name, options)` に変更されています
...

## WARNING

### UNDOCUMENTED
- [ ] `src/utils/auth.ts:export function validateToken()` はドキュメントに記載がありません
...

## INFO

### VERSION_DRIFT
- [ ] `README.md:3` → バージョン `1.2.0` は `package.json` の `1.3.0` と異なります
...
```

### Step 7: ターミナルサマリー表示

```
## Doc-Code Sync 完了

**CRITICAL**: <n>件  **WARNING**: <n>件  **INFO**: <n>件

### 主要な問題
1. <最も重要な問題の概要>
2. <次に重要な問題の概要>
3. ...

詳細レポート: .docstore/sync-report.md

### 推奨アクション
- <CRITICAL 項目がある場合> 即座にドキュメントを修正してください。
- <UNDOCUMENTED が多い場合> `/doc-integrate` で不足ドキュメントを追加してください。
- <STALE が多い場合> `/doc-update` でドキュメントを最新化してください。
```

## 注意事項

- コードの解析は AST パース（高精度）と静的パターンマッチング（フォールバック）の二段構成で行う。
- AST エクストラクタは `references/extractors/` 配下のスクリプトを Bash 経由で実行する。タイムアウトは **60秒**。
- AST エクストラクタは対象プロジェクトの `node_modules` に含まれるパーサーを使用する。追加の npm install は不要。
- AST パース失敗時は自動的に Grep 戦略にフォールバックする。エラーは stderr に記録される。
- 大規模プロジェクトでは、`docs/` 配下と README のみをデフォルトスキャン対象とする。
- `.gitignore` に含まれるファイルはスキャン対象外とする。
- `check-patterns.md` に定義されていない言語の場合は、汎用パターン（export, public 等）でフォールバックする。
- レポートのチェックボックス `- [ ]` は、手動で修正完了をマークするために使用する。
- Solidity プロジェクトでは NatSpec の整合性チェックが追加で実行される（`NATSPEC_DRIFT`, `MISSING_NATSPEC`）。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
