---
name: configure-ecc
description: Everything Claude Code のインタラクティブインストーラー — ユーザーレベルまたはプロジェクトレベルのディレクトリへの skills と rules の選択とインストールをガイドし、パスを検証し、必要に応じてインストールされたファイルを最適化します。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Configure Everything Claude Code (ECC)

Everything Claude Code プロジェクトのためのインタラクティブな段階的インストールウィザードです。`AskUserQuestion` を使用して、skills と rules の選択的インストールをユーザーに案内し、正確性を検証して最適化を提案します。

## アクティベートのタイミング

- ユーザーが「configure ecc」、「install ecc」、「setup everything claude code」またはそれに類似したことを言ったとき
- ユーザーがこのプロジェクトから skills または rules を選択的にインストールしたいとき
- ユーザーが既存の ECC インストールを検証または修正したいとき
- ユーザーがインストールされた skills または rules をプロジェクト向けに最適化したいとき

## 前提条件

この skill は、アクティベート前に Claude Code からアクセス可能である必要があります。ブートストラップには2つの方法があります：
1. **プラグイン経由**: `/plugin install everything-claude-code` — プラグインがこの skill を自動的に読み込みます
2. **手動**: この skill のみを `~/.claude/skills/configure-ecc/SKILL.md` にコピーし、「configure ecc」と言ってアクティベートします

---

## ステップ 0: ECC リポジトリのクローン

インストールの前に、最新の ECC ソースを `/tmp` にクローンします：

```bash
rm -rf /tmp/everything-claude-code
git clone https://github.com/affaan-m/everything-claude-code.git /tmp/everything-claude-code
```

後続のすべてのコピー操作のソースとして `ECC_ROOT=/tmp/everything-claude-code` を設定します。

クローンが失敗した場合（ネットワークの問題など）、`AskUserQuestion` を使用して、既存の ECC クローンへのローカルパスの提供をユーザーに依頼します。

---

## ステップ 1: インストールレベルの選択

`AskUserQuestion` を使用して、ユーザーにインストール先を尋ねます：

```
Question: "ECC コンポーネントをどこにインストールしますか？"
Options:
  - "User-level (~/.claude/)" — "すべての Claude Code プロジェクトに適用されます"
  - "Project-level (.claude/)" — "現在のプロジェクトのみに適用されます"
  - "Both" — "共通/共有アイテムはユーザーレベル、プロジェクト固有のアイテムはプロジェクトレベル"
```

選択を `INSTALL_LEVEL` として保存します。ターゲットディレクトリを設定します：
- User-level: `TARGET=~/.claude`
- Project-level: `TARGET=.claude`（現在のプロジェクトルートからの相対パス）
- Both: `TARGET_USER=~/.claude`, `TARGET_PROJECT=.claude`

ターゲットディレクトリが存在しない場合は作成します：
```bash
mkdir -p $TARGET/skills $TARGET/rules
```

---

## ステップ 2: Skills の選択とインストール

### 2a: Skill カテゴリの選択

4つのカテゴリに整理された27の skills があります。`multiSelect: true` で `AskUserQuestion` を使用します：

```
Question: "どの skill カテゴリをインストールしますか？"
Options:
  - "Framework & Language" — "Django, Spring Boot, Go, Python, Java, Frontend, Backend パターン"
  - "Database" — "PostgreSQL, ClickHouse, JPA/Hibernate パターン"
  - "Workflow & Quality" — "TDD, 検証, 学習, セキュリティレビュー, 圧縮"
  - "All skills" — "利用可能なすべての skill をインストール"
```

### 2b: 個別 Skills の確認

選択されたカテゴリごとに、以下の skills の完全なリストを印刷し、ユーザーに確認または特定のものの選択解除を依頼します。リストが4項目を超える場合は、リストをテキストとして印刷し、`AskUserQuestion` を使用して「リストされたすべてをインストール」オプションと、ユーザーが特定の名前を貼り付けるための「Other」を提供します。

**カテゴリ: Framework & Language（16 skills）**

