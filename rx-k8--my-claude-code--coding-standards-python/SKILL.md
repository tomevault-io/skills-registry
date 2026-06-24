---
name: coding-standards-python
description: Python 開発のための普遍的なコーディング標準、ベストプラクティス、パターン。pytest、mypy、ruff を使用。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Python コーディング標準とベストプラクティス

すべての Python プロジェクトに適用可能な普遍的なコーディング標準。

## コード品質の原則

### 1. 可読性優先
- コードは書かれるより読まれることが多い
- 明確な変数名と関数名
- コメントよりも自己文書化コードを優先
- 一貫したフォーマット (ruff format)

### 2. KISS (Keep It Simple, Stupid)
- 動作する最もシンプルなソリューション
- 過度なエンジニアリングを避ける
- 時期尚早な最適化をしない
- 賢いコードよりも理解しやすいコード

### 3. DRY (Don't Repeat Yourself)
- 共通ロジックを関数に抽出
- 再利用可能なモジュールを作成
- パッケージ間でユーティリティを共有
- コピー&ペーストプログラミングを避ける

### 4. YAGNI (You Aren't Gonna Need It)
- 必要になる前に機能を構築しない
- 推測的な一般化を避ける
- 必要な時にのみ複雑さを追加
- シンプルに始めて、必要に応じてリファクタリング

## Python 標準

### 変数の命名

```python
# ✅ 良い例: 説明的な名前 (snake_case)
market_search_query = 'election'
is_user_authenticated = True
total_revenue = 1000

# ❌ 悪い例: 不明確な名前
q = 'election'
flag = True
x = 1000
```

### 関数の命名

```python
# ✅ 良い例: 動詞-名詞パターン (snake_case)
async def fetch_market_data(market_id: str) -> dict:
    pass

def calculate_similarity(a: list[float], b: list[float]) -> float:
    pass

def is_valid_email(email: str) -> bool:
    pass

# ❌ 悪い例: 不明確または名詞のみ
async def market(id):
    pass

def similarity(a, b):
    pass

def email(e):
    pass
```

### 不変性パターン (重要)

```python
from dataclasses import dataclass, replace

# ✅ 常に frozen dataclasses または replace を使用
@dataclass(frozen=True)
class User:
    name: str
    age: int

def update_user(user: User, name: str) -> User:
    return replace(user, name=name)

# Pydantic での例
from pydantic import BaseModel

class User(BaseModel):
    name: str
    age: int

def update_user(user: User, name: str) -> User:
    return user.model_copy(update={'name': name})

# ❌ 直接ミューテートしない
@dataclass
class User:
    name: str
    age: int

def update_user(user: User, name: str) -> User:
    user.name = name  # 悪い例
    return user
```

### エラーハンドリング

```python
# ✅ 良い例: 包括的なエラーハンドリング
async def fetch_data(url: str) -> dict:
    try:
        async with httpx.AsyncClient() as client:
            response = await client.get(url)

            if response.status_code != 200:
                raise RuntimeError(
                    f'HTTP {response.status_code}: {response.reason_phrase}'
                )

            return response.json()
    except httpx.RequestError as e:
        print(f'Fetch failed: {e}')
        raise RuntimeError('Failed to fetch data') from e

# ❌ 悪い例: エラーハンドリングなし
async def fetch_data(url):
    async with httpx.AsyncClient() as client:
        response = await client.get(url)
        return response.json()
```

### Async/Await ベストプラクティス

```python
import asyncio

# ✅ 良い例: 可能な場合は並列実行
users, markets, stats = await asyncio.gather(
    fetch_users(),
    fetch_markets(),
    fetch_stats()
)

# ❌ 悪い例: 不必要な逐次実行
users = await fetch_users()
markets = await fetch_markets()
stats = await fetch_stats()
```

### 型ヒント

```python
from typing import Optional
from datetime import datetime

# ✅ 良い例: 完全な型ヒント
class Market:
    id: str
    name: str
    status: str  # 'active' | 'resolved' | 'closed'
    created_at: datetime

async def get_market(market_id: str) -> Market:
    # 実装
    pass

# Pydantic を使用した例
from pydantic import BaseModel
from datetime import datetime
from typing import Literal

class Market(BaseModel):
    id: str
    name: str
    status: Literal['active', 'resolved', 'closed']
    created_at: datetime

async def get_market(market_id: str) -> Market:
    # 実装
    pass

# ❌ 悪い例: 型ヒントなし
def get_market(market_id):
    # 実装
    pass
```

## FastAPI ベストプラクティス

### エンドポイント構造

```python
from fastapi import FastAPI, HTTPException
from pydantic import BaseModel

app = FastAPI()

class MarketCreate(BaseModel):
    name: str
    description: str
    end_date: str

class Market(BaseModel):
    id: str
    name: str
    status: str

@app.get('/api/markets', response_model=list[Market])
async def list_markets():
    markets = await market_service.list()
    return markets

@app.post('/api/markets', response_model=Market, status_code=201)
async def create_market(data: MarketCreate):
    market = await market_service.create(data)
    return market

# ❌ 悪い例: 型なし、不明確な構造
@app.post('/api/markets')
async def create_market(data):
    return {'success': True}
```

