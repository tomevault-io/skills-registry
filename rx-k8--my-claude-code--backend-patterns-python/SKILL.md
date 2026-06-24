---
name: backend-patterns-python
description: FastAPI、SQLAlchemy、Pydantic を使用したバックエンドアーキテクチャパターン、API 設計、データベース最適化、サーバーサイドのベストプラクティス Use when this capability is needed.
metadata:
  author: rx-k8
---

# Python バックエンド開発パターン

スケーラブルなサーバーサイド Python アプリケーションのためのバックエンドアーキテクチャパターンとベストプラクティス。

## API 設計パターン

### FastAPI エンドポイント構造

```python
from fastapi import FastAPI, HTTPException, Depends, Query
from pydantic import BaseModel
from typing import Optional, Literal

app = FastAPI()

# レスポンスモデル
class Market(BaseModel):
    id: str
    name: str
    status: Literal['active', 'resolved', 'closed']
    volume: float

class MarketCreate(BaseModel):
    name: str
    description: str
    end_date: str

# ✅ リソースベースの URL
@app.get('/api/markets', response_model=list[Market])
async def list_markets(
    status: Optional[str] = Query(None),
    limit: int = Query(20, le=100),
    offset: int = Query(0, ge=0)
):
    """マーケット一覧を取得"""
    markets = await market_service.list(status=status, limit=limit, offset=offset)
    return markets

@app.get('/api/markets/{market_id}', response_model=Market)
async def get_market(market_id: str):
    """単一マーケットを取得"""
    market = await market_service.find_by_id(market_id)
    if not market:
        raise HTTPException(status_code=404, detail='Market not found')
    return market

@app.post('/api/markets', response_model=Market, status_code=201)
async def create_market(data: MarketCreate):
    """新しいマーケットを作成"""
    market = await market_service.create(data)
    return market

@app.patch('/api/markets/{market_id}', response_model=Market)
async def update_market(market_id: str, data: dict):
    """マーケットを部分更新"""
    market = await market_service.update(market_id, data)
    if not market:
        raise HTTPException(status_code=404, detail='Market not found')
    return market

@app.delete('/api/markets/{market_id}', status_code=204)
async def delete_market(market_id: str):
    """マーケットを削除"""
    await market_service.delete(market_id)
```

### リポジトリパターン

```python
from abc import ABC, abstractmethod
from typing import Optional, Protocol

# リポジトリインターフェース
class MarketRepository(ABC):
    @abstractmethod
    async def find_all(
        self,
        status: Optional[str] = None,
        limit: int = 20,
        offset: int = 0
    ) -> list[Market]:
        """マーケット一覧を取得"""
        pass

    @abstractmethod
    async def find_by_id(self, market_id: str) -> Optional[Market]:
        """IDでマーケットを検索"""
        pass

    @abstractmethod
    async def create(self, data: MarketCreate) -> Market:
        """新しいマーケットを作成"""
        pass

    @abstractmethod
    async def update(self, market_id: str, data: dict) -> Optional[Market]:
        """マーケットを更新"""
        pass

    @abstractmethod
    async def delete(self, market_id: str) -> None:
        """マーケットを削除"""
        pass

# Supabase 実装
class SupabaseMarketRepository(MarketRepository):
    def __init__(self, supabase_client):
        self.client = supabase_client

    async def find_all(
        self,
        status: Optional[str] = None,
        limit: int = 20,
        offset: int = 0
    ) -> list[Market]:
        query = self.client.table('markets').select('*')

        if status:
            query = query.eq('status', status)

        query = query.limit(limit).offset(offset)
        response = await query.execute()

        return [Market(**row) for row in response.data]

    async def find_by_id(self, market_id: str) -> Optional[Market]:
        response = await self.client.table('markets') \
            .select('*') \
            .eq('id', market_id) \
            .single() \
            .execute()

        if not response.data:
            return None

        return Market(**response.data)

    # その他のメソッド...
```

### サービスレイヤーパターン

