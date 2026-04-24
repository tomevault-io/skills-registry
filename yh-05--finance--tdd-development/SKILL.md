---
name: tdd-development
description: t-wada流TDDに基づくテスト駆動開発のナレッジベース。TDDサイクル、テスト命名規則、ファイル配置、三角測量の手法を提供。テスト作成時、実装時にプロアクティブに使用。 Use when this capability is needed.
metadata:
  author: yh-05
---

# TDD 開発スキル

t-wada 流 TDD（テスト駆動開発）に基づく開発プロセスのナレッジベースを提供します。

## 目的

このスキルは以下を提供します：

- **TDD サイクル**: Red → Green → Refactor の基本フロー
- **テスト命名規則**: 日本語での意図を明確にする命名パターン
- **テスト種別とファイル配置**: 単体/プロパティ/統合テストの分類
- **三角測量**: 仮実装から一般化への導き方
- **テストテンプレート**: 各テスト種別のテンプレート

## いつ使用するか

### プロアクティブ使用（自動で検討）

以下の状況では、ユーザーが明示的に要求しなくても使用を検討：

1. **テスト作成時**
   - 新機能のテストを書く
   - バグ修正のテストを追加する
   - テストケースを設計する

2. **TDD 実践時**
   - 「テストから書いて」
   - 「TDD で進めて」
   - Red → Green → Refactor サイクルの実行

3. **テスト設計の議論**
   - テストの優先度を決める
   - テストの分類を検討する
   - プロパティテストの必要性を判断する

### 明示的な使用（ユーザー要求）

- `/write-tests` コマンドの実行時
- 「テストを書いて」「テストを設計して」などの直接的な要求

## TDD サイクル

```
🔴 Red     → 失敗するテストを書く
🟢 Green   → テストを通す最小限の実装
🔵 Refactor → リファクタリング
```

### 各フェーズの詳細

| フェーズ | 目標 | 注意点 |
|---------|------|--------|
| Red | 失敗するテストを1つ書く | 一度に複数書かない |
| Green | 最小限の実装でテストを通す | 仮実装（ハードコード）OK |
| Refactor | コードを整理する | テストが通る状態を維持 |

## テスト命名規則

```
test_[正常系|異常系|エッジケース|プロパティ|パラメトライズ]_条件で結果()
```

### 例

| パターン | 例 |
|---------|-----|
| 正常系 | `test_正常系_有効なデータで処理成功` |
| 異常系 | `test_異常系_不正なサイズでValueError` |
| エッジケース | `test_エッジケース_空リストで空結果` |
| プロパティ | `test_プロパティ_チャンク化しても全要素が保持` |
| パラメトライズ | `test_パラメトライズ_様々なサイズで正しく動作` |

## テスト種別とファイル配置

```
tests/{library}/
├── unit/                    # 単体テスト
│   └── test_{module}.py
├── property/                # プロパティベーステスト
│   └── test_{module}_property.py
├── integration/             # 統合テスト
│   └── test_{module}_integration.py
└── conftest.py              # 共通フィクスチャ
```

### 種別の選択基準

| 種別 | 対象 | ツール |
|------|------|--------|
| 単体テスト | 関数・クラスの基本動作 | pytest |
| プロパティテスト | 不変条件・数学的性質 | Hypothesis |
| 統合テスト | コンポーネント間連携 | pytest |

### プロパティテストが有効なケース

| 性質 | 例 |
|------|-----|
| 冪等性 | `encode(encode(x)) == encode(x)` |
| 可逆性 | `decode(encode(x)) == x` |
| 不変条件 | `len(flatten(chunks)) == len(original)` |
| 結合則 | `(a + b) + c == a + (b + c)` |

## テスト優先度

```yaml
P0 (必須):
  - 主要な正常系
  - クリティカルなエラーケース

P1 (重要):
  - 副次的な正常系
  - 一般的なエラーケース

P2 (推奨):
  - エッジケース
  - プロパティテスト

P3 (任意):
  - 稀なケース
  - パフォーマンステスト
```

## context7 によるドキュメント参照

テスト作成時には、テストフレームワークの最新ドキュメントを context7 MCP ツールで確認してください。

### 参照が必須のケース

- pytest の高度なフィクスチャ機能（scope, autouse, parametrize）
- Hypothesis でカスタムストラテジーを定義する際
- モック（unittest.mock, pytest-mock）の複雑なパターン
- 非同期テスト（pytest-asyncio）

### 使用手順

1. `mcp__context7__resolve-library-id` でライブラリ ID を取得
2. `mcp__context7__query-docs` でドキュメントをクエリ

## リソース

このスキルには以下のリソースが含まれています：

### ./guide.md

TDD 開発の詳細ガイド：

- TDD サイクルの詳細フロー
- 三角測量の手法
- テスト設計プロセス
- フィクスチャの活用