| Skill | Description |
|-------|-------------|
| `backend-patterns` | Backend アーキテクチャ、API デザイン、Node.js/Express/Next.js のサーバーサイドベストプラクティス |
| `coding-standards` | TypeScript、JavaScript、React、Node.js のための普遍的なコーディング標準 |
| `django-patterns` | Django アーキテクチャ、DRF による REST API、ORM、キャッシング、シグナル、ミドルウェア |
| `django-security` | Django セキュリティ: 認証、CSRF、SQL インジェクション、XSS 防止 |
| `django-tdd` | pytest-django、factory_boy、モック、カバレッジを使った Django テスト |
| `django-verification` | Django 検証ループ: マイグレーション、リンティング、テスト、セキュリティスキャン |
| `frontend-patterns` | React、Next.js、状態管理、パフォーマンス、UI パターン |
| `golang-patterns` | 慣用的な Go パターン、堅牢な Go アプリケーションのための規則 |
| `golang-testing` | Go テスト: テーブル駆動テスト、サブテスト、ベンチマーク、ファジング |
| `java-coding-standards` | Spring Boot のための Java コーディング標準: 命名、不変性、Optional、ストリーム |
| `python-patterns` | Pythonic なイディオム、PEP 8、型ヒント、ベストプラクティス |
| `python-testing` | pytest を使った Python テスト、TDD、フィクスチャ、モック、パラメータ化 |
| `springboot-patterns` | Spring Boot アーキテクチャ、REST API、レイヤードサービス、キャッシング、非同期 |
| `springboot-security` | Spring Security: 認証/認可、バリデーション、CSRF、シークレット、レート制限 |
| `springboot-tdd` | JUnit 5、Mockito、MockMvc、Testcontainers を使った Spring Boot TDD |
| `springboot-verification` | Spring Boot 検証: ビルド、静的解析、テスト、セキュリティスキャン |

**カテゴリ: Database（3 skills）**

| Skill | Description |
|-------|-------------|
| `clickhouse-io` | ClickHouse パターン、クエリ最適化、分析、データエンジニアリング |
| `jpa-patterns` | JPA/Hibernate エンティティデザイン、リレーションシップ、クエリ最適化、トランザクション |
| `postgres-patterns` | PostgreSQL クエリ最適化、スキーマデザイン、インデックス作成、セキュリティ |

**カテゴリ: Workflow & Quality（8 skills）**

| Skill | Description |
|-------|-------------|
| `continuous-learning` | セッションから再利用可能なパターンを学習済み skills として自動抽出 |
| `continuous-learning-v2` | 信頼度スコアリングを伴う本能ベースの学習、skills/commands/agents に進化 |
| `eval-harness` | 評価駆動開発（EDD）のための形式的評価フレームワーク |
| `iterative-retrieval` | サブエージェントコンテキスト問題のための段階的コンテキスト洗練 |
| `security-review` | セキュリティチェックリスト: 認証、入力、シークレット、API、支払い機能 |
| `strategic-compact` | 論理的な間隔で手動コンテキスト圧縮を提案 |
| `tdd-workflow` | 80%以上のカバレッジで TDD を強制: ユニット、統合、E2E |
| `verification-loop` | 検証と品質ループのパターン |

**スタンドアロン**

| Skill | Description |
|-------|-------------|
| `project-guidelines-example` | プロジェクト固有の skills を作成するためのテンプレート |

### 2c: インストールの実行

選択された各 skill について、skill ディレクトリ全体をコピーします：
```bash
cp -r $ECC_ROOT/skills/<skill-name> $TARGET/skills/
```

注意: `continuous-learning` と `continuous-learning-v2` には追加ファイル（config.json、フック、スクリプト）があります — SKILL.md だけでなく、ディレクトリ全体がコピーされることを確認してください。

---

## ステップ 3: Rules の選択とインストール

`multiSelect: true` で `AskUserQuestion` を使用します：

```
Question: "どの rule セットをインストールしますか？"
Options:
  - "Common rules (Recommended)" — "言語に依存しない原則: コーディングスタイル、git ワークフロー、テスト、セキュリティなど（8ファイル）"
  - "TypeScript/JavaScript" — "TS/JS パターン、フック、Playwright を使ったテスト（5ファイル）"
  - "Python" — "Python パターン、pytest、black/ruff フォーマット（5ファイル）"
  - "Go" — "Go パターン、テーブル駆動テスト、gofmt/staticcheck（5ファイル）"
```

インストールの実行：
```bash
# Common rules（rules/ へのフラットコピー）
cp -r $ECC_ROOT/rules/common/* $TARGET/rules/

# 言語固有の rules（rules/ へのフラットコピー）
cp -r $ECC_ROOT/rules/typescript/* $TARGET/rules/   # 選択された場合
cp -r $ECC_ROOT/rules/python/* $TARGET/rules/        # 選択された場合
cp -r $ECC_ROOT/rules/golang/* $TARGET/rules/        # 選択された場合
```

**重要**: ユーザーが言語固有の rules を選択したが、common rules を選択しなかった場合、警告します：
> "言語固有の rules は common rules を拡張します。common rules なしでインストールすると、不完全なカバレッジになる可能性があります。common rules もインストールしますか？"

---

## ステップ 4: インストール後の検証

インストール後、これらの自動チェックを実行します：

### 4a: ファイル存在の検証

インストールされたすべてのファイルをリストし、ターゲットの場所に存在することを確認します：
```bash
ls -la $TARGET/skills/
ls -la $TARGET/rules/
```

### 4b: パス参照のチェック

インストールされたすべての `.md` ファイルでパス参照をスキャンします：
```bash
grep -rn "~/.claude/" $TARGET/skills/ $TARGET/rules/
grep -rn "../common/" $TARGET/rules/
grep -rn "skills/" $TARGET/skills/
```

