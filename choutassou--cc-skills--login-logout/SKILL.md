---
name: login-logout
description: 実行されているWebアプリケーション(http://$URL:$PORT)にログインする。agent-browserを使用してユーザー名・パスワードを自動入力し、MFA認証コードのみ人間に入力させる。動作確認(confirm)、テスト(test)する時に、ログイン(login)、認証(authentication)、サインイン(signin)が必要な時に使用する。 Use when this capability is needed.
metadata:
  author: choutassou
---

# login-logout Skill

動作確認、テストへのログイン自動化。

## 重要ポイント

### 認証コードについて
メールまたは電話に送信される認証コード、または認証アプリによる生成される認証コードは人間に入力させる

### agent-browserのロードについて
agent-browserというスキルの初期ロードはとても時間かかる。この時間を7分以内に短縮してください。
こちら実施する動作確認、テストの内容は、主には"操作、遷移と画面表示項目は正しいか"だけですので、一部コアな用法だけは把握すれば充分です。
単純な用法というのは、open、snapshot、fill、click、select、navigation等の操作

用途等を把握しなくてもよいコマンド
 - /agent-browser trace *
 - /agent-browser console *
 - /agent-browser frame *
 - /agent-browser network *
 - /agent-browser frame *
 - /agent-browser cookies *
 - /agent-browser storage *

## 本文中のパラメータ

本文中、すべての"$XXXX"は"パラメータXXXX"を意味する。
例：
"$PORT"には"PORTパラメータの値"を意味する

## argument default values
- OPER: `login`
- PORT: `3000`
- USER: `xxxxxxxxxx`
- PWD: `xxxxxxxxx`
- URL: `localhost`

## OPER=loginの時の手順

1. ページを開く
```bash
agent-browser open http://$URL:$PORT
agent-browser wait --load networkidle
```

2. ユーザー名を入力して次へ
```bash
agent-browser snapshot -i
agent-browser fill @e1 "$USER"
agent-browser click @e2
```

3. パスワードを入力して続行
```bash
agent-browser snapshot -i
agent-browser fill @e1 "$PWD"
agent-browser click @e4
```

4. MFA認証コードを人間に入力させる
```bash
agent-browser snapshot -i
```
ユーザーが把握した認証コードを聞いて、入力する：
```bash
agent-browser fill @e1 "<認証コード>"
agent-browser click @e2
```

5. ログイン完了を確認
```bash
agent-browser snapshot -i
```
「サインアウト」ボタンが表示されていればログイン成功。

## OPER=logoutの時の手順

画面上に"ログアウト"を意味するリンク/ボタン/メニューアイテムを見つけてクリックする。
"ログアウト"を意味するものの例：
- sign out
- logout
- サインアウト
- ログアウト
- 退出

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/choutassou) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
