---
name: springboot-tdd
description: JUnit 5、Mockito、MockMvc、Testcontainers、JaCoCoを使用したSpring Bootのためのテスト駆動開発。機能追加、バグ修正、リファクタリング時に使用。 Use when this capability is needed.
metadata:
  author: rx-k8
---

# Spring Boot TDDワークフロー

Spring Bootサービスのための80%以上のカバレッジ (ユニット + 統合) を含むTDDガイダンス。

## いつ使用するか

- 新機能またはエンドポイント
- バグ修正またはリファクタリング
- データアクセスロジックまたはセキュリティルールの追加

## ワークフロー

1) まずテストを書く (失敗するはず)
2) パスするための最小限のコードを実装する
3) テストをグリーンに保ったままリファクタリングする
4) カバレッジを強制する (JaCoCo)

## ユニットテスト (JUnit 5 + Mockito)

```java
@ExtendWith(MockitoExtension.class)
class MarketServiceTest {
  @Mock MarketRepository repo;
  @InjectMocks MarketService service;

  @Test
  void createsMarket() {
    CreateMarketRequest req = new CreateMarketRequest("name", "desc", Instant.now(), List.of("cat"));
    when(repo.save(any())).thenAnswer(inv -> inv.getArgument(0));

    Market result = service.create(req);

    assertThat(result.name()).isEqualTo("name");
    verify(repo).save(any());
  }
}
```

パターン:
- Arrange-Act-Assert
- 部分モックを避ける; 明示的なスタビングを優先する
- バリアントには `@ParameterizedTest` を使用する

## Web層テスト (MockMvc)

```java
@WebMvcTest(MarketController.class)
class MarketControllerTest {
  @Autowired MockMvc mockMvc;
  @MockBean MarketService marketService;

  @Test
  void returnsMarkets() throws Exception {
    when(marketService.list(any())).thenReturn(Page.empty());

    mockMvc.perform(get("/api/markets"))
        .andExpect(status().isOk())
        .andExpect(jsonPath("$.content").isArray());
  }
}
```

## 統合テスト (SpringBootTest)

```java
@SpringBootTest
@AutoConfigureMockMvc
@ActiveProfiles("test")
class MarketIntegrationTest {
  @Autowired MockMvc mockMvc;

  @Test
  void createsMarket() throws Exception {
    mockMvc.perform(post("/api/markets")
        .contentType(MediaType.APPLICATION_JSON)
        .content("""
          {"name":"Test","description":"Desc","endDate":"2030-01-01T00:00:00Z","categories":["general"]}
        """))
      .andExpect(status().isCreated());
  }
}
```

## 永続化テスト (DataJpaTest)

```java
@DataJpaTest
@AutoConfigureTestDatabase(replace = AutoConfigureTestDatabase.Replace.NONE)
@Import(TestContainersConfig.class)
class MarketRepositoryTest {
  @Autowired MarketRepository repo;

  @Test
  void savesAndFinds() {
    MarketEntity entity = new MarketEntity();
    entity.setName("Test");
    repo.save(entity);

    Optional<MarketEntity> found = repo.findByName("Test");
    assertThat(found).isPresent();
  }
}
```

## Testcontainers

- 本番環境をミラーするためにPostgres/Redisの再利用可能なコンテナを使用する
- `@DynamicPropertySource` 経由で配線してJDBC URLをSpringコンテキストに注入する

## カバレッジ (JaCoCo)

Mavenスニペット:
```xml
<plugin>
  <groupId>org.jacoco</groupId>
  <artifactId>jacoco-maven-plugin</artifactId>
  <version>0.8.14</version>
  <executions>
    <execution>
      <goals><goal>prepare-agent</goal></goals>
    </execution>
    <execution>
      <id>report</id>
      <phase>verify</phase>
      <goals><goal>report</goal></goals>
    </execution>
  </executions>
</plugin>
```

## アサーション

- 可読性のためにAssertJ (`assertThat`) を優先する
- JSONレスポンスには `jsonPath` を使用する
- 例外には: `assertThatThrownBy(...)`

## テストデータビルダー

```java
class MarketBuilder {
  private String name = "Test";
  MarketBuilder withName(String name) { this.name = name; return this; }
  Market build() { return new Market(null, name, MarketStatus.ACTIVE); }
}
```

## CIコマンド

- Maven: `mvn -T 4 test` または `mvn verify`
- Gradle: `./gradlew test jacocoTestReport`

**覚えておくこと**: テストは高速で、分離され、決定論的に保つ。実装の詳細ではなく、振る舞いをテストする。

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/rx-k8) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
