---
title: "はじめに"
---

競技プログラミングの解き方を、アルゴリズム・ロジックの観点から解説している記事が多くあります。しかし、アルゴリズムが分かったとしても、そのアルゴリズムを特定のプログラミング言語でどう実装するか、どういったところでハマりがちかという記事はそんなに多くないように思います。

本記事では、AtCoder 2023 年言語アップデート環境の Rust 1.70 + 利用可能なライブラリーを使って、競技プログラミング教材 [競プロ典型 90 問 \- AtCoder](https://atcoder.jp/contests/typical90) を前から順に解いてみます。


# 本記事で行うこと

* Rust で典型 90 問を解きます
* 再利用可能そうな部品は、ライブラリー化・スニペット化します
  * 同じような問題が今後出てきたときに、本記事を参考にするとバグらせずに実装できることを目指します
* 難しい問題は単体テストを書き、どこまで確実に動くかを確認しながら進めます


# 本記事で行わないこと

* Rust の基本的な文法紹介
  * [RustCoder ―― AtCoder と Rust で始める競技プログラミング入門](https://zenn.dev/toga/books/rust-atcoder) がとても良くまとまっています
* アルゴリズム・解き方をどのように気づくか
  * E869120 さんによる公式解説や、他の方によるユーザー解説が詳しいです
* コード量を減らすことは目指しません
  * コーディング速度よりも、分かりやすさや再利用性を優先します


# 本記事中のコーディングスタイル

* rustfmt (フォーマッター) を通します
* `cargo Clippy` (リンター) を通します
* 配列のインデックスは、基本的には読み込み時に 0-based に変換し、出力時に必要なら 1-based にします
  * 単体テストを書きたい場合は違う場所で行うこともあります
* グローバル変数は使いません
* 宣言型プログラミング寄りな実装を行います
  * 手続き的に可変変数 `mut` も使用します
* 変数名・関数名は適当にすることがあります
  * スコープが短いですので
* シャドウイングを多用します
  * 誤った変数を参照や書き換えしないように
* 誤ってバグを埋め込んだときに `panic!` しやすくします
  * panic した場所をデバッグ実行で止めると、その時の変数値から、どこにバグがあるか気づきやすいため


## よく使う型

|型|用途|Note|
|---|---|---|
|`usize`|非負整数|インデックスとしてそのまま扱えることと、 u32 をはみ出すことも多いため|
|`u8`|クエリーの種類|型を分けると誤用しづらいため|
|`i64`|整数|||
|`f64`|実数|Rust では全順序でないことに注意|
|`Vec<chars>`|文字の配列|`String` は扱いづらいのでサボります|


# 使用クレート

AtCoder 2023 年言語アップデート環境で使えるクレートの概要が、AtCoder Rust Wiki にサンプルコード付きで簡潔に分かりやすくまとまっています。

* [Since 1\.15\.1 · rust\-lang\-ja/atcoder\-rust\-resources Wiki](https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/Since-1.15.1)
* [Jan 2023 Language Update ライブラリ案 · rust\-lang\-ja/atcoder\-rust\-resources Wiki](https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/Jan-2023-Language-Update-%E3%83%A9%E3%82%A4%E3%83%96%E3%83%A9%E3%83%AA%E6%A1%88)

すべて使おうとするとビルド時間等に影響しますので、この中の一部を使います。


## よく使うクレート

|クレート|用途|
|---|---|
|[std](https://doc.rust-lang.org/std/)|||
|[ac-library-rs](https://crates.io/crates/ac-library-rs)|AtCoder 頻出アルゴリズム|
|[itertools](https://crates.io/crates/itertools)|イテレーター操作の拡張|
|[num-integer](https://crates.io/crates/num-integer)|整数演算の拡張|
|[proconio](https://crates.io/crates/proconio)|標準入出力|

## たまに使うクレート

|クレート|用途|Note|
|---|---|---|
|[nalgebra](https://crates.io/crates/nalgebra)|行列|ndarray より好み|
|[num-bigint](https://docs.rs/num-bigint/latest/num_bigint/)|多倍長整数|||
|[num-complex](https://crates.io/crates/num-bigint)|複素数|||
|[num-rational](https://crates.io/crates/num-rational)|分数|||
|[pathfinding](https://crates.io/crates/pathfinding)|グラフ|petgraph より入力組み立てが楽|


# 開発環境

* Windows 11
* [Rust](https://www.rust-lang.org/ja)
* [Visual Studio Code](https://azure.microsoft.com/ja-jp/products/visual-studio-code)
  * [rust-analyzer extension](https://marketplace.visualstudio.com/items?itemName=rust-lang.rust-analyzer)
  * [CodeLLDB extension](https://marketplace.visualstudio.com/items?itemName=vadimcn.vscode-lldb)
* [cargo-compete](https://github.com/qryxip/cargo-compete/blob/master/README-ja.md)


# 本記事の構成

問題ごとに次の構成で書きます。

* 問題文へのリンク
* アルゴリズム
  * 問題を解くためのアルゴリズム。ライブラリーにあるものをそのまま呼び出す場合もあれば、一から実装する場合もあります。
* Tips
  * それ以外の言語機能など、解答例のコーディングに関連するものを紹介します
* 解答例
