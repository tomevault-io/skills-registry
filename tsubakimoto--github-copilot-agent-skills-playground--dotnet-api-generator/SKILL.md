---
name: dotnet-api-generator
description: C# 14 と ASP.NET Core 10 を使用し、Native AOT を前提とした高パフォーマンスな Web API ボイラープレートを生成します。 Use when this capability is needed.
metadata:
  author: tsubakimoto
---

# C# 14 / ASP.NET Core 10 API 構築スキル

このスキルは、モダンな .NET 10 の機能を最大限に活用し、メンテナンス性とパフォーマンスに優れた API プロジェクトの初期構造を生成します。

## 1. 適用する標準スタック
- **Language:** C# 14 (Explicit Extensions, Interceptors, Record improvements を活用)
- **Framework:** ASP.NET Core 10 (Minimal APIs 必須)
- **Deployment:** Native AOT 互換をデフォルトとする
- **Architecture:** Vertical Slice Architecture (機能単位のディレクトリ構成)

## 2. 推奨ディレクトリ構造
エージェントは以下の構造に従ってファイルを生成してください。

```text
MySolution/
├── MySolution.sln                        # ソリューションファイル
├── .gitignore
├── .gitattributes
├── .editorconfig
├── README.md
├── src/                                  # ソースコードディレクトリ
│   └── MySolution.Api/                   # Web API プロジェクト
│       ├── Endpoints/                    # エンドポイント定義
│       │   ├── WeatherEndpoints.cs
│       │   └── ProductEndpoints.cs
│       ├── Models/                       # データモデル、DTOs
│       │   ├── Product.cs
│       │   ├── CreateProductRequest.cs
│       │   ├── UpdateProductRequest.cs
│       │   └── WeatherForecast.cs
│       ├── Services/                     # ビジネスロジック
│       │   ├── IProductService.cs
│       │   └── ProductService.cs
│       ├── Data/                         # データアクセス
│       │   ├── ApplicationDbContext.cs
│       │   └── Repositories/
│       │       ├── IProductRepository.cs
│       │       └── ProductRepository.cs
│       ├── Filters/                      # エンドポイントフィルター
│       │   └── ValidationFilter.cs
│       ├── Extensions/                   # 拡張メソッド
│       │   ├── ServiceCollectionExtensions.cs
│       │   └── WebApplicationExtensions.cs
│       ├── Middleware/                   # カスタムミドルウェア
│       │   └── ExceptionHandlingMiddleware.cs
│       ├── Properties/
│       │   └── launchSettings.json
│       ├── wwwroot/                      # 静的ファイル (オプション)
│       ├── appsettings.json
│       ├── appsettings.Development.json  # 開発環境用設定
│       ├── appsettings.Staging.json      # ステージング環境用設定
│       ├── appsettings.Production.json   # 本番環境用設定
│       ├── Program.cs                    # エントリーポイント (エンドポイント登録含む)
│       ├── GlobalUsings.cs               # グローバル using
│       └── MySolution.Api.csproj
│
└── tests/                                # テストディレクトリ
    └── MySolution.Api.Tests/             # 単体テストプロジェクト
        ├── Endpoints/                    # エンドポイントのテスト
        │   └── ProductEndpointsTests.cs
        ├── Services/                     # サービスのテスト
        │   └── ProductServiceTests.cs
        ├── Repositories/                 # リポジトリのテスト
        │   └── ProductRepositoryTests.cs
        ├── Fixtures/                     # テストフィクスチャ
        │   └── TestDatabaseFixture.cs
        ├── Helpers/                      # テストヘルパー
        │   └── MockDataHelper.cs
        ├── GlobalUsings.cs               # グローバル using
        └── MySolution.Api.Tests.csproj
```

## 3. 実装ルール (Instructions)
- Minimal APIs: `Controller` クラスは使用せず、`MapGroup` を使った Minimal API を構築してください。
- C# 14 Features: 新しい `params` コレクションの柔軟な利用や、改善された `record` 型を DTO に使用してください。
- Native AOT 互換性: リフレクションを避け、Source Generators を活用する構成にしてください（`JsonSerializerContext` の定義など）。
- Dependency Injection: `AddService` ではなく、インターフェースを介さない具象クラスの登録を推奨します（パフォーマンス向上）。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/tsubakimoto) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
