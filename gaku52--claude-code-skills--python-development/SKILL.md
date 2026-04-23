---
name: python-development
description: Python開発ガイド。FastAPI、Django、Flask、型ヒント、非同期処理、データ処理、パフォーマンス最適化など、Pythonアプリケーション開発のベストプラクティス。 Use when this capability is needed.
metadata:
  author: gaku52
---

# Python Development Skill

## 📋 目次

1. [概要](#概要)
2. [学習の進め方](#学習の進め方) 🆕 **NEW**
3. [いつ使うか](#いつ使うか)
4. [ガイド一覧](#ガイド一覧)
5. [テンプレート](#テンプレート)
6. [クイックスタート](#クイックスタート)
7. [FastAPI](#fastapi)
8. [Django](#django)
9. [型ヒント](#型ヒント)
10. [非同期処理](#非同期処理)
11. [パフォーマンス最適化](#パフォーマンス最適化)
12. [Agent連携](#agent連携)

---

## 概要

このSkillは、Python開発の全領域をカバーします：

- **FastAPI** - モダンAPI フレームワーク
- **Django** - フルスタックWebフレームワーク
- **型ヒント** - 型安全性向上
- **非同期処理** - async/await
- **データ処理** - Pandas, NumPy
- **パフォーマンス最適化** - プロファイリング、最適化手法
- **テスト** - Pytest
- **ツール** - Ruff, mypy, pre-commit

---

## 学習の進め方

### 🆕 完全初心者の方

**プログラミングが初めての方、Pythonを初めて学ぶ方は、まず基礎ガイドから始めてください。**

#### [📘 Python基礎ガイド（6本のガイド）](./guides/basics/)

1. **[Python入門](./guides/basics/01-python-intro.md)**
   - Pythonとは何か、なぜ学ぶのか
   - Pythonのインストールと環境構築
   - 最初のプログラムとREPL

2. **[基本文法](./guides/basics/02-basic-syntax.md)**
   - 変数と代入
   - データ型（int、float、str、bool）
   - 演算子と文字列操作

3. **[制御フロー](./guides/basics/03-control-flow.md)**
   - if/elif/else（条件分岐）
   - for/while（ループ）
   - break、continue、pass

4. **[関数](./guides/basics/04-functions.md)**
   - 関数の定義と呼び出し
   - 引数と戻り値
   - スコープとラムダ式

5. **[データ構造](./guides/basics/05-data-structures.md)**
   - リスト、タプル、辞書、セット
   - 内包表記
   - データ構造の使い分け

6. **[モジュールとパッケージ](./guides/basics/06-modules-packages.md)**
   - モジュールのインポート
   - 標準ライブラリの活用
   - pipとパッケージ管理、仮想環境

**学習時間の目安：** 各ガイド1〜2時間、合計6〜12時間

---

### 🎯 初心者〜中級者の方

基礎を理解している方は、以下の順番で学習を進めてください。

1. **Webフレームワークの選択**
   - **FastAPI**：モダンなAPI開発、非同期処理、自動ドキュメント生成
   - **Django**：フルスタックWebアプリ、管理画面、ORM

2. **型ヒント**：コードの保守性と安全性を向上

3. **非同期処理**：async/awaitでパフォーマンス向上

4. **データ処理**：pandas、NumPyでデータ分析

---

## 📚 公式ドキュメント・参考リソース

**このガイドで学べること**: フレームワークパターン、型安全設計、パフォーマンス最適化、データ処理パターン
**公式で確認すべきこと**: 最新API、Python 3.13の新機能、ライブラリアップデート、セキュリティ情報

### 主要な公式ドキュメント

- **[Python Documentation](https://docs.python.org/3/)** - Python公式ドキュメント
  - [Tutorial](https://docs.python.org/3/tutorial/) - 公式チュートリアル
  - [Library Reference](https://docs.python.org/3/library/) - 標準ライブラリ完全リファレンス
  - [Language Reference](https://docs.python.org/3/reference/) - 言語仕様
  - [What's New](https://docs.python.org/3/whatsnew/) - 新機能一覧

- **[FastAPI](https://fastapi.tiangolo.com/)** - FastAPI公式ドキュメント
  - [Tutorial](https://fastapi.tiangolo.com/tutorial/) - ステップバイステップガイド
  - [Advanced User Guide](https://fastapi.tiangolo.com/advanced/) - 高度な機能
  - [Deployment](https://fastapi.tiangolo.com/deployment/) - プロダクションデプロイ

- **[Django](https://docs.djangoproject.com/)** - Django公式ドキュメント
  - [Getting Started](https://docs.djangoproject.com/en/stable/intro/) - スタートガイド
  - [Topics](https://docs.djangoproject.com/en/stable/topics/) - 詳細トピック
  - [REST Framework](https://www.django-rest-framework.org/) - DRF公式ドキュメント

- **[pandas](https://pandas.pydata.org/docs/)** - pandas公式ドキュメント
  - データ分析・操作の標準ライブラリ

- **[NumPy](https://numpy.org/doc/)** - NumPy公式ドキュメント
  - 数値計算の基礎ライブラリ

### 開発ツール

- **[mypy](https://mypy.readthedocs.io/)** - 静的型チェッカー
- **[Ruff](https://docs.astral.sh/ruff/)** - 高速リンター・フォーマッター
- **[pytest](https://docs.pytest.org/)** - テストフレームワーク

### 関連リソース

- **[Python Enhancement Proposals (PEPs)](https://peps.python.org/)** - Python仕様提案
- **[Real Python](https://realpython.com/)** - チュートリアル集
- **[PyPI](https://pypi.org/)** - Python Package Index

---

### スキルステータス

**🟢 High (100% completion, 4/4 guides)**
- ✅ 包括的なガイド 4本（105,000+ 文字）
- ✅ プロジェクトテンプレート完備
- ✅ ベストプラクティスチェックリスト
- ✅ 実践的なコード例

---

## いつ使うか

### 🎯 必須のタイミング

- [ ] 新規Python プロジェクト作成時
- [ ] API開発時（FastAPI）
- [ ] Webアプリ開発時（Django）
- [ ] データ処理スクリプト作成時
- [ ] パフォーマンス問題の解決時

---

## ガイド一覧

### 📚 詳細ガイド

1. **[Python Best Practices](./guides/01-python-best-practices.md)** (8,790 chars)
   - 型ヒント、コード品質、プロジェクト構成
   - 仮想環境管理、テスト、パフォーマンス基礎

2. **[FastAPI & Django 開発ガイド](./guides/02-fastapi-django.md)** (32,079 chars)
   - FastAPI プロジェクトセットアップ
   - SQLAlchemy、認証・認可
   - Django REST Framework
   - パフォーマンス最適化

3. **[データ処理・自動化ガイド](./guides/03-data-processing.md)** (24,607 chars)
   - CSV/JSON/Excel 処理
   - pandas/NumPy データ分析
   - Web スクレイピング
   - 自動化、並列処理

4. **[パフォーマンス最適化ガイド](./guides/04-performance-optimization.md)** (40,000+ chars)
   - プロファイリング（cProfile, line_profiler, memory_profiler）
   - データ構造の最適化
   - NumPy/Pandas 最適化
   - 並列処理・非同期処理
   - キャッシング戦略
   - Cython、JIT コンパイル
   - 実践的な最適化事例

### 📋 チェックリスト

- **[CHECKLIST.md](./CHECKLIST.md)** - Python 開発ベストプラクティスチェックリスト

---

## テンプレート

すぐに使えるプロジェクトテンプレートを提供：

### プロジェクト設定
- **[pyproject.toml](./templates/pyproject.toml)** - モダンな依存関係管理
- **[.gitignore](./templates/.gitignore)** - Python プロジェクト用
- **[.env.example](./templates/.env.example)** - 環境変数テンプレート

### コード品質
- **[.pre-commit-config.yaml](./templates/.pre-commit-config.yaml)** - Pre-commit フック設定
- **[tox.ini](./templates/tox.ini)** - 複数環境でのテスト自動化

### タスク自動化
- **[Makefile](./templates/Makefile)** - タスク自動化

### Docker
- **[Dockerfile](./templates/Dockerfile)** - マルチステージビルド
- **[docker-compose.yml](./templates/docker-compose.yml)** - 開発環境構築

---

## クイックスタート

### 新規プロジェクト作成

```bash
# プロジェクトディレクトリ作成
mkdir my-project && cd my-project

# テンプレートをコピー
cp /path/to/templates/pyproject.toml .
cp /path/to/templates/.gitignore .
cp /path/to/templates/.pre-commit-config.yaml .
cp /path/to/templates/Makefile .

# プロジェクト構造を作成
mkdir -p src/my_project tests

# 開発環境セットアップ
make dev

# テスト実行
make test
```

### プロジェクト構造（推奨）

```
my-project/
├── src/
│   └── my_project/
│       ├── __init__.py
│       ├── main.py
│       ├── api/
│       ├── models/
│       ├── schemas/
│       └── services/
├── tests/
│   ├── __init__.py
│   └── test_main.py
├── pyproject.toml
├── .gitignore
├── .pre-commit-config.yaml
├── Makefile
└── README.md
```

---

## FastAPI

### プロジェクトセットアップ

```bash
# 仮想環境作成
python -m venv venv
source venv/bin/activate  # Windowsの場合: venv\Scripts\activate

# パッケージインストール
pip install fastapi uvicorn sqlalchemy pydantic
```

### 基本的なAPI

```python
# main.py
from fastapi import FastAPI
from pydantic import BaseModel

app = FastAPI()

class Item(BaseModel):
    name: str
    price: float
    description: str | None = None

@app.get("/")
async def root():
    return {"message": "Hello World"}

@app.get("/items/{item_id}")
async def read_item(item_id: int, q: str | None = None):
    return {"item_id": item_id, "q": q}

@app.post("/items/")
async def create_item(item: Item):
    return item

# 起動: uvicorn main:app --reload
```

### データベース統合（SQLAlchemy）

```python
# database.py
from sqlalchemy import create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

SQLALCHEMY_DATABASE_URL = "sqlite:///./app.db"

engine = create_engine(SQLALCHEMY_DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

Base = declarative_base()

# models.py
from sqlalchemy import Column, Integer, String
from database import Base

class User(Base):
    __tablename__ = "users"

    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True)
    email = Column(String, unique=True, index=True)

# schemas.py
from pydantic import BaseModel, EmailStr

class UserBase(BaseModel):
    name: str
    email: EmailStr

class UserCreate(UserBase):
    password: str

class UserResponse(UserBase):
    id: int

    class Config:
        from_attributes = True

# crud.py
from sqlalchemy.orm import Session
import models, schemas

def get_user(db: Session, user_id: int):
    return db.query(models.User).filter(models.User.id == user_id).first()

def get_users(db: Session, skip: int = 0, limit: int = 100):
    return db.query(models.User).offset(skip).limit(limit).all()

def create_user(db: Session, user: schemas.UserCreate):
    db_user = models.User(**user.dict())
    db.add(db_user)
    db.commit()
    db.refresh(db_user)
    return db_user

# main.py
from fastapi import FastAPI, Depends
from sqlalchemy.orm import Session
import crud, models, schemas
from database import SessionLocal, engine

models.Base.metadata.create_all(bind=engine)

app = FastAPI()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.get("/users/", response_model=list[schemas.UserResponse])
async def read_users(skip: int = 0, limit: int = 100, db: Session = Depends(get_db)):
    users = crud.get_users(db, skip=skip, limit=limit)
    return users

@app.post("/users/", response_model=schemas.UserResponse)
async def create_user(user: schemas.UserCreate, db: Session = Depends(get_db)):
    return crud.create_user(db=db, user=user)
```

### バリデーション

```python
from pydantic import BaseModel, validator, EmailStr, Field

class User(BaseModel):
    name: str = Field(..., min_length=1, max_length=100)
    email: EmailStr
    age: int = Field(..., ge=0, le=120)

    @validator('name')
    def name_must_not_be_empty(cls, v):
        if not v.strip():
            raise ValueError('Name cannot be empty')
        return v

    @validator('age')
    def age_must_be_adult(cls, v):
        if v < 18:
            raise ValueError('Must be 18 or older')
        return v
```

### 認証（JWT）

```python
from fastapi import Depends, HTTPException, status
from fastapi.security import OAuth2PasswordBearer, OAuth2PasswordRequestForm
from jose import JWTError, jwt
from passlib.context import CryptContext
from datetime import datetime, timedelta

SECRET_KEY = "your-secret-key"
ALGORITHM = "HS256"
ACCESS_TOKEN_EXPIRE_MINUTES = 30

pwd_context = CryptContext(schemes=["bcrypt"], deprecated="auto")
oauth2_scheme = OAuth2PasswordBearer(tokenUrl="token")

def verify_password(plain_password, hashed_password):
    return pwd_context.verify(plain_password, hashed_password)

def get_password_hash(password):
    return pwd_context.hash(password)

def create_access_token(data: dict):
    to_encode = data.copy()
    expire = datetime.utcnow() + timedelta(minutes=ACCESS_TOKEN_EXPIRE_MINUTES)
    to_encode.update({"exp": expire})
    return jwt.encode(to_encode, SECRET_KEY, algorithm=ALGORITHM)

async def get_current_user(token: str = Depends(oauth2_scheme)):
    credentials_exception = HTTPException(
        status_code=status.HTTP_401_UNAUTHORIZED,
        detail="Could not validate credentials",
    )
    try:
        payload = jwt.decode(token, SECRET_KEY, algorithms=[ALGORITHM])
        username: str = payload.get("sub")
        if username is None:
            raise credentials_exception
    except JWTError:
        raise credentials_exception

    # ユーザー取得処理
    return username

@app.post("/token")
async def login(form_data: OAuth2PasswordRequestForm = Depends()):
    # ユーザー認証処理
    access_token = create_access_token(data={"sub": form_data.username})
    return {"access_token": access_token, "token_type": "bearer"}

@app.get("/users/me")
async def read_users_me(current_user: str = Depends(get_current_user)):
    return {"username": current_user}
```

---

## Django

### プロジェクト作成

```bash
# インストール
pip install django

# プロジェクト作成
django-admin startproject myproject
cd myproject

# アプリ作成
python manage.py startapp users

# マイグレーション
python manage.py makemigrations
python manage.py migrate

# スーパーユーザー作成
python manage.py createsuperuser

# サーバー起動
python manage.py runserver
```

### モデル

```python
# users/models.py
from django.db import models

class User(models.Model):
    name = models.CharField(max_length=100)
    email = models.EmailField(unique=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)

    def __str__(self):
        return self.name

class Post(models.Model):
    title = models.CharField(max_length=200)
    content = models.TextField()
    author = models.ForeignKey(User, on_delete=models.CASCADE, related_name='posts')
    created_at = models.DateTimeField(auto_now_add=True)

    class Meta:
        ordering = ['-created_at']

    def __str__(self):
        return self.title
```

### ビュー

```python
# users/views.py
from django.shortcuts import render, get_object_or_404
from django.http import JsonResponse
from .models import User

def user_list(request):
    users = User.objects.all()
    return JsonResponse({'users': list(users.values())})

def user_detail(request, pk):
    user = get_object_or_404(User, pk=pk)
    return JsonResponse({
        'id': user.id,
        'name': user.name,
        'email': user.email
    })
```

### Django REST Framework

```bash
pip install djangorestframework
```

```python
# users/serializers.py
from rest_framework import serializers
from .models import User

class UserSerializer(serializers.ModelSerializer):
    class Meta:
        model = User
        fields = ['id', 'name', 'email', 'created_at']

# users/views.py
from rest_framework import viewsets
from .models import User
from .serializers import UserSerializer

class UserViewSet(viewsets.ModelViewSet):
    queryset = User.objects.all()
    serializer_class = UserSerializer

# urls.py
from rest_framework.routers import DefaultRouter
from users.views import UserViewSet

router = DefaultRouter()
router.register(r'users', UserViewSet)

urlpatterns = router.urls
```

---

## 型ヒント

### 基本的な型ヒント

```python
# 基本型
def greet(name: str) -> str:
    return f"Hello, {name}"

# リスト
def process_numbers(numbers: list[int]) -> list[int]:
    return [n * 2 for n in numbers]

# 辞書
def get_user_info(user_id: int) -> dict[str, str]:
    return {"id": str(user_id), "name": "John"}

# Optional
from typing import Optional

def find_user(user_id: int) -> Optional[dict]:
    # ユーザーが見つからない場合はNone
    return None

# Union（Python 3.10+は | 記法）
def process_value(value: int | str) -> str:
    return str(value)
```

### 高度な型ヒント

```python
from typing import TypedDict, Callable

# TypedDict
class UserDict(TypedDict):
    id: int
    name: str
    email: str

def create_user() -> UserDict:
    return {"id": 1, "name": "John", "email": "john@example.com"}

# Callable
def apply_function(func: Callable[[int], int], value: int) -> int:
    return func(value)

# ジェネリクス
from typing import TypeVar, Generic

T = TypeVar('T')

class Stack(Generic[T]):
    def __init__(self) -> None:
        self.items: list[T] = []

    def push(self, item: T) -> None:
        self.items.append(item)

    def pop(self) -> T:
        return self.items.pop()

stack: Stack[int] = Stack()
stack.push(1)
```

---

## 非同期処理

### async/await

```python
import asyncio
import aiohttp

# 非同期関数
async def fetch_data(url: str) -> dict:
    async with aiohttp.ClientSession() as session:
        async with session.get(url) as response:
            return await response.json()

# 並列実行
async def fetch_all(urls: list[str]) -> list[dict]:
    tasks = [fetch_data(url) for url in urls]
    return await asyncio.gather(*tasks)

# 実行
asyncio.run(fetch_all(["https://api.example.com/1", "https://api.example.com/2"]))
```

### FastAPIでの非同期

```python
from fastapi import FastAPI
import httpx

app = FastAPI()

@app.get("/users/{user_id}")
async def get_user(user_id: int):
    async with httpx.AsyncClient() as client:
        response = await client.get(f"https://api.example.com/users/{user_id}")
        return response.json()
```

---

## パフォーマンス最適化

### プロファイリング

```python
import cProfile

# 関数をプロファイリング
profiler = cProfile.Profile()
profiler.enable()

# 重い処理
result = expensive_function()

profiler.disable()
profiler.print_stats(sort='cumulative')
```

### データ構造の選択

```python
# ❌ 遅い: リストで検索
if item in my_list:  # O(n)
    pass

# ✅ 速い: セットで検索
if item in my_set:  # O(1)
    pass
```

### キャッシング

```python
from functools import lru_cache

@lru_cache(maxsize=128)
def fibonacci(n: int) -> int:
    if n < 2:
        return n
    return fibonacci(n-1) + fibonacci(n-2)
```

詳細は **[パフォーマンス最適化ガイド](./guides/04-performance-optimization.md)** を参照。

---

## Agent連携

### 📖 Agentへの指示例

**FastAPI CRUD作成**
```
FastAPIで/api/postsのCRUD APIを作成してください。
Pydantic BaseModelでバリデーションを含めてください。
```

**Django モデル作成**
```
Djangoで以下のモデルを作成してください：
- User（name, email）
- Post（title, content, author）
マイグレーションファイルも生成してください。
```

**パフォーマンス最適化**
```
このPython関数をプロファイリングして、
ボトルネックを特定し、最適化してください。
```

---

## まとめ

### Pythonのベストプラクティス

1. **型ヒント** - 型安全性向上
2. **FastAPI** - モダンAPI開発
3. **非同期処理** - パフォーマンス向上
4. **Pydantic** - データバリデーション
5. **テスト** - pytest でテスト駆動開発
6. **Linting** - Ruff で高速なコード品質チェック
7. **プロファイリング** - cProfile, line_profiler で最適化

### 開発フロー

1. プロジェクトセットアップ（テンプレート使用）
2. 型ヒント付きでコード作成
3. テスト作成（TDD）
4. Lint & 型チェック
5. プロファイリング & 最適化
6. ドキュメント作成
7. CI/CD でデプロイ

---

_Last updated: 2025-01-02_

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/gaku52) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-13 -->
