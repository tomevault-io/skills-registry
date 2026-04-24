---
name: tdd-workflow-python
description: 新機能の記述、バグ修正、またはコードのリファクタリング時にこのスキルを使用します。pytest、mypy、ruff を使用して、ユニット、統合、E2Eテストを含む80%以上のカバレッジでテスト駆動開発を強制します。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Python テスト駆動開発ワークフロー

このスキルは、すべての Python コード開発が包括的なテストカバレッジを伴うTDD原則に従うことを保証します。

## いつアクティブ化するか

- 新機能または機能を書くとき
- バグや問題を修正するとき
- 既存のコードをリファクタリングするとき
- APIエンドポイントを追加するとき
- 新しいサービスやモジュールを作成するとき

## 中核原則

### 1. コードの前にテスト
常にテストを最初に書き、次にテストをパスするコードを実装します。

### 2. カバレッジ要件
- 最小80%カバレッジ (ユニット + 統合 + E2E)
- すべてのエッジケースがカバーされている
- エラーシナリオがテストされている
- 境界条件が検証されている

### 3. テストタイプ

#### ユニットテスト
- 個別の関数とユーティリティ
- ビジネスロジック
- 純粋関数
- ヘルパーとユーティリティ

#### 統合テスト
- FastAPI エンドポイント
- データベース操作
- サービス間相互作用
- 外部API呼び出し

#### E2Eテスト (Playwright)
- 重要なユーザーフロー
- 完全なワークフロー
- ブラウザ自動化
- UI相互作用

## TDDワークフローステップ

### ステップ1: ユーザージャーニーを書く
```
[ロール]として、[アクション]したい、[利益]のために

例:
ユーザーとして、市場を意味的に検索したい、
正確なキーワードなしでも関連する市場を見つけられるように。
```

### ステップ2: テストケースを生成
各ユーザージャーニーに対して、包括的なテストケースを作成:

```python
import pytest
from src.services.search_service import search_markets

class TestSemanticSearch:
    def test_returns_relevant_markets_for_query(self):
        # テスト実装
        pass

    def test_handles_empty_query_gracefully(self):
        # エッジケースをテスト
        pass

    def test_falls_back_to_substring_search_when_redis_unavailable(self):
        # フォールバック動作をテスト
        pass

    def test_sorts_results_by_similarity_score(self):
        # ソートロジックをテスト
        pass
```

### ステップ3: テストを実行 (失敗するはず)
```bash
pytest
# テストは失敗するはず - まだ実装していない
```

### ステップ4: コードを実装
テストをパスするための最小限のコードを書く:

```python
# テストに導かれた実装
async def search_markets(query: str) -> list[Market]:
    # ここに実装
    pass
```

### ステップ5: 再度テストを実行
```bash
pytest
# テストは今パスするはず
```

### ステップ6: リファクタリング
テストをグリーンに保ちながらコード品質を改善:
- 重複を削除
- 命名を改善
- パフォーマンスを最適化
- 可読性を向上

### ステップ7: カバレッジを検証
```bash
pytest --cov=src --cov-report=html
# 80%以上のカバレッジが達成されたことを確認
```

## テストパターン

### ユニットテストパターン (pytest)

```python
import pytest
from src.lib.similarity import calculate_cosine_similarity

def test_calculates_similarity_correctly():
    # Arrange
    vector1 = [1, 0, 0]
    vector2 = [0, 1, 0]

    # Act
    similarity = calculate_cosine_similarity(vector1, vector2)

    # Assert
    assert similarity == 0

def test_raises_error_for_empty_vectors():
    with pytest.raises(ValueError):
        calculate_cosine_similarity([], [])

def test_handles_normalized_vectors():
    vector1 = [0.6, 0.8]
    vector2 = [0.6, 0.8]

    similarity = calculate_cosine_similarity(vector1, vector2)

    assert similarity == pytest.approx(1.0, abs=0.01)

# pytest fixtures を使用
@pytest.fixture
def sample_vectors():
    return {
        'v1': [1, 2, 3],
        'v2': [4, 5, 6]
    }

def test_with_fixture(sample_vectors):
    result = calculate_cosine_similarity(
        sample_vectors['v1'],
        sample_vectors['v2']
    )
    assert result > 0
```

### API統合テストパターン (FastAPI)