### 依存性注入

```python
from typing import Annotated
from fastapi import Depends

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get('/api/markets/{market_id}')
async def get_market(
    market_id: str,
    db: Annotated[Session, Depends(get_db)]
):
    market = db.query(Market).filter(Market.id == market_id).first()
    if not market:
        raise HTTPException(status_code=404, detail='Market not found')
    return market
```

### 状態管理

```python
# ✅ 良い例: 不変データ構造
from dataclasses import dataclass, replace

@dataclass(frozen=True)
class AppState:
    count: int
    is_active: bool

def increment_count(state: AppState) -> AppState:
    return replace(state, count=state.count + 1)

# ❌ 悪い例: ミューテーション
@dataclass
class AppState:
    count: int
    is_active: bool

def increment_count(state: AppState) -> AppState:
    state.count += 1  # ミューテーション!
    return state
```

## API 設計標準

### REST API 規約

```
GET    /api/markets              # すべてのマーケットをリスト
GET    /api/markets/{id}         # 特定のマーケットを取得
POST   /api/markets              # 新しいマーケットを作成
PUT    /api/markets/{id}         # マーケットを更新 (完全)
PATCH  /api/markets/{id}         # マーケットを更新 (部分)
DELETE /api/markets/{id}         # マーケットを削除

# フィルタリング用のクエリパラメータ
GET /api/markets?status=active&limit=10&offset=0
```

### レスポンス形式

```python
from typing import Generic, TypeVar, Optional
from pydantic import BaseModel

T = TypeVar('T')

class PaginationMeta(BaseModel):
    total: int
    page: int
    limit: int

class ApiResponse(BaseModel, Generic[T]):
    success: bool
    data: Optional[T] = None
    error: Optional[str] = None
    meta: Optional[PaginationMeta] = None

# 成功レスポンス
from fastapi.responses import JSONResponse

return JSONResponse(
    content={
        'success': True,
        'data': [market.model_dump() for market in markets],
        'meta': {'total': 100, 'page': 1, 'limit': 10}
    }
)

# エラーレスポンス
return JSONResponse(
    content={'success': False, 'error': 'Invalid request'},
    status_code=400
)
```

### 入力検証

```python
from pydantic import BaseModel, Field, validator
from datetime import datetime

# ✅ 良い例: Pydantic スキーマ検証
class CreateMarketInput(BaseModel):
    name: str = Field(min_length=1, max_length=200)
    description: str = Field(min_length=1, max_length=2000)
    end_date: datetime
    categories: list[str] = Field(min_length=1)

    @validator('end_date')
    def end_date_must_be_future(cls, v):
        if v < datetime.now():
            raise ValueError('End date must be in the future')
        return v

@app.post('/api/markets')
async def create_market(data: CreateMarketInput):
    # Pydantic が自動的に検証
    market = await market_service.create(data)
    return market
```

## ファイル構成

### プロジェクト構造

```
src/
├── api/                   # FastAPI アプリケーション
│   ├── routes/           # API ルート
│   ├── dependencies.py   # 依存性注入
│   └── middleware.py     # ミドルウェア
├── services/             # ビジネスロジック
│   ├── market_service.py
│   └── user_service.py
├── repositories/         # データアクセス層
│   ├── market_repo.py
│   └── user_repo.py
├── models/               # データモデル
│   ├── market.py
│   └── user.py
├── schemas/              # Pydantic スキーマ
│   ├── market.py
│   └── user.py
├── lib/                  # ユーティリティと設定
│   ├── database.py
│   ├── redis.py
│   └── utils/
└── tests/                # テスト
    ├── unit/
    ├── integration/
    └── e2e/
```

### ファイル命名

```
models/market.py              # モデルは snake_case
services/market_service.py    # サービスは snake_case
schemas/create_market.py      # スキーマは snake_case
lib/format_date.py            # ユーティリティは snake_case
```

## コメントとドキュメント

### コメントのタイミング

```python
# ✅ 良い例: 何をではなく、なぜを説明
# 障害時に API を圧倒しないよう指数バックオフを使用
delay = min(1000 * (2 ** retry_count), 30000)

# 大きな配列でのパフォーマンスのため、意図的にミューテーションを使用
items.append(new_item)

# ❌ 悪い例: 明白なことを述べる
# カウンターを 1 増やす
count += 1

# ユーザーの名前に名前を設定
name = user.name
```

### パブリック API のドキュメント