**プロジェクトレベルのインストールの場合**、`~/.claude/` パスへの参照にフラグを立てます：
- skill が `~/.claude/settings.json` を参照している場合 — これは通常問題ありません（設定は常にユーザーレベルです）
- skill が `~/.claude/skills/` または `~/.claude/rules/` を参照している場合 — プロジェクトレベルのみにインストールされている場合は壊れている可能性があります
- skill が別の skill を名前で参照している場合 — 参照された skill もインストールされているか確認します

### 4c: Skills 間の相互参照のチェック

一部の skills は他の skills を参照しています。これらの依存関係を検証します：
- `django-tdd` は `django-patterns` を参照する場合があります
- `springboot-tdd` は `springboot-patterns` を参照する場合があります
- `continuous-learning-v2` は `~/.claude/homunculus/` ディレクトリを参照します
- `python-testing` は `python-patterns` を参照する場合があります
- `golang-testing` は `golang-patterns` を参照する場合があります
- 言語固有の rules は `common/` の対応物を参照します

### 4d: 問題の報告

見つかった各問題について報告します：
1. **File**: 問題のある参照を含むファイル
2. **Line**: 行番号
3. **Issue**: 何が問題か（例：「~/.claude/skills/python-patterns を参照していますが、python-patterns がインストールされていません」）
4. **Suggested fix**: 何をすべきか（例：「python-patterns skill をインストール」または「パスを .claude/skills/ に更新」）

---

## ステップ 5: インストールされたファイルの最適化（オプション）

`AskUserQuestion` を使用します：

```
Question: "インストールされたファイルをプロジェクト向けに最適化しますか？"
Options:
  - "Optimize skills" — "無関係なセクションを削除し、パスを調整し、技術スタックに合わせて調整"
  - "Optimize rules" — "カバレッジターゲットを調整し、プロジェクト固有のパターンを追加し、ツール設定をカスタマイズ"
  - "Optimize both" — "インストールされたすべてのファイルの完全な最適化"
  - "Skip" — "すべてをそのまま保持"
```

### skills を最適化する場合：
1. インストールされた各 SKILL.md を読み取ります
2. ユーザーにプロジェクトの技術スタックを尋ねます（まだ知られていない場合）
3. 各 skill について、無関係なセクションの削除を提案します
4. インストールターゲットの SKILL.md ファイルをその場で編集します（ソースリポジトリではありません）
5. ステップ4で見つかったパスの問題を修正します

### rules を最適化する場合：
1. インストールされた各 rule .md ファイルを読み取ります
2. ユーザーに設定を尋ねます：
   - テストカバレッジターゲット（デフォルト80%）
   - 優先するフォーマットツール
   - Git ワークフローの規則
   - セキュリティ要件
3. インストールターゲットの rule ファイルをその場で編集します

**重要**: インストールターゲット（`$TARGET/`）のファイルのみを変更し、ソース ECC リポジトリ（`$ECC_ROOT/`）のファイルは決して変更しないでください。

---

## ステップ 6: インストールサマリー

`/tmp` からクローンされたリポジトリをクリーンアップします：

```bash
rm -rf /tmp/everything-claude-code
```

その後、サマリーレポートを印刷します：

```
## ECC インストール完了

### インストールターゲット
- Level: [user-level / project-level / both]
- Path: [target path]

### インストールされた Skills（[count]）
- skill-1, skill-2, skill-3, ...

### インストールされた Rules（[count]）
- common（8ファイル）
- typescript（5ファイル）
- ...

### 検証結果
- [count] 件の問題が見つかり、[count] 件が修正されました
- [残りの問題をリスト]

### 適用された最適化
- [行われた変更をリスト、または「なし」]
```

---

## トラブルシューティング

### "Skills が Claude Code に認識されない"
- skill ディレクトリに `SKILL.md` ファイルが含まれていることを確認してください（単なる .md ファイルではありません）
- ユーザーレベルの場合: `~/.claude/skills/<skill-name>/SKILL.md` が存在することを確認
- プロジェクトレベルの場合: `.claude/skills/<skill-name>/SKILL.md` が存在することを確認

### "Rules が機能しない"
- Rules はサブディレクトリではなくフラットファイルです: `$TARGET/rules/coding-style.md`（正しい） vs `$TARGET/rules/common/coding-style.md`（フラットインストールでは不正）
- rules をインストールした後、Claude Code を再起動してください

### "プロジェクトレベルインストール後のパス参照エラー"
- 一部の skills は `~/.claude/` パスを想定しています。これらを見つけて修正するにはステップ4の検証を実行してください。
- `continuous-learning-v2` の場合、`~/.claude/homunculus/` ディレクトリは常にユーザーレベルです — これは予期されたものでエラーではありません。

---
> Source: [rx-k8/my-claude-code](https://github.com/rx-k8/my-claude-code) — distributed by [TomeVault](https://tomevault.io).
<!-- tomevault:4.0:skill_md:2026-06-04 -->