```python
from typing import Optional

class MarketService:
    """ビジネスロジックを含むサービスレイヤー"""

    def __init__(self, repository: MarketRepository):
        self.repository = repository

    async def search_markets(
        self,
        query: str,
        limit: int = 10
    ) -> list[Market]:
        """セマンティック検索を使用してマーケットを検索"""
        # ビジネスロジック
        embedding = await self.generate_embedding(query)
        results = await self.vector_search(embedding, limit)

        # 完全なデータを取得
        market_ids = [r['id'] for r in results]
        markets = await self.repository.find_by_ids(market_ids)

        # 類似度でソート
        score_map = {r['id']: r['score'] for r in results}
        markets.sort(key=lambda m: score_map.get(m.id, 0), reverse=True)

        return markets

    async def create_with_validation(
        self,
        data: MarketCreate
    ) -> Market:
        """バリデーション付きでマーケットを作成"""
        # ビジネスルールを検証
        if await self.is_duplicate_name(data.name):
            raise ValueError('Market name already exists')

        # マーケットを作成
        market = await self.repository.create(data)

        # 追加のビジネスロジック（例: イベント発行）
        await self.publish_market_created_event(market)

        return market

    async def is_duplicate_name(self, name: str) -> bool:
        """名前の重複をチェック"""
        # 実装
        pass

    async def vector_search(self, embedding: list[float], limit: int):
        """ベクトル検索の実装"""
        # 実装
        pass
```

### 依存性注入パターン

```python
from fastapi import Depends
from typing import Annotated

# 依存性ファクトリー
def get_supabase_client():
    """Supabase クライアントを取得"""
    from supabase import create_client
    return create_client(SUPABASE_URL, SUPABASE_KEY)

def get_market_repository(
    client = Depends(get_supabase_client)
) -> MarketRepository:
    """マーケットリポジトリを取得"""
    return SupabaseMarketRepository(client)

def get_market_service(
    repository: Annotated[MarketRepository, Depends(get_market_repository)]
) -> MarketService:
    """マーケットサービスを取得"""
    return MarketService(repository)

# エンドポイントでの使用
@app.get('/api/markets')
async def list_markets(
    service: Annotated[MarketService, Depends(get_market_service)],
    limit: int = 20
):
    return await service.list(limit=limit)
```

## データベースパターン

### SQLAlchemy モデル

```python
from sqlalchemy import Column, String, Float, DateTime, Enum as SQLEnum
from sqlalchemy.ext.declarative import declarative_base
from datetime import datetime
import enum

Base = declarative_base()

class MarketStatus(enum.Enum):
    ACTIVE = 'active'
    RESOLVED = 'resolved'
    CLOSED = 'closed'

class MarketModel(Base):
    __tablename__ = 'markets'

    id = Column(String, primary_key=True)
    name = Column(String(200), nullable=False, index=True)
    description = Column(String(2000))
    status = Column(SQLEnum(MarketStatus), default=MarketStatus.ACTIVE)
    volume = Column(Float, default=0.0)
    created_at = Column(DateTime, default=datetime.utcnow)
    updated_at = Column(DateTime, default=datetime.utcnow, onupdate=datetime.utcnow)
```

### クエリ最適化

```python
from sqlalchemy import select
from sqlalchemy.orm import selectinload

# ✅ 良い例: 必要なカラムのみを選択
async with async_session() as session:
    result = await session.execute(
        select(MarketModel.id, MarketModel.name, MarketModel.status)
        .where(MarketModel.status == 'active')
        .order_by(MarketModel.volume.desc())
        .limit(10)
    )
    markets = result.all()

# ❌ 悪い例: すべてを選択
async with async_session() as session:
    result = await session.execute(select(MarketModel))
    markets = result.scalars().all()
```

### N+1 クエリ問題の防止

```python
from sqlalchemy.orm import selectinload

# ❌ 悪い例: N+1 クエリ問題
markets = await session.execute(select(Market))
for market in markets.scalars():
    # 各マーケットごとに追加クエリが発生
    creator = await session.execute(
        select(User).where(User.id == market.creator_id)
    )

# ✅ 良い例: Eager loading
markets = await session.execute(
    select(Market)
    .options(selectinload(Market.creator))
)
# 1つのクエリで creators を取得
```

