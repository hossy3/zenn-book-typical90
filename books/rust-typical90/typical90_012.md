---
title: "012 - Red Painting（★4）"
---

[012 \- Red Painting（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_l)

# アルゴリズム

## Disjoint Set (Union Find) でグループ化

「🔴」で赤く塗るたびに隣「🔍」を見て、もし赤いマス「❌」なら Disjoint Set でつなげます。

||||
|---|---|---|
|0|1|2|
|3|4|5|
|6|7|8|

→ グループ `[]`

||||
|---|---|---|
|0|1 🔍|2|
|3 🔍|4 🔴|5 🔍|
|6|7 🔍|8|

→ グループ `[[4]]`

||||
|---|---|---|
|0 🔴|1 🔍|2|
|3 🔍|4 ❌|5|
|6|7|8|

→ グループ `[[0], [4]]`

||||
|---|---|---|
|0 ❌|1|2|
|3|4 ❌🔍|5|
|6|7 🔴|8|

→ グループ `[[0], [4, 7]]`

||||
|---|---|---|
|0 ❌|1|2|
|3|4 ❌🔍|5 🔴|
|6|7 ❌|8|

→ グループ `[[0], [4, 5, 7]]`

||||
|---|---|---|
|0 ❌🔍|1|2|
|3 🔴|4 ❌🔍|5 ❌|
|6 🔍|7 ❌|8|

→ グループ `[[0, 3, 4, 5, 7]]`

同グループかどうかは一瞬で分かります。

* [Dsu in ac\_library::dsu \- Rust](https://docs.rs/ac-library-rs/0.1.1/ac_library/dsu/struct.Dsu.html)

`ac_library::Dsu` を使う場合は、最初に要素数を設定します。

```rust
let mut uf = Dsu::new(h * w);
```

# Tips

## クロージャ

* [クロージャ：環境をキャプチャできる匿名関数 \- The Rust Programming Language 日本語版](https://doc.rust-jp.rs/book-ja/ch13-01-closures.html)

2次元座標 `(r0, c0)` を 1次元座標 `i` に変換する関数が書きたいとします。

```rust
fn f(w: usize, r: usize, c: usize) -> usize {
    r * w + c
}

let i = f(w, r0, c0);
```

よりも、

```rust
let f = |r: usize, c: usize| -> usize { r * w + c };

let i = f(r0, c0);
```

や

```rust
let f = |r, c| r * w + c;

let i = f(r0, c0);
```

と書く方が簡単です。幅 `w` が固定なら、毎回同じ引数を書くよりも、クロージャ内にキャプチャーする方が良いです。


## 上下左右を辿る

* [usize \- Rust > wrapping_add_signed()](https://doc.rust-lang.org/core/primitive.usize.html#method.wrapping_add_signed)

上に辿る = `(-1, 0)` だけ移動する、のようにそれぞれの移動先を配列にして、足し合わせることをよく行います。

Rust では非負整数 `usize` と `-1` を直接足し算できません。そこで負の値を折り返す `wrapping_add_signed()` を使います。足し合わせた結果 `-1` だと、`usize` としては `usize::MAX` になります。

折り返して足し算したあと、幅・高さが範囲内かどうかを調べれば、枠の外側を見ることもありません。

```rust
let dir: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];
for &(dr, dc) in &dir {
    let (r0, c0) = (r.wrapping_add_signed(dr), c.wrapping_add_signed(dc));
    if r0 < h && c0 < w {
        // :
    }
}
```

## 周辺に余白を付けるか

隣を見るときに、端かどうかを調べて分岐するのはちょっと手間です。余白を1マス分置くようにすると、常に上下左右にアクセスして良くなります。

||||||
|---|---|---|---|---|
|0|1|2|3|4|
|5|6❌🔍|7|8|9|
|10 🔍|11🔴|12 ❌🔍|13 ❌|14|
|15|16 🔍|17 ❌|18|19|
|20|21|22|23|24|

しかし良いことばかりではなく、行列の番号対応が分かりにくくなる面もあります。個人的には速度重視な上級者向けテクニックだと思っています。

```rust
let dir: [(isize, isize); 4] = [(-1, 0), (1, 0), (0, -1), (0, 1)];
for &(dr, dc) in &dir {
    let (r0, c0) = (r.wrapping_add_signed(dr), c.wrapping_add_signed(dc));
    // :
}
```

余白を付けないコードとの違いは `if r0 < h && c0 < w` が取れたことだけです。非負整数型と整数型を強く区別する Rust では、そんなに嬉しくないと思います。


# 実装例

## Disjoint Set (Union Find) でグループ化
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/012_dsu.rs

## Disjoint Set + 上下左右を配列で辿る
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/012_dsu_wrapping.rs

## Disjoint Set + 周辺に余白を付ける
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/012_dsu_padding.rs
