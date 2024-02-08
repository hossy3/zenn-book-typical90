---
title: "014 - We Used to Sing a Song Together（★3）"
---

[014 \- We Used to Sing a Song Together（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_n)

# アルゴリズム

## ソート

||||||||
|---|---|---|---|---|---|---|
|A🏠|8|6|9|2|1|0|
|B🏫|1|5|7|2|3|9|

元の順番のまま、不満度が小さくなるように近いペアを探すのは大変です。

||||||||
|---|---|---|---|---|---|---|
|A🏠|0|1|2|6|8|9|
|B🏫|1|2|3|5|7|9|
|不満度|1|1|1|1|1|0|

並び替えて順番に対応付ければ、それが不満度最小のペアになります。

```rust
a.sort();
```

などで並び替えます。


## 合計値を求める

* [Iterator in std::iter \- Rust > sum()](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.sum)

`iter::sum()` で合計値を求められます。

```rust
let result: usize = iter::zip(a, b).map(|(a, b)| a.abs_diff(b)).sum();
```

ループを回しても良いです。

```rust
let mut result = 0;
for i in 0..n {
    result += a[i].abs_diff(b[i]);
}
```

前者の書き方は「result が不変になるのでその後誤って書き換えない」「インデックス使用誤りを防げる」という利点があります。


# 実装例

## ソートして合計値を求める
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/014_sort.rs