```python
async def search_markets(
    query: str,
    limit: int = 10
) -> list[Market]:
    """
    セマンティック類似性を使用してマーケットを検索します。

    Args:
        query: 自然言語検索クエリ
        limit: 最大結果数 (デフォルト: 10)

    Returns:
        類似度スコアでソートされたマーケットのリスト

    Raises:
        RuntimeError: OpenAI API が失敗するか Redis が利用不可の場合

    Example:
        >>> results = await search_markets('election', 5)
        >>> print(results[0].name)
        'Trump vs Biden'
    """
    # 実装
    pass
```

## パフォーマンスベストプラクティス

### キャッシング

```python
from functools import lru_cache
import redis.asyncio as redis

@lru_cache(maxsize=128)
def get_redis():
    return redis.from_url('redis://localhost')

async def cached_market(market_id: str) -> Optional[Market]:
    r = get_redis()

    # キャッシュをチェック
    cached = await r.get(f'market:{market_id}')
    if cached:
        return Market.parse_raw(cached)

    # DBから取得
    market = await market_repo.find_by_id(market_id)
    if market:
        await r.setex(
            f'market:{market_id}',
            300,  # 5分間
            market.json()
        )

    return market
```

### データベースクエリ

```python
from sqlalchemy import select
from sqlalchemy.orm import selectinload

# ✅ 良い例: 必要なカラムのみを選択
async with async_session() as session:
    result = await session.execute(
        select(Market.id, Market.name, Market.status)
        .limit(10)
    )
    markets = result.all()

# ✅ 良い例: N+1 問題を回避
async with async_session() as session:
    result = await session.execute(
        select(Market)
        .options(selectinload(Market.trades))
        .limit(10)
    )
    markets = result.scalars().all()

# ❌ 悪い例: すべてを選択
async with async_session() as session:
    result = await session.execute(select(Market))
    markets = result.scalars().all()
```

## テスト標準

### テスト構造 (AAA パターン)

```python
def test_calculates_similarity_correctly():
    # Arrange (準備)
    vector1 = [1, 0, 0]
    vector2 = [0, 1, 0]

    # Act (実行)
    similarity = calculate_cosine_similarity(vector1, vector2)

    # Assert (検証)
    assert similarity == 0
```

### テストの命名

```python
# ✅ 良い例: 説明的なテスト名
def test_returns_empty_array_when_no_markets_match_query():
    pass

def test_raises_error_when_openai_api_key_is_missing():
    pass

def test_falls_back_to_substring_search_when_redis_unavailable():
    pass

# ❌ 悪い例: 曖昧なテスト名
def test_works():
    pass

def test_search():
    pass
```

## コードスメルの検出

これらのアンチパターンに注意:

### 1. 長い関数

```python
# ❌ 悪い例: 50 行以上の関数
def process_market_data():
    # 100 行のコード
    pass

# ✅ 良い例: 小さな関数に分割
def process_market_data():
    validated = validate_data()
    transformed = transform_data(validated)
    return save_data(transformed)
```

### 2. 深いネスト

```python
# ❌ 悪い例: 5 段階以上のネスト
if user:
    if user.is_admin:
        if market:
            if market.is_active:
                if has_permission:
                    # 何かをする
                    pass

# ✅ 良い例: 早期リターン
if not user:
    return
if not user.is_admin:
    return
if not market:
    return
if not market.is_active:
    return
if not has_permission:
    return

# 何かをする
```

### 3. マジックナンバー

```python
# ❌ 悪い例: 説明のない数値
if retry_count > 3:
    pass
await asyncio.sleep(0.5)

# ✅ 良い例: 名前付き定数
MAX_RETRIES = 3
DEBOUNCE_DELAY_SECONDS = 0.5

if retry_count > MAX_RETRIES:
    pass
await asyncio.sleep(DEBOUNCE_DELAY_SECONDS)
```

## ツールチェーン

### ruff (リンター & フォーマッター)

```bash
# コードチェック
ruff check .

# 自動修正
ruff check --fix .

# フォーマット
ruff format .
```

### mypy (型チェック)

```bash
# 型チェック実行
mypy .

# 厳格モード
mypy --strict src/
```

### pytest (テスト)

```bash
# すべてのテストを実行
pytest

# カバレッジ付き
pytest --cov=src --cov-report=html

# 特定ファイルをテスト
pytest tests/test_market.py
```

## コード品質チェックリスト

作業完了前の確認事項:
- [ ] コードが読みやすく、適切な命名がされている (snake_case)
- [ ] 関数が小さい (<50行)
- [ ] ファイルが集中している (<800行)
- [ ] 深いネストがない (>4レベル)
- [ ] 適切なエラー処理
- [ ] print文がない (ロギングを使用)
- [ ] ハードコードされた値がない
- [ ] ミューテーションがない (不変パターンを使用)
- [ ] 型ヒントが適切に使用されている
- [ ] すべてのパブリック関数にドキュメントがある
- [ ] テストが書かれている (80%以上のカバレッジ)

**覚えておくこと**: コード品質は交渉の余地がありません。明確で保守性の高いコードが、迅速な開発と自信を持ったリファクタリングを可能にします。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