```python
import pytest
from fastapi.testclient import TestClient
from src.main import app

client = TestClient(app)

class TestMarketsAPI:
    def test_get_markets_returns_success(self):
        response = client.get('/api/markets')

        assert response.status_code == 200
        data = response.json()
        assert data['success'] is True
        assert isinstance(data['data'], list)

    def test_create_market_with_valid_data(self):
        market_data = {
            'name': 'Test Market',
            'description': 'Test description',
            'end_date': '2025-12-31T00:00:00Z'
        }

        response = client.post('/api/markets', json=market_data)

        assert response.status_code == 201
        data = response.json()
        assert data['name'] == 'Test Market'

    def test_validates_query_parameters(self):
        response = client.get('/api/markets?limit=invalid')

        assert response.status_code == 400

    def test_handles_database_errors_gracefully(self):
        # データベース障害をモック
        # エラー処理をテスト
        pass

# pytest-asyncio を使用した非同期テスト
import pytest
from httpx import AsyncClient
from src.main import app

@pytest.mark.asyncio
async def test_async_endpoint():
    async with AsyncClient(app=app, base_url='http://test') as client:
        response = await client.get('/api/markets')
        assert response.status_code == 200
```

### E2Eテストパターン (Playwright)

```python
import pytest
from playwright.sync_api import Page, expect

def test_user_can_search_and_filter_markets(page: Page):
    # marketsページに移動
    page.goto('/')
    page.click('a[href="/markets"]')

    # ページが読み込まれたことを確認
    expect(page.locator('h1')).to_contain_text('Markets')

    # 市場を検索
    page.fill('input[placeholder="Search markets"]', 'election')

    # デバウンスと結果を待つ
    page.wait_for_timeout(600)

    # 検索結果が表示されたことを確認
    results = page.locator('[data-testid="market-card"]')
    expect(results).to_have_count(5)

    # 結果に検索語が含まれることを確認
    first_result = results.first
    expect(first_result).to_contain_text('election', ignore_case=True)

    # ステータスでフィルター
    page.click('button:has-text("Active")')

    # フィルターされた結果を確認
    expect(results).to_have_count(3)

def test_user_can_create_a_new_market(page: Page):
    # 最初にログイン
    page.goto('/creator-dashboard')

    # 市場作成フォームを入力
    page.fill('input[name="name"]', 'Test Market')
    page.fill('textarea[name="description"]', 'Test description')
    page.fill('input[name="endDate"]', '2025-12-31')

    # フォームを送信
    page.click('button[type="submit"]')

    # 成功メッセージを確認
    expect(page.locator('text=Market created successfully')).to_be_visible()

    # 市場ページへのリダイレクトを確認
    expect(page).to_have_url('/markets/test-market')
```

## テストファイル構成

```
tests/
├── unit/
│   ├── lib/
│   │   ├── test_similarity.py
│   │   └── test_utils.py
│   ├── services/
│   │   ├── test_market_service.py
│   │   └── test_user_service.py
│   └── repositories/
│       └── test_market_repo.py
├── integration/
│   ├── api/
│   │   ├── test_markets_api.py
│   │   └── test_users_api.py
│   └── db/
│       └── test_database.py
└── e2e/
    ├── test_markets.py
    ├── test_trading.py
    └── test_auth.py
```

## 外部サービスのモック

### データベースモック

```python
import pytest
from unittest.mock import AsyncMock, patch

@pytest.fixture
def mock_db_session():
    session = AsyncMock()
    session.execute = AsyncMock()
    session.commit = AsyncMock()
    return session

def test_market_repository_with_mock(mock_db_session):
    # テスト実装
    pass

# pytest-mock を使用
def test_with_pytest_mock(mocker):
    mock_query = mocker.patch('src.repositories.market_repo.session.execute')
    mock_query.return_value.scalars.return_value.all.return_value = []

    # テスト実装
    pass
```

### Redisモック

```python
import pytest
from unittest.mock import AsyncMock, patch

@pytest.fixture
def mock_redis():
    redis = AsyncMock()
    redis.get = AsyncMock(return_value=None)
    redis.setex = AsyncMock()
    return redis

@pytest.mark.asyncio
async def test_cached_market(mock_redis, mocker):
    mocker.patch('src.lib.redis.get_redis', return_value=mock_redis)

    result = await cached_market('test-id')

    mock_redis.get.assert_called_once_with('market:test-id')
```

### OpenAIモック

```python
import pytest
from unittest.mock import AsyncMock, patch

@pytest.fixture
def mock_openai():
    return [0.1] * 1536  # 1536次元埋め込みをモック

@pytest.mark.asyncio
async def test_generate_embedding(mocker, mock_openai):
    mock_create = mocker.patch(
        'openai.embeddings.create',
        return_value=AsyncMock(data=[AsyncMock(embedding=mock_openai)])
    )

    from src.lib.openai_client import generate_embedding
    result = await generate_embedding('test query')

    assert len(result) == 1536
    mock_create.assert_called_once()
```

## テストカバレッジ検証

