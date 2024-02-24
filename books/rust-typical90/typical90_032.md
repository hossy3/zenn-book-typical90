---
title: "032 - AtCoder Ekiden（★3）"
---

[032 \- AtCoder Ekiden（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_af)


# アルゴリズム

## 順列 (itertools::permutations())

例題 1 を考えます。走るのに表の時間がかかります。

||区間1|区間2|区間3|
|---|---|---|---|
|選手1|1|10|100|
|選手2|10|1|100|
|選手3|100|10|1|

また、選手1と選手2は仲が悪く、隣り合わせにできないとします。この条件の中で、もっとも合計時間が短くなるものを選びます。

選手は最大 10人です。10人を並び替える順列 10! は、すべての組み合わせを総当たりで調べられます。 `[1, 3, 2]` を、[(選手 1, 区間 1), (選手 2, 区間 3), (選手 3, 区間 2)] のように考えていきます。

|選手1, 選手2, 選手3|可能?|時間|
|---|---|---|
|区間1, 区間2, 区間3|❌||
|区間1, 区間3, 区間2|✅|1 + 100 + 10 = 111|
|区間2, 区間1, 区間3|❌||
|区間2, 区間3, 区間1|❌||
|区間3, 区間1, 区間2|❌||
|区間3, 区間2, 区間1|✅|100 + 1 + 100 = 201|

* [Itertools in itertools \- Rust > permutations()](https://docs.rs/itertools/latest/itertools/trait.Itertools.html#method.permutations)

組み合わせや重複組み合わせ同様、順列についても itertools が使えます。この問題はだいたい以下のコードで解けます。

```rust
let mut min_score = usize::MAX;
for v in (0..n).permutations(n) {
    if v.windows(2).any(|v| s.contains(&(v[0], v[1]))) {
        continue;
    }
    let score = v.iter().enumerate().fold(0, |acc, (j, &i)| acc + a[i][j]);
    min_score = min_score.min(score);
}
```

## 順列 / 再帰

`itertools::permutations()` は便利ですが、最初の方で選手 1 と 2 が隣り合ってもその先のすべての順列を取り出すという、実行時間的には少し無駄のある形になっています。

途中で仲が悪いケースをはじくようにすることもできます。


# 実装例

## 順列 (itertools::permutations())
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/032_permutations.rs

## 順列 / 再帰
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/032_recursive_closure_2.rs
