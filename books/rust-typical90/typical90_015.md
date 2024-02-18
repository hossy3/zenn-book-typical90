---
title: "015 - Don't be too close（★6）"
---

[015 \- Don't be too close（★6）](https://atcoder.jp/contests/typical90/tasks/typical90_o)

# アルゴリズム

## 方針

公式解説と同じです。

`N = 9` 個のボールから差が `k = 3` 以上になるように `a = 3` 個のボールを選ぶことを考えます。一番詰めた配置は `[1, 4, 7]` で、`[8, 9]` が空きです。空きを別の場所に動かすイメージです。

具体的に当てはめると次のようになります:

||1|2|3|4|5|6|7|8|9|
|---|---|---|---|---|---|---|---|---|---|
|147|✅|-|-|✅|-|-|✅|❔|❔|
|148|✅|-|-|✅|-|-|❔|✅|❔|
|149|✅|-|-|✅|-|-|❔|❔|✅|
|158|✅|-|-|❔|✅|-|-|✅|❔|
|159|✅|-|-|❔|✅|-|-|❔|✅|
|169|✅|-|-|❔|❔|✅|-|-|✅|
|258|❔|✅|-|-|✅|-|-|✅|❔|
|259|❔|✅|-|-|✅|-|-|❔|✅|
|269|❔|✅|-|-|❔|✅|-|-|✅|
|369|❔|❔|✅|-|-|✅|-|-|✅|

差が 3 以上になるためには、✅の右 2つは使えません。こう見ると ✅✅✅❔❔ の 5個を並び替える問題と同じです。

つまり $_{N-(k-1)(a-1)}C_a$ になります。

## 組み合わせ

$_nC_r = \dfrac{1 \times 2 \times ... \times r}{n \times (n-1)\times ... \times ((n-r+1))}$

そのまま実装するとこのようになります。余りは `modint` を使います。

```rust
fn combination(n: usize, r: usize) -> Mint {
    let r = r.min(n - r);
    (1..=r).fold(Mint::new(1), |acc, x| {
        acc * Mint::new(n - x + 1) / Mint::new(x)
    })
}
```

これで k, a をループすればだいたい解けます。

```rust
for k in 1..=n {
    let result = (1..=(1 + (n - 1) / k)).fold(Mint::new(0), |acc, a| {
        acc + combination(n - (k - 1) * (a - 1), a)
    });
    println!("{result}");
}
```

だいたい解けますが、TLE します。

## 階乗を事前計算

前の実装では、同じ掛け算を何度も繰り返します。ここが遅いです。

その対策としてい、階乗 $n! = 1 \times 2 \times ... \times n$ を事前計算して、配列 fact に詰めておきます。

||0|1|2|3|4|5|...|
|---|---|---|---|---|---|---|---|
|fact|1|1|2|6|24|120|...|

組み合わせ計算では、計算済みの値を再利用するだけで良くなります。

$_nC_r = \dfrac{r! \times (n-r)!}{n!}$


```rust
fn combination(n: usize, r: usize, fact: &[Mint]) -> Mint {
    fact[n] / (fact[r] * fact[n - r])
}
```

```rust
let mut fact = vec![Mint::new(1); n + 1];
for i in 2..=n {
    fact[i] = fact[i - 1] * i;
}

for k in 1..=n {
    let result = (1..=(1 + (n - 1) / k)).fold(Mint::new(0), |acc, a| {
        acc + combination(n - (k - 1) * (a - 1), a, &fact)
    });
    println!("{result}");
}
```

これで AC します。


## 階乗の逆数を事前計算

逆数の余りを求めるのも少し重いです。事前計算しても良いです。

```rust
fn combination(n: usize, r: usize, fact: &[Mint]) -> Mint {
    fact[n] / (fact[r] * fact[n - r])
}
```

```rust
let mut fact_inv = vec![Mint::new(1); n + 1];
for i in 0..=n {
    fact_inv[i] = fact[i].inv();
}

for k in 1..=n {
    let result = (1..=(1 + (n - 1) / k)).fold(Mint::new(0), |acc, a| {
        acc + combination(n - (k - a) * (a - 1), a, &fact, &fact_inv)
    });
    println!("{result}");
}
```

まあしかし、ac-library の modint 逆数計算は十分速いですので、この問題については気にしなくて良いはずです。


# 実装例

## 組み合わせ (TLE)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/015_combination_tle.rs

## 階乗を事前計算
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/015_combination_fact.rs

## 階乗の逆数を事前計算
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/015_combination_factinv.rs