### カバレッジレポートを実行
```bash
# HTMLレポート生成
pytest --cov=src --cov-report=html
open htmlcov/index.html

# ターミナルレポート（欠落行付き）
pytest --cov=src --cov-report=term-missing

# カバレッジ閾値を設定
pytest --cov=src --cov-fail-under=80
```

### pytest.ini でカバレッジしきい値を設定
```ini
[tool:pytest]
addopts = --cov=src --cov-report=html --cov-fail-under=80
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
```

## 避けるべき一般的なテストミス

### ❌ 間違い: 実装詳細のテスト
```python
# 内部状態をテストしない
def test_internal_state():
    service = MarketService()
    service.process()
    assert service._internal_count == 5  # 悪い例
```

### ✅ 正しい: ユーザーに見える動作をテスト
```python
# パブリックAPIをテスト
def test_public_behavior():
    service = MarketService()
    result = service.process()
    assert result['count'] == 5  # 良い例
```

### ❌ 間違い: 脆弱なセレクタ
```python
# 簡単に壊れる
page.click('.css-class-xyz')
```

### ✅ 正しい: セマンティックセレクタ
```python
# 変更に強い
page.click('button:has-text("Submit")')
page.click('[data-testid="submit-button"]')
```

### ❌ 間違い: テスト分離なし
```python
# テストが互いに依存
def test_creates_user():
    global user
    user = create_user()

def test_updates_same_user():
    # 前のテストに依存 - 悪い例
    update_user(user)
```

### ✅ 正しい: 独立したテスト
```python
# 各テストが独自のデータをセットアップ
@pytest.fixture
def user():
    return create_test_user()

def test_creates_user(user):
    # テストロジック
    pass

def test_updates_user(user):
    # 更新ロジック
    pass
```

## 継続的テスト

### 開発中のウォッチモード
```bash
# pytest-watch を使用
ptw
# または
pytest-watch

# ファイル変更時に自動的にテストを実行
```

### プレコミットフック
```bash
# .pre-commit-config.yaml
repos:
  - repo: local
    hooks:
      - id: pytest
        name: pytest
        entry: pytest
        language: system
        pass_filenames: false
        always_run: true
```

### CI/CD統合
```yaml
# GitHub Actions
name: Test

on: [push, pull_request]

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-python@v4
        with:
          python-version: '3.11'

      - name: Install dependencies
        run: |
          pip install -r requirements.txt
          pip install pytest pytest-cov pytest-asyncio

      - name: Run tests
        run: pytest --cov=src --cov-report=xml

      - name: Upload coverage
        uses: codecov/codecov-action@v3
        with:
          file: ./coverage.xml
```

## ベストプラクティス

1. **最初にテストを書く** - 常にTDD
2. **テストごとに1つのアサート** - 単一の動作に焦点を当てる
3. **説明的なテスト名** - テストされていることを説明する
4. **Arrange-Act-Assert** - 明確なテスト構造
5. **外部依存関係をモック** - ユニットテストを分離
6. **エッジケースをテスト** - None、空リスト、最大値
7. **エラーパスをテスト** - ハッピーパスだけではない
8. **テストを高速に保つ** - ユニットテストは各50ms未満
9. **テスト後のクリーンアップ** - 副作用なし
10. **カバレッジレポートをレビュー** - ギャップを特定

## pytest の便利な機能

### パラメータ化テスト

```python
import pytest

@pytest.mark.parametrize('input,expected', [
    ([1, 0, 0], [0, 1, 0], 0),
    ([1, 0, 0], [1, 0, 0], 1),
    ([1, 1, 0], [0, 1, 1], 0.5),
])
def test_similarity_with_parameters(input, expected):
    result = calculate_cosine_similarity(input[0], input[1])
    assert result == pytest.approx(expected, abs=0.01)
```

### マーカー

```python
import pytest

@pytest.mark.slow
def test_slow_operation():
    # 時間のかかるテスト
    pass

@pytest.mark.integration
def test_database_integration():
    # 統合テスト
    pass

# 特定のマーカーのみ実行
# pytest -m slow
# pytest -m "not slow"
```

### テンポラリディレクトリ

```python
def test_file_operations(tmp_path):
    # tmp_path は一時ディレクトリ
    test_file = tmp_path / 'test.txt'
    test_file.write_text('test content')

    assert test_file.read_text() == 'test content'
```

## 成功メトリクス

- 80%以上のコードカバレッジ達成
- すべてのテストがパス (グリーン)
- スキップまたは無効化されたテストなし
- 高速なテスト実行 (ユニットテストで30秒未満)
- E2Eテストが重要なユーザーフローをカバー
- テストが本番前にバグをキャッチ

---

**覚えておくこと**: テストはオプションではありません。自信を持ったリファクタリング、迅速な開発、本番環境の信頼性を可能にする安全網です。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-16 -->