### トランザクションパターン

```python
from sqlalchemy.ext.asyncio import AsyncSession

async def create_market_with_position(
    session: AsyncSession,
    market_data: MarketCreate,
    position_data: PositionCreate
):
    """トランザクション内で複数の操作を実行"""
    async with session.begin():
        # マーケットを作成
        market = MarketModel(**market_data.model_dump())
        session.add(market)
        await session.flush()  # ID を取得

        # ポジションを作成
        position = PositionModel(
            **position_data.model_dump(),
            market_id=market.id
        )
        session.add(position)

        # 例外が発生した場合、自動的にロールバック
        # それ以外の場合、コミット

    return market, position
```

## キャッシング戦略

### Redis キャッシングレイヤー

```python
import redis.asyncio as redis
from functools import lru_cache
import json

@lru_cache(maxsize=1)
def get_redis_client():
    """Redis クライアントのシングルトン"""
    return redis.from_url('redis://localhost:6379')

class CachedMarketRepository(MarketRepository):
    """キャッシング付きリポジトリ"""

    def __init__(self, base_repo: MarketRepository):
        self.base_repo = base_repo
        self.redis = get_redis_client()
        self.ttl = 300  # 5分

    async def find_by_id(self, market_id: str) -> Optional[Market]:
        """キャッシュをチェックしてから DB にフォールバック"""
        # キャッシュをチェック
        cached = await self.redis.get(f'market:{market_id}')
        if cached:
            return Market.parse_raw(cached)

        # DB から取得
        market = await self.base_repo.find_by_id(market_id)
        if market:
            # キャッシュに保存
            await self.redis.setex(
                f'market:{market_id}',
                self.ttl,
                market.json()
            )

        return market

    async def invalidate(self, market_id: str):
        """キャッシュを無効化"""
        await self.redis.delete(f'market:{market_id}')
```

## エラーハンドリングパターン

### カスタム例外

```python
class MarketNotFoundError(Exception):
    """マーケットが見つからない場合"""
    pass

class MarketValidationError(Exception):
    """マーケットのバリデーションエラー"""
    pass

# FastAPI 例外ハンドラー
from fastapi import Request
from fastapi.responses import JSONResponse

@app.exception_handler(MarketNotFoundError)
async def market_not_found_handler(request: Request, exc: MarketNotFoundError):
    return JSONResponse(
        status_code=404,
        content={'error': 'Market not found', 'detail': str(exc)}
    )

@app.exception_handler(MarketValidationError)
async def validation_error_handler(request: Request, exc: MarketValidationError):
    return JSONResponse(
        status_code=400,
        content={'error': 'Validation failed', 'detail': str(exc)}
    )
```

### エラーレスポンスの標準化

```python
from pydantic import BaseModel
from typing import Optional

class ErrorResponse(BaseModel):
    error: str
    detail: Optional[str] = None
    code: Optional[str] = None

async def handle_error(error: Exception) -> JSONResponse:
    """統一されたエラーレスポンス"""
    if isinstance(error, MarketNotFoundError):
        return JSONResponse(
            status_code=404,
            content=ErrorResponse(
                error='Not found',
                detail=str(error),
                code='MARKET_NOT_FOUND'
            ).model_dump()
        )
    elif isinstance(error, ValueError):
        return JSONResponse(
            status_code=400,
            content=ErrorResponse(
                error='Bad request',
                detail=str(error),
                code='INVALID_INPUT'
            ).model_dump()
        )
    else:
        return JSONResponse(
            status_code=500,
            content=ErrorResponse(
                error='Internal server error',
                code='INTERNAL_ERROR'
            ).model_dump()
        )
```

## 認証と認可パターン

### JWT 認証ミドルウェア

