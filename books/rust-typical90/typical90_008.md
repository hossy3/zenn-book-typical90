---
title: "008 - AtCounter（★4）"
---

[008 \- AtCounter（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_h)

# アルゴリズム

## DP

こんな感じで DP を書くと解けます。

* 最初の値は 1
* 左の状態数をコピーする
* ⚡文字があっているときは、左上の状態数の和を取る

||初期|a|t|t|c|o|r|d|e|e|r|
|---|---|---|---|---|---|---|---|---|---|---|---|
|[0]|1|1|1|1|1|1|1|1|1|1|1|
|[1] a||⚡1|1|1|1|1|1|1|1|1|1|
|[2] at|||⚡1|⚡2|2|2|2|2|2|2|2|
|[3] atc|||||⚡2|2|2|2|2|2|2|
|[4] atco||||||⚡2|2|2|2|2|2|
|[5] atcod||||||||⚡2|2|2|2|
|[6] atcode|||||||||⚡2|⚡4|4|
|[7] atcoder|||||||⚡||||⚡4|

この通り実装すれば AC します:
```rust
for (i, &c) in s.iter().enumerate() {
    for j in 0..M {
        let x = dp[i][j];
        dp[i + 1][j] += x;
    }
    for (j, &c0) in s0.iter().enumerate() {
        if c == c0 {
            let x = dp[i][j];
            dp[i + 1][j + 1] += x;
        }
    }
}
```

# Tips

## proconio::Chars

* [proconio \- Rust](https://docs.rs/proconio/latest/proconio/)

Rust で文字列 `String` を扱うのにひと手間かかります。競技プログラミングでは `Vec<char>` や `Vec<u8>` で文字の配列になっていた方が扱いやすいこともあります。

`String` として読み込む場合:

```rust
input! {
    s: String,
};
```

`Vec<char>` として読み込む場合:

```rust
input! {
    s: Chars,
};
```

こうすると文字単位でループを回すのも簡単です。

```rust
for &c in &s {
    // ...
}
```

## iter().enumerate()

ループを回す際に、個々の要素に加えてインデックスも欲しいときに、私は良く `iter().enumerate()` します。

```rust
for (i, &c) in s.iter().enumerate() {
    // :
}
```

慣れている言語によっては、Rust でこう書きたくなるかもしれません:

```rust
for i in 0..(s.len()) {
    let c = s[i];
    // :
}
```

でもこうすると、多重ループを実装するときに `s[j]` のつもりでうっかり違うループカウンターを使い `s[i]` と書いてしまうという事故があります。 `iter().enumerate()` すると `(i, &c)` とペアを描いていますから間違いにくいです。後からコードを読み直すときも、ペアの対応が分かりやすいです。

# 実装例

## DP
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/008_dp.rs
