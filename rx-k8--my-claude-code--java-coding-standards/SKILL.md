---
name: java-coding-standards
description: Spring BootサービスのためのJavaコーディング規約: 命名規則、イミュータビリティ、Optionalの使用、ストリーム、例外処理、ジェネリクス、プロジェクトレイアウト。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Javaコーディング規約

Spring BootサービスにおけるJava (17+)の読みやすく保守しやすいコードのための規約。

## 中核原則

- 巧妙さよりも明快さを優先する
- デフォルトでイミュータブル; 共有可変状態を最小限にする
- 意味のある例外でフェイルファストする
- 一貫した命名規則とパッケージ構造

## 命名規則

```java
// ✅ クラス/レコード: パスカルケース
public class MarketService {}
public record Money(BigDecimal amount, Currency currency) {}

// ✅ メソッド/フィールド: キャメルケース
private final MarketRepository marketRepository;
public Market findBySlug(String slug) {}

// ✅ 定数: 大文字のスネークケース
private static final int MAX_PAGE_SIZE = 100;
```

## イミュータビリティ

```java
// ✅ レコードとfinalフィールドを優先する
public record MarketDto(Long id, String name, MarketStatus status) {}

public class Market {
  private final Long id;
  private final String name;
  // getterのみ、setterなし
}
```

## Optionalの使用

```java
// ✅ find*メソッドからはOptionalを返す
Optional<Market> market = marketRepository.findBySlug(slug);

// ✅ get()の代わりにmap/flatMapを使用する
return market
    .map(MarketResponse::from)
    .orElseThrow(() -> new EntityNotFoundException("Market not found"));
```

## ストリームのベストプラクティス

```java
// ✅ 変換にはストリームを使用し、パイプラインは短く保つ
List<String> names = markets.stream()
    .map(Market::name)
    .filter(Objects::nonNull)
    .toList();

// ❌ 複雑なネストされたストリームは避ける; 明快さのためにループを優先する
```

## 例外処理

- ドメインエラーには非チェック例外を使用; 技術的例外はコンテキストとともにラップする
- ドメイン固有の例外を作成する (例: `MarketNotFoundException`)
- 広範な `catch (Exception ex)` は、中央で再スローまたはログ記録する場合を除き避ける

```java
throw new MarketNotFoundException(slug);
```

## ジェネリクスと型安全性

- rawタイプを避ける; ジェネリックパラメータを宣言する
- 再利用可能なユーティリティには境界付きジェネリクスを優先する

```java
public <T extends Identifiable> Map<Long, T> indexById(Collection<T> items) { ... }
```

## プロジェクト構造 (Maven/Gradle)

```
src/main/java/com/example/app/
  config/
  controller/
  service/
  repository/
  domain/
  dto/
  util/
src/main/resources/
  application.yml
src/test/java/... (mainをミラー)
```

## フォーマットとスタイル

- 一貫して2または4スペースを使用する (プロジェクト標準)
- ファイルごとに1つのpublicトップレベル型
- メソッドは短く焦点を絞る; ヘルパーを抽出する
- メンバーの順序: 定数、フィールド、コンストラクタ、publicメソッド、protected、private

## 避けるべきコードスメル

- 長いパラメータリスト → DTO/ビルダーを使用する
- 深いネスト → 早期リターンを使用する
- マジックナンバー → 名前付き定数を使用する
- 静的可変状態 → 依存性注入を優先する
- サイレントなcatchブロック → ログ記録して対処するか再スローする

## ロギング

```java
private static final Logger log = LoggerFactory.getLogger(MarketService.class);
log.info("fetch_market slug={}", slug);
log.error("failed_fetch_market slug={}", slug, ex);
```

## Null処理

- やむを得ない場合のみ `@Nullable` を受け入れる; それ以外は `@NonNull` を使用する
- 入力にはBean Validation (`@NotNull`, `@NotBlank`) を使用する

## テストの期待事項

- JUnit 5 + AssertJで流暢なアサーションを行う
- モックにはMockitoを使用; 可能な限り部分モックを避ける
- 決定論的なテストを優先; 隠れたスリープはしない

**覚えておくこと**: コードは意図的で、型付けされ、観察可能なものにする。証明された必要性がない限り、マイクロ最適化よりも保守性を最適化する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