```python
from fastapi import Header, HTTPException
from typing import Annotated, Optional
import jwt

async def get_current_user(
    authorization: Annotated[Optional[str], Header()] = None
) -> User:
    """認証トークンからユーザーを取得"""
    if not authorization:
        raise HTTPException(status_code=401, detail='Missing authorization header')

    try:
        # Bearer トークンを抽出
        token = authorization.replace('Bearer ', '')

        # トークンを検証
        payload = jwt.decode(token, SECRET_KEY, algorithms=['HS256'])

        # ユーザーを取得
        user_id = payload.get('sub')
        user = await user_service.find_by_id(user_id)

        if not user:
            raise HTTPException(status_code=401, detail='User not found')

        return user

    except jwt.InvalidTokenError:
        raise HTTPException(status_code=401, detail='Invalid token')

# 保護されたエンドポイント
@app.get('/api/protected')
async def protected_route(
    current_user: Annotated[User, Depends(get_current_user)]
):
    return {'user': current_user.email}
```

### ロールベースアクセス制御 (RBAC)

```python
from enum import Enum

class UserRole(str, Enum):
    ADMIN = 'admin'
    CREATOR = 'creator'
    USER = 'user'

def require_role(required_role: UserRole):
    """ロール要件を強制するデコレーター"""
    async def check_role(
        current_user: Annotated[User, Depends(get_current_user)]
    ):
        if current_user.role != required_role:
            raise HTTPException(status_code=403, detail='Insufficient permissions')
        return current_user

    return check_role

# 使用例
@app.post('/api/markets')
async def create_market(
    data: MarketCreate,
    current_user: Annotated[User, Depends(require_role(UserRole.CREATOR))]
):
    return await market_service.create(data)
```

## レート制限パターン

```python
from fastapi import Request
from slowapi import Limiter
from slowapi.util import get_remote_address

limiter = Limiter(key_func=get_remote_address)

@app.get('/api/markets')
@limiter.limit('100/hour')
async def list_markets(request: Request):
    """1時間あたり100リクエストに制限"""
    return await market_service.list()

@app.post('/api/markets')
@limiter.limit('10/hour')
async def create_market(request: Request, data: MarketCreate):
    """1時間あたり10リクエストに制限"""
    return await market_service.create(data)
```

## バックグラウンドタスクパターン

```python
from fastapi import BackgroundTasks

async def send_email_notification(user_email: str, market_name: str):
    """バックグラウンドでメールを送信"""
    # メール送信ロジック
    pass

@app.post('/api/markets')
async def create_market(
    data: MarketCreate,
    background_tasks: BackgroundTasks
):
    """マーケットを作成してバックグラウンドで通知を送信"""
    market = await market_service.create(data)

    # バックグラウンドタスクを追加
    background_tasks.add_task(
        send_email_notification,
        user_email='admin@example.com',
        market_name=market.name
    )

    return market
```

## ログとモニタリング

```python
import logging
from fastapi import Request
import time

logger = logging.getLogger(__name__)

@app.middleware('http')
async def log_requests(request: Request, call_next):
    """リクエストをログに記録"""
    start_time = time.time()

    # リクエストをログ
    logger.info(f'{request.method} {request.url.path}')

    # レスポンスを取得
    response = await call_next(request)

    # 処理時間をログ
    duration = time.time() - start_time
    logger.info(
        f'{request.method} {request.url.path} '
        f'completed in {duration:.2f}s with status {response.status_code}'
    )

    return response
```

## ベストプラクティス

1. **レイヤーの分離** - リポジトリ、サービス、コントローラーを分離
2. **依存性注入** - テスト可能性と柔軟性のため
3. **型ヒント** - すべての関数とメソッドに型ヒントを使用
4. **Pydantic バリデーション** - 入力を自動的に検証
5. **非同期/待機** - I/O バウンド操作にはasync/await を使用
6. **エラーハンドリング** - カスタム例外と標準化されたレスポンス
7. **キャッシング** - 頻繁にアクセスされるデータをキャッシュ
8. **ロギング** - すべてのリクエストとエラーをログ
9. **レート制限** - API を乱用から保護
10. **セキュリティ** - 認証、認可、入力検証

---

**覚えておくこと**: スケーラブルなバックエンドアーキテクチャは、明確な責任分離、堅牢なエラーハンドリング、パフォーマンス最適化に依存しています。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
