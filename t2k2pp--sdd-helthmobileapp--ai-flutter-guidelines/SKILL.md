---
name: ai-flutter-guidelines
description: AI駆動Flutter開発ガイドライン。LLMコーディングアシスタントの典型的な問題と対策、禁止行動、ベストプラクティスを定義。全サブエージェントが参照すべき基盤スキル。 Use when this capability is needed.
metadata:
  author: t2k2pp
---

# AI駆動Flutter開発ガイドライン

AI（LLM）を活用したFlutter開発で発生しやすい問題と、その対策を定義する。

---

## 🚫 禁止行動（MUST NOT）

### 1. 簡易実装への逃げ
```
❌ 禁止: エラーが困難だからと言って、簡易的な処理で完了とする
❌ 禁止: 複雑な処理をスキップして「TODO: 後で実装」で済ませる
❌ 禁止: 仕様を満たさない部分的な実装で完了とする

✅ 必須: 困難な場合はユーザーに相談し、代替案を提示する
✅ 必須: 完全な実装が不可能な場合は理由と制限を明示する
```

### 2. モック・スタブでの代替
```
❌ 禁止: 本番コードをモックで置き換えて完了とする
❌ 禁止: テストコード以外でモックデータを返す実装
❌ 禁止: API未実装を理由にハードコード値で代替

✅ 必須: 実際の実装を行う
✅ 必須: 外部依存がある場合は正しい待機処理を実装
```

### 3. 無断のコード削除・変更
```
❌ 禁止: 理解できないコードを「不要」と判断して削除する
❌ 禁止: 既存機能を無断で削除・変更する
❌ 禁止: エラー解消のために機能を減らす

✅ 必須: 削除前にユーザーに確認する
✅ 必須: 削除理由を明確に説明する
✅ 必須: 削除した場合は必ず報告する
```

### 4. エラー・警告の無視
```
❌ 禁止: コンパイルエラーを放置する
❌ 禁止: 「とりあえず動く」状態で完了とする
❌ 禁止: 警告を無視する（特にdeprecated警告）

✅ 必須: 全エラーを解消してから完了とする
✅ 必須: 警告も可能な限り解消する
```

### 5. 無駄なリトライ
```
❌ 禁止: 同じアプローチを3回以上繰り返す
❌ 禁止: ユーザーの要望に忖度して不可能なことを繰り返し試す
❌ 禁止: 根本原因を調べずに試行錯誤を続ける

✅ 必須: 2回失敗したらアプローチを変える
✅ 必須: 不可能と判断したら正直に報告する
✅ 必須: 制限や前提条件を確認する
```

---

## ⚠️ 注意すべき問題と対策

### 1. 間違った言語の使用

**問題:** FlutterなのにKotlin/Swiftで実装しようとする

**対策:**
- ファイル拡張子を確認する（`.dart`であること）
- Flutter/Dartの構文であることを検証する
- プラットフォームチャネル以外でnative言語を使わない

```dart
// ✅ 正しい: Dart
class UserRepository {
  Future<User> getUser(String id) async {
    final response = await dio.get('/users/$id');
    return User.fromJson(response.data);
  }
}

// ❌ 間違い: Kotlin（Flutterプロジェクトなのに）
class UserRepository {
    suspend fun getUser(id: String): User {
        return api.getUser(id)
    }
}
```

### 2. 古いライブラリ・API使用

**問題:** 学習データに含まれる古いバージョンを使用する

**対策:**
- 現在の日付: 2026年2月
- Riverpod 3.0（2.5.x）を使用する
- Flutter 3.19+を前提とする
- 非推奨APIを使わない

```dart
// ❌ 古い: Provider（Riverpod前）
ChangeNotifierProvider(create: (_) => MyModel())

// ✅ 現在: Riverpod 3.0
@riverpod
class MyNotifier extends _$MyNotifier {
  @override
  State build() => State();
}
```

**チェックすべき非推奨パターン:**
| 非推奨 | 現在の推奨 |
|-------|-----------|
| `Provider`パッケージ | `flutter_riverpod` |
| `setState` + StatefulWidget | Riverpod + ConsumerWidget |
| `FutureBuilder` | `FutureProvider` / `AsyncValue` |
| `StreamBuilder` | `StreamProvider` |
| `Navigator 1.0` | `go_router` |
| `http`パッケージ | `dio` |

### 3. ハルシネーション（存在しないAPI）

**問題:** 存在しないメソッド・クラスを生成する

