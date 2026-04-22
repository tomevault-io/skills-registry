---
name: skill-eval
description: > Use when this capability is needed.
metadata:
  author: stanah
---

# skill-eval: スキル評価スキル

doc-code-sync スキルの品質を2層で検証し、評価レポートを生成する。

## 評価対象

| Layer | 対象 | 手法 |
|-------|------|------|
| Layer 1 | エクストラクタスクリプト | `run-tests.sh` による単体テスト（決定的） |
| Layer 2 | スキルワークフロー全体 | フィクスチャに対する検出率・精度評価 |

## 利用可能なフィクスチャ

| 名前 | 言語 | 目的 | 期待 |
|------|------|------|------|
| `mismatch-project` | TypeScript + Solidity | 検出能力テスト | 高検出率 |
| `clean-project` | TypeScript | FP = 0 テスト | 検出ゼロ |
| `tricky-project` | TypeScript | FP 誘発テスト | 低 FP |
| `python-project` | Python (FastAPI) | 多言語対応テスト | 高検出率 |
| `go-project` | Go (Gin) | 多言語対応テスト | 高検出率 |
| `monorepo-project` | TypeScript | バレルエクスポート対応テスト | 高検出率 |

## ワークフロー

### Step 1: テスト対象の特定

1. 引数からテスト対象スキルとフィクスチャを判定する:
   - デフォルトスキル: `doc-code-sync`
   - デフォルトフィクスチャ: `mismatch-project`
   - オプション: `--fixture <name>` で特定フィクスチャを指定
   - オプション: `--all` で全フィクスチャをテスト
   - オプション: `--runs N` で複数回実行（統計評価）
2. テストフィクスチャの存在を確認する:
   - Layer 1: `.claude/skills/doc-code-sync/tests/run-tests.sh`
   - Layer 2: `.claude/skills/skill-eval/references/fixtures/<fixture-name>/`
3. Ground Truth ファイルのパスを記録する（**この時点では読み込まない**）:
   - `.claude/skills/skill-eval/references/ground-truths/<fixture-name>.json`

### Step 2: Layer 1 実行（エクストラクタ単体テスト）

1. テストスクリプトを実行する:
   ```bash
   bash .claude/skills/doc-code-sync/tests/run-tests.sh
   ```
2. 出力から PASS/FAIL 件数を記録する。
3. FAIL がある場合、失敗したテスト名と詳細を記録する。

### Step 3: Layer 2 実行（ワークフロー評価）

**重要**: 評価バイアスを排除するため、検出と評価を2フェーズに分離する。

#### Phase 1: 検出フェーズ（サブエージェント）

Task ツールを使用してサブエージェントを起動し、コンテキスト分離を実現する:

```
Task ツール呼び出し:
- subagent_type: "general-purpose"
- description: "doc-code-sync 検出実行"
- prompt: |
    以下のディレクトリに対して `/doc-code-sync` スキルを実行してください:

    対象ディレクトリ: .claude/skills/skill-eval/references/fixtures/<fixture-name>/

    手順:
    1. Skill ツールで `doc-code-sync` を呼び出す
    2. 対象ディレクトリを `.claude/skills/skill-eval/references/fixtures/<fixture-name>/` に指定
    3. 生成された `.docstore/sync-report.md` の内容を出力として返す

    **禁止事項**:
    - `ground-truths/` ディレクトリへのアクセス禁止
    - `ground-truth` を含むファイルの読み込み禁止

    タスク完了時、sync-report.md の全内容を返してください。
```

#### Phase 2: 評価フェーズ（親エージェント）

サブエージェント完了後、親エージェントが評価を実行する:

1. サブエージェントの出力（sync-report.md の内容）を取得する。
2. **この時点で初めて** ground-truth を読み込む:
   ```bash
   cat .claude/skills/skill-eval/references/ground-truths/<fixture-name>.json
   ```
3. Step 4 の Ground Truth 比較を実行する。

### Step 3b: 複数回実行モード（オプション）

`--runs N` オプション指定時:

1. Phase 1 を N 回実行する（各回サブエージェントを起動）
2. 各回の sync-report.md を `sync-report-{i}.md` として保存
3. Phase 2 で全レポートを集約し、統計を算出:
   - 平均検出率 (Mean Recall)
   - 標準偏差 (Std Dev)
   - 最小/最大検出率
   - 安定性スコア (1 - CV, where CV = Std Dev / Mean)
4. 安定性スコアが 0.8 以上で PASS

### Step 4: Ground Truth 比較

#### 標準フィクスチャ（mismatch-project, python-project, go-project, monorepo-project）

1. `ground-truths/<fixture-name>.json` の `expected_issues` 配列の各項目について:
   - **True Positive (TP)**: sync-report.md に対応する検出項目が存在する。
     - カテゴリが一致し、説明の趣旨が合致していれば TP とする。
     - ファイルパスが指定されている場合、そのファイルへの言及も確認する。
   - **False Negative (FN)**: 期待されたが sync-report.md に検出されていない。
   - **False Positive (FP)**: sync-report.md に検出されているが ground-truth に期待がない。
     - FP の判定は fuzzy（曖昧一致）とし、明らかに誤った検出のみカウントする。
2. カテゴリ別の検出率を算出する:
   - 検出率 (Recall) = TP / (TP + FN)
   - 精度 (Precision) = TP / (TP + FP)

#### 否定テスト: clean-project

1. sync-report.md に検出された問題が **ゼロ** であることを確認する。
2. いかなる問題も検出された場合、すべて False Positive としてカウント。
3. 合格基準: **FP = 0**

#### 否定テスト: tricky-project

1. `ground-truths/tricky-project.json` の `expected_non_issues` を確認する。
2. 各パターンについて:
   - sync-report.md にそのパターンが誤って検出されていないか確認。
   - 検出されていれば False Positive としてカウント。
