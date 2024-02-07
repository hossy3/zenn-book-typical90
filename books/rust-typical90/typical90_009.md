---
title: "009 - Three Point Angle（★6）"
---

[009 \- Three Point Angle（★6）](https://atcoder.jp/contests/typical90/tasks/typical90_i)

# アルゴリズム

## 方針

頂点 0 の角度 $\angle{P_iP_0P_k}$ の最大値を求めたいとします。i, k の全探索をすると重いです。 $P_1 ... P_n$ を $P_0$ から見た角度順に並べておくと、尺取り法や二分探索で計算量を減らせます。

例題 3 である頂点から見た他の点が、次の位置にあったとします:

|角度|||||||||
|---|---|---|---|---|---|---|---|---|
|-120.9|-45|0|30.9|45|45|108.4|135|135|
|✅|||||✅||||
||✅||||||✅||

左端を動かし、右端を 180度近くになるところを尺取り法や二分探索で追っていきます。下側の 135 - (-45) = 180度 が答えになります。

## 尺取り法

左端 `i` を動かしながら、右端 `j` を動かして尺取りするとこんな感じになります。

```rust
fn calc_score(degs: &[f64]) -> f64 {
    let n = degs.len();
    let mut score = 0f64;
    let mut j = 0;
    for i in 0..n {
        while j < n {
            let s = degs[j] - degs[i];
            let s0 = s.min(360.0 - s);
            score = score.max(s0);
            if s > 180.0 {
                break;
            }
            j += 1;
        }
    }

    score
}
```

2つのポインターの移動、調べる状態の更新が混ざります。私はいつも混乱しそうになります。かと言ってライブラリー化できるほど一般化できそうにもなく。

## 二分探索 (Vec::partition_point())

* [Vec in std::vec \- Rust > partition_point](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.partition_point)

二分探索でも解けます。

```rust
fn calc_score(degs: &[f64]) -> f64 {
    let n = degs.len();
    let mut score = 0f64;
    for &deg in degs {
        let i = degs.partition_point(|&x| x - deg <= 180.0);
        let deg0 = degs[(i + n - 1) % n] - deg;
        score = score.max(deg0.min(360.0 - deg0));

        let deg0 = degs[i % n] - deg;
        score = score.max(deg0.min(360.0 - deg0));
    }

    score
}
```

## 実数の比較

最後にスコアの最大値を求めたいです。スコアが整数ならこのように書けます。

```rust
let score = (0..n).map(|i| f(i, &xy)).max().unwrap();
```

しかし実数ではこう書けません。 Rust の実数は `Ord` 全順序 Trait を満たしていないためです。詳しくは AtCoder で使用できるクレートの `ordered-float` 説明をどうぞ。

* [2020 Update · rust\-lang\-ja/atcoder\-rust\-resources Wiki > ordered-float](https://github.com/rust-lang-ja/atcoder-rust-resources/wiki/2020-Update#ordered-float)


私は代わりに、個々に比較した結果を `fold()` でまとめています。

* [Iterator in std::iter \- Rust](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.fold)


```rust
let score = (0..n).fold(0f64, |acc, i| acc.max(f(i, &xy)));
```

# 実装例

## 尺取り法
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/009_two_pointer.rs

## 二分探索 (Vec::partition_point())
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/009_partition_point.rs