### ./templates/unit-test.md

単体テストテンプレート：

- クラス構造
- 命名規則
- アサーション例

### ./templates/property-test.md

プロパティベーステストテンプレート：

- Hypothesis strategies
- 不変条件の定義
- エッジケースの自動生成

### ./templates/integration-test.md

統合テストテンプレート：

- セットアップ・ティアダウン
- ファイル I/O テスト
- 複数コンポーネント連携

## 使用例

### 例1: 新機能のテスト作成

**状況**: chunk_list 関数のテストを書きたい

**処理**:
1. テスト TODO リストを作成
2. 正常系テストを書く（Red）
3. 最小限の実装（Green）
4. 三角測量で一般化
5. リファクタリング

**期待される出力**:
```python
class TestChunkList:
    def test_正常系_リストを指定サイズに分割できる(self) -> None:
        result = chunk_list([1, 2, 3, 4, 5], 2)
        assert result == [[1, 2], [3, 4], [5]]

    def test_異常系_チャンクサイズが0以下でValueError(self) -> None:
        with pytest.raises(ValueError, match="chunk_size must be positive"):
            chunk_list([1, 2, 3], 0)
```

---

### 例2: プロパティテストの追加

**状況**: chunk_list の不変条件をテストしたい

**処理**:
1. 不変条件を特定（全要素の保持）
2. Hypothesis strategy を設計
3. プロパティテストを実装

**期待される出力**:
```python
@given(
    items=st.lists(st.integers()),
    chunk_size=st.integers(min_value=1, max_value=100),
)
def test_プロパティ_チャンク化しても全要素が保持される(
    self, items: list[int], chunk_size: int
) -> None:
    chunks = chunk_list(items, chunk_size)
    flattened = [item for chunk in chunks for item in chunk]
    assert flattened == items
```

---

### 例3: TDD サイクルの実行

**状況**: add 関数を TDD で実装したい

**処理**:
```python
# Step 1: Red - 失敗するテストを書く
def test_add_正の数():
    assert add(2, 3) == 5

# Step 2: Green - 仮実装で通す
def add(a, b):
    return 5  # ハードコード

# Step 3: 三角測量 - 2つ目のテストで一般化を促す
def test_add_別の正の数():
    assert add(10, 20) == 30

# Step 4: Green - 一般化
def add(a, b):
    return a + b

# Step 5: Refactor - 必要に応じて整理
```

---

### 例4: テスト設計

**状況**: fetch_market_data 関数のテストを設計したい

**処理**:
1. 機能を分析（入力、出力、副作用、依存）
2. テスト TODO リストを作成
3. 優先度を付与
4. ファイル配置を決定

**期待される出力**:
```yaml
テスト設計書:
  対象: fetch_market_data
  ライブラリ: market_analysis

テスト TODO:
  単体テスト (tests/market_analysis/unit/test_fetcher.py):
    - test_正常系_有効なシンボルでデータ取得 [P0]
    - test_異常系_無効なシンボルでValueError [P0]
    - test_正常系_期間指定でデータ取得 [P1]

  プロパティテスト (tests/market_analysis/property/test_fetcher_property.py):
    - test_プロパティ_データ件数の不変条件 [P2]

  統合テスト (tests/market_analysis/integration/test_fetcher_integration.py):
    - test_統合_実APIからデータ取得 [P1]
```

## 品質基準

### 必須（MUST）

- [ ] テストは1つずつ追加（一度に複数書かない）
- [ ] 1つのテストで1つの振る舞いをテスト
- [ ] テストファーストの徹底（必ず失敗するテストから書く）
- [ ] `make test` で全テストがパス

### 推奨（SHOULD）

- 日本語テスト名で意図を明確に
- 不安な部分から着手
- テスト TODO リストを常に更新
- Red → Green でコミット

### 禁止（NEVER）

- 実装を先に書いてからテストを追加
- テストなしで機能を完成とする
- 失敗しないテストを書く（常に green になるテスト）
- 他のテストに依存するテストを書く

## テスト実行コマンド

```bash
make test              # 全テスト
make test-cov          # カバレッジ付き
make test-unit         # 単体テストのみ
make test-property     # プロパティテストのみ
make test-integration  # 統合テストのみ

# 特定テストのみ
uv run pytest tests/unit/test_xxx.py::TestClass::test_method -v
```

## 完了条件

- [ ] テスト TODO リストの全項目が完了
- [ ] `make test` が全テストパス
- [ ] カバレッジが適切な水準（目安: 80%以上）
- [ ] TDD サイクルが正しく実行されている

## 関連スキル

- **development-guidelines**: 開発プロセス全般
- **coding-standards**: コーディング規約

## 参考資料

- テンプレート: `template/tests/`
- 詳細ガイド: `./guide.md`
- テストテンプレート: `./templates/`

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/yh-05) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