3. 合格基準: **FP ≤ 1** (max_false_positives)

### Step 5: 評価レポート生成

`.docstore/eval-report.md` に以下の形式で出力する:

```markdown
# Skill Evaluation Report

**対象スキル**: doc-code-sync
**評価日**: YYYY-MM-DD
**フィクスチャ**: <fixture-name>

## Layer 1: エクストラクタテスト

| テスト | 結果 |
|--------|------|
| TypeScript JSON スキーマ | PASS/FAIL |
| TypeScript シンボル検出 | PASS/FAIL |
| TypeScript ルート検出 | PASS/FAIL |
| TypeScript 設定キー検出 | PASS/FAIL |
| Solidity コントラクト検出 | PASS/FAIL |
| Solidity NatSpec 抽出 | PASS/FAIL |
| 合計 | X/Y PASS |

## Layer 2: ワークフロー評価

### 検出率サマリー

| カテゴリ | 期待 | 検出 | 検出率 |
|---------|------|------|--------|
| BROKEN_REF | N | N | X% |
| STALE_EXAMPLE | N | N | X% |
| UNDOCUMENTED | N | N | X% |
| CONFIG_DRIFT | N | N | X% |
| VERSION_DRIFT | N | N | X% |
| API_DRIFT | N | N | X% |
| NATSPEC_DRIFT | N | N | X% |
| MISSING_NATSPEC | N | N | X% |

### True Positives (検出成功)

- ✅ CATEGORY: 説明

### False Negatives (検出漏れ)

- ❌ CATEGORY: 説明

### False Positives (誤検出)

- ⚠️ CATEGORY: 説明
- (該当なし の場合はその旨を記載)

## 総合評価

**検出率 (Recall)**: X% (TP / (TP + FN))
**精度 (Precision)**: X% (TP / (TP + FP))
**合格基準**: 検出率 75% 以上
**判定**: PASS / FAIL
```

### 複数回実行時のレポート拡張

```markdown
## Layer 2: ワークフロー評価（N=5 回実行）

### 統計サマリー

| 指標 | 値 |
|------|-----|
| 実行回数 | 5 |
| 平均検出率 | 87.5% |
| 標準偏差 | 12.5% |
| 最小検出率 | 75% |
| 最大検出率 | 100% |
| 安定性スコア | 0.86 |

### 各回の結果

| Run | TP | FN | FP | Recall | Precision |
|-----|----|----|----| -------|-----------|
| 1 | 8 | 0 | 0 | 100% | 100% |
| 2 | 7 | 1 | 0 | 87.5% | 100% |
| ... |

### 判定

**安定性基準**: CV < 0.2 (安定性スコア > 0.8)
**判定**: PASS / FAIL
```

### 否定テスト時のレポート形式

```markdown
## Layer 2: 否定テスト評価

### フィクスチャ: clean-project

**目的**: False Positive = 0 の確認

| 指標 | 結果 |
|------|------|
| 検出数 | 0 |
| 期待値 | 0 |
| FP 数 | 0 |
| 判定 | PASS |

### フィクスチャ: tricky-project

**目的**: FP 誘発パターンの正しい除外確認

| パターン | 誤検出 | 判定 |
|---------|--------|------|
| 同名関数の誤検出 | なし | ✅ |
| コメント内コードの誤検出 | なし | ✅ |
| テストファイルの誤検出 | なし | ✅ |
| deprecated 関数の誤検出 | なし | ✅ |

| 指標 | 結果 |
|------|------|
| FP 数 | 0 |
| 許容 FP | 1 |
| 判定 | PASS |
```

### Step 6: ターミナルサマリー表示

```
## Skill Evaluation 完了

### Layer 1: エクストラクタテスト
X/Y PASS

### Layer 2: ワークフロー評価
フィクスチャ: <name>
検出率: X% (TP/期待件数)
精度: X% (TP/(TP+FP))

### 判定
(PASS: 検出率 75% 以上 / FAIL: 検出率 75% 未満)

詳細レポート: .docstore/eval-report.md
```

## 合格基準

| フィクスチャ | 指標 | 基準 |
|-------------|------|------|
| mismatch-project | Recall | ≥ 75% |
| python-project | Recall | ≥ 75% |
| go-project | Recall | ≥ 75% |
| monorepo-project | Recall | ≥ 75% |
| clean-project | FP | = 0 |
| tricky-project | FP | ≤ 1 |
| 複数回実行 | 安定性スコア | ≥ 0.8 (CV < 0.2) |

## 注意事項

- Layer 1 は決定的テストであり、全 PASS が期待される。FAIL がある場合はエクストラクタのバグを示す。
- Layer 2 の検出は LLM ベースのため、実行ごとに結果が異なる可能性がある。
- Ground Truth 比較は意味的一致（fuzzy matching）で行い、完全一致は求めない。
- フィクスチャを変更した場合は対応する ground-truth ファイルも更新すること。
- 否定テストは FP 測定が主目的であり、Recall は評価しない。

## コンテキスト分離の設計根拠

Layer 2 で Task ツールを使用する理由:

1. **評価バイアスの排除**: サブエージェントは親のコンテキストを引き継がないため、ground-truth を知らない状態で検出を実行できる。
2. **真の検出能力測定**: 「答えを見てからテストを受ける」状態を防ぎ、スキルの純粋な検出能力を評価できる。
3. **再現性向上**: 検出と評価が明確に分離され、結果の検証が容易になる。

Ground Truth ファイルは `references/ground-truths/` に配置され、フィクスチャディレクトリとは分離されている。これにより、サブエージェントがフィクスチャを探索しても ground-truth にアクセスすることはない。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/stanah) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
