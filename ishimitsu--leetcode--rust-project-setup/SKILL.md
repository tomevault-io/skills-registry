---
name: rust-project-setup
description: RustでのLeetCode問題セットアップ。Rustプロジェクトを新規作成する時、RustでLeetCodeに取り組む時に使用。Cargo.toml、lib.rs、main.rsの設定を行う。 Use when this capability is needed.
metadata:
  author: ishimitsu
---

# Rust LeetCode プロジェクトセットアップ

## 開発コマンド

### ビルド・実行
- ビルド: `cargo build`
- テスト実行: `cargo test`
- 実行: `cargo run`
- リリースビルド: `cargo build --release`

### デバッグ
- 警告表示: `cargo clippy`
- フォーマット: `cargo fmt`
- ドキュメント生成: `cargo doc --open`

## よく使う標準ライブラリ
- `std::collections::HashMap` - ハッシュテーブル
- `std::collections::HashSet` - ハッシュセット
- `std::collections::VecDeque` - 両端キュー（BFS用）
- `std::collections::BinaryHeap` - 優先度キュー
- `Vec<T>` - 動的配列
- `String` / `&str` - 文字列操作

## ディレクトリ・ファイル作成

ディレクトリ名: `{問題番号}_{問題名(アンダースコア区切り)}`
例: `15_3Sum`, `14_Longest_Common_Prefix`

作成するファイル:
- `Cargo.toml`
- `src/lib.rs`
- `src/main.rs`

### Cargo.toml テンプレート

```toml
[package]
name = "{問題名_snake_case}"
version = "0.1.0"
edition = "2021"

[lib]
path = "src/lib.rs"

[[bin]]
name = "{問題名_snake_case}"
path = "src/main.rs"
```

### src/lib.rs テンプレート

```rust
pub struct Solution;

impl Solution {
    // LeetCodeの関数シグネチャをここに記述
    pub fn solve() -> bool {
        // TODO: implement
        false
    }
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn example1() {
        // Input: ...
        // Output: ...
        // TODO: implement test
    }

    #[test]
    fn example2() {
        // Input: ...
        // Output: ...
        // TODO: implement test
    }
}
```

### src/main.rs テンプレート

```rust
use {crate_name}::Solution;

fn main() {
    // テストケースを手動で実行する場合に使用
    println!("Result: {:?}", Solution::solve());
}
```

---
> Converted and distributed by [TomeVault](https://tomevault.io/claim/ishimitsu) — claim your Tome and manage your conversions.
<!-- tomevault:4.0:skill_md:2026-04-15 -->
