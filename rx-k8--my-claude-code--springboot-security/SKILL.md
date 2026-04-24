---
name: springboot-security
description: Java Spring Bootサービスにおける認証/認可、検証、CSRF、シークレット、ヘッダー、レート制限、依存関係セキュリティのためのSpring Securityベストプラクティス。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Spring Bootセキュリティレビュー

認証の追加、入力処理、エンドポイント作成、シークレットの扱いの際に使用します。

## 認証

- ステートレスJWTまたは失効リスト付きの不透明トークンを優先する
- セッションには `httpOnly`, `Secure`, `SameSite=Strict` クッキーを使用する
- `OncePerRequestFilter` またはリソースサーバーでトークンを検証する

```java
@Component
public class JwtAuthFilter extends OncePerRequestFilter {
  private final JwtService jwtService;

  public JwtAuthFilter(JwtService jwtService) {
    this.jwtService = jwtService;
  }

  @Override
  protected void doFilterInternal(HttpServletRequest request, HttpServletResponse response,
      FilterChain chain) throws ServletException, IOException {
    String header = request.getHeader(HttpHeaders.AUTHORIZATION);
    if (header != null && header.startsWith("Bearer ")) {
      String token = header.substring(7);
      Authentication auth = jwtService.authenticate(token);
      SecurityContextHolder.getContext().setAuthentication(auth);
    }
    chain.doFilter(request, response);
  }
}
```

## 認可

- メソッドセキュリティを有効にする: `@EnableMethodSecurity`
- `@PreAuthorize("hasRole('ADMIN')")` または `@PreAuthorize("@authz.canEdit(#id)")` を使用する
- デフォルトで拒否; 必要なスコープのみを公開する

## 入力検証

- コントローラーで `@Valid` を使用したBean Validationを使用する
- DTOに制約を適用する: `@NotBlank`, `@Email`, `@Size`, カスタムバリデータ
- レンダリング前にホワイトリストでHTMLをサニタイズする

## SQLインジェクション防止

- Spring Dataリポジトリまたはパラメータ化クエリを使用する
- ネイティブクエリには `:param` バインディングを使用; 文字列を連結しない

## CSRF保護

- ブラウザセッションアプリの場合、CSRFを有効に保つ; フォーム/ヘッダーにトークンを含める
- Bearerトークンを使用する純粋なAPIの場合、CSRFを無効にしてステートレス認証に依存する

```java
http
  .csrf(csrf -> csrf.disable())
  .sessionManagement(sm -> sm.sessionCreationPolicy(SessionCreationPolicy.STATELESS));
```

## シークレット管理

- ソース内にシークレットを置かない; 環境変数またはvaultから読み込む
- `application.yml` を資格情報なしに保つ; プレースホルダーを使用する
- トークンとDB資格情報を定期的にローテーションする

## セキュリティヘッダー

```java
http
  .headers(headers -> headers
    .contentSecurityPolicy(csp -> csp
      .policyDirectives("default-src 'self'"))
    .frameOptions(HeadersConfigurer.FrameOptionsConfig::sameOrigin)
    .xssProtection(Customizer.withDefaults())
    .referrerPolicy(rp -> rp.policy(ReferrerPolicyHeaderWriter.ReferrerPolicy.NO_REFERRER)));
```

## レート制限

- 高コストなエンドポイントにBucket4jまたはゲートウェイレベルの制限を適用する
- バーストをログ記録してアラートする; リトライヒント付きで429を返す

## 依存関係セキュリティ

- CIでOWASP Dependency Check / Snykを実行する
- Spring BootとSpring Securityをサポートされているバージョンに保つ
- 既知のCVEでビルドを失敗させる

## ロギングとPII

- シークレット、トークン、パスワード、完全なPANデータをログ記録しない
- 機密フィールドを編集; 構造化JSONロギングを使用する

## ファイルアップロード

- サイズ、コンテンツタイプ、拡張子を検証する
- Webルート外に保存; 必要に応じてスキャンする

## リリース前チェックリスト

- [ ] 認証トークンが正しく検証され期限切れになっている
- [ ] すべての機密パスに認可ガード
- [ ] すべての入力が検証およびサニタイズされている
- [ ] 文字列連結されたSQLがない
- [ ] アプリタイプに対して正しいCSRF体制
- [ ] シークレットが外部化されている; コミットされていない
- [ ] セキュリティヘッダーが設定されている
- [ ] APIにレート制限
- [ ] 依存関係がスキャンされ最新
- [ ] ログに機密データがない

**覚えておくこと**: デフォルトで拒否し、入力を検証し、最小権限で、まず設定によるセキュリティを確保する。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