**対策:**
- 生成したコードは`flutter analyze`で検証する
- 不明なAPIを使う前にドキュメントを確認する
- import文が正しいか確認する

```dart
// ❌ ハルシネーション例: 存在しないメソッド
final user = await FirebaseAuth.instance.getUser(); // 存在しない

// ✅ 正しいAPI
final user = FirebaseAuth.instance.currentUser;
```

### 4. コンテキスト消失

**問題:** 会話が長くなるとファイル構造や要件を忘れる

**対策:**
- 1ファイル200行を目安に分割する
- 各ファイルの冒頭にコメントで役割を記述する
- 依存関係をimport文で明示する

```dart
/// [AuthRepository] 認証関連のデータ操作を担当
/// 
/// 依存: [Dio], [FlutterSecureStorage]
/// 使用: [AuthNotifier]
class AuthRepository {
  // ...
}
```

### 5. エッジケース・エラーハンドリング不足

**問題:** 正常系のみ実装し、異常系を無視する

**対策:**
- 全ての外部呼び出しにtry-catchを追加する
- null/empty/errorの3状態を必ず実装する
- タイムアウト、ネットワークエラーを考慮する

```dart
// ✅ 完全なエラーハンドリング
Future<void> login(String email, String password) async {
  state = const AsyncValue.loading();
  state = await AsyncValue.guard(() async {
    try {
      return await repository.login(email, password);
    } on DioException catch (e) {
      if (e.type == DioExceptionType.connectionTimeout) {
        throw AppException('接続がタイムアウトしました');
      }
      throw AppException('ネットワークエラー: ${e.message}');
    } on AuthException catch (e) {
      throw AppException('認証エラー: ${e.message}');
    }
  });
}
```

### 6. dispose忘れ（メモリリーク）

**問題:** リソースの解放を忘れる

**対策チェックリスト:**
- [ ] `TextEditingController` → `dispose()`
- [ ] `AnimationController` → `dispose()`
- [ ] `StreamSubscription` → `cancel()`
- [ ] `Timer` → `cancel()`
- [ ] `ScrollController` → `dispose()`
- [ ] `FocusNode` → `dispose()`

```dart
class _MyWidgetState extends State<MyWidget> {
  final _controller = TextEditingController();
  late final AnimationController _animController;
  StreamSubscription? _subscription;

  @override
  void initState() {
    super.initState();
    _animController = AnimationController(vsync: this);
    _subscription = stream.listen(_onData);
  }

  @override
  void dispose() {
    _controller.dispose();       // 必須
    _animController.dispose();   // 必須
    _subscription?.cancel();     // 必須
    super.dispose();
  }
}
```

### 7. 巨大ファイル生成

**問題:** トークン制限を超える巨大ファイルを生成する

**対策:**
- 1ファイル200行以内を目安に分割する
- 機能単位でファイルを分ける
- 共通処理はutilsに切り出す

```
推奨構造:
lib/features/auth/
├── presentation/
│   ├── screens/
│   │   ├── login_screen.dart      # 50-100行
│   │   └── register_screen.dart   # 50-100行
│   └── widgets/
│       ├── login_form.dart        # 50-80行
│       └── password_field.dart    # 30-50行
```

---

## ✅ 必須プラクティス

### 作業前確認
1. 対象がFlutter/Dartプロジェクトか確認
2. 既存のアーキテクチャ・パターンを確認
3. 使用ライブラリのバージョンを確認

### 作業中
1. 各変更後に`flutter analyze`を実行
2. 既存機能を壊していないか確認
3. 不明点はユーザーに質問

### 作業後
1. 全エラー・警告を解消
2. 削除した機能があれば報告
3. 制限事項・注意点を明示

---

## クイックリファレンス

### 現在の推奨バージョン（2026年2月）
| パッケージ | 推奨バージョン |
|-----------|---------------|
| Flutter | 3.19+ |
| Dart | 3.3+ |
| flutter_riverpod | 2.5.x |
| riverpod_annotation | 2.3.x |
| dio | 5.4.x |
| go_router | 13.x |
| freezed | 2.5.x |

### 禁止チェックリスト
- [ ] 簡易実装で完了としていない
- [ ] モックで本番コードを代替していない
- [ ] コードを無断削除していない
- [ ] エラー・警告が残っていない
- [ ] 同じ失敗を3回以上繰り返していない
- [ ] Flutterプロジェクトで他言語を使っていない
- [ ] 古いAPIを使っていない
- [ ] disposeを忘れていない

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/t2k2pp) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-14 -->
