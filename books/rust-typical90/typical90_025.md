---
title: "025 - Digit Product Equation（★7）"
---

[025 \- Digit Product Equation（★7）](https://atcoder.jp/contests/typical90/tasks/typical90_y)


# アルゴリズム

## 重複組み合わせ (combinations_with_replacement())

999以下の、各桁の積に 434 を足した値を考えます。

|1桁の組|f(x)|b + f(x)|結果|
|---|---|---|---|
|0|0|434|❌|
|1|1|435|❌|
|2|2|436|❌|
|:|:|:|:|
|9|9|443|❌|

|2桁の組|f(x)|b + f(x)|結果|
|---|---|---|---|
|00|0|434|❌|
|01|0|434|❌|
|02|0|434|❌|
|:|:|:|:|
|11|1|435|❌|
|12|2|436|❌|
|:|:|:|:|

|3桁の組|f(x)|b + f(x)|結果|
|---|---|---|---|
|000|0|434|❌|
|001|0|434|❌|
|:|:|:|:|
|455|100|534|❌|
|456|120|554|❌|
|457|140|574|✅|
|:|:|:|:|
|777|343|777|✅|
|:|:|:|:|

同じ数字を含む組み合わせに対して `b + f(x)` を求め、並び替えると同じ値になるかを調べれば良いです。 `574` を並び替えると `457` になります。 1～999 の数を全探索するよりも、組み合わせの方が少ないです。

* [Itertools in itertools \- Rust > combinations_with_replacement()](https://docs.rs/itertools/latest/itertools/trait.Itertools.html#method.combinations_with_replacement)

```rust
let nums: Vec<_> = (0usize..=9).collect();
for v in nums.iter().combinations_with_replacement(i) {
    // :
}
```

重複組み合わせは `itertools::combinations_with_replacement()` で列挙できます。再帰でもできます。

## 並び替えた数字が同じかを調べる

10要素の配列に、各桁の成分の個数を入れます。値が同じになるかを調べます。

||0|1|2|3|4|5|6|7|8|9|結果|
|---|---|---|---|---|---|---|---|---|---|---|---|
|457|||||1|1||1||||
|574|||||1|1||1|||✅|

||0|1|2|3|4|5|6|7|8|9|結果|
|---|---|---|---|---|---|---|---|---|---|---|---|
|004|2||||1|||||||
|4|||||1||||||❌|

重複組み合わせでは `004` のような数字も表れます。 `4` なのに積が `0` になると答えがあわなくなります。重複組み合わせ側では先頭の `0` の個数を数え、`b + f(x)` 側では先頭の `0` の個数を数えないようにすれば、これを除けます。


# 実装例

## 重複組み合わせ combinations_with_replacement()
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/025_combinations_with_replacement.rs

## 再帰
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/025_recursive.rs
