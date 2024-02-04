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


# 実装例

## Disjoint Set (Union Find) でグループ化
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/012_dsu.rs

## Disjoint Set + 周辺に余白を付ける
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/012_dsu_padding.rs
