---
title: "004 - Cross Sum（★2）"
---

[004 \- Cross Sum（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_d)

# アルゴリズム

## 前処理

|||||
|---|---|---|---|
|✅3|✅1|✅4|✅1|
|✅5|9|2|6|
|✅5|3|5|8|
|✅9|7|9|3|

この✅の合計をそのまま求めるには `3 + 1 + 4 + 1 + 5 + 5 + 9 = 28` と多くの足し算が必要です。

前処理で各行と各列の合計を求めておくと、

|||||行の合計|
|---|---|---|---|---|
||3|1|4|1|9|
||5|9|2|6|22|
||5|3|5|8|21|
||9|7|9|3|28|
|列の合計|22|20|20|18|80|

|||||行の合計|
|---|---|---|---|---|
||✅3|1|4|1|✅9|
||5|9|2|6|22|
||5|3|5|8|21|
||9|7|9|3|28|
|列の合計|✅22|20|20|18|80|

先ほどの結果は `9 + 22 - 3 = 28` と少ない計算で求められます。

```rust
    let mut sum_c = vec![0; w];
    let mut sum_r = vec![0; h];
    for r in 0..h {
        for c in 0..w {
            sum_c[c] += a[r][c];
            sum_r[r] += a[r][c];
        }
    }
```


# Tips

## itertools::join(" ")

この問題では複数の値を出力します。値と値の間にはスペースを入れ、行末はスペースなしです。

```
28 28 25 26
39 33 40 34
38 38 36 31
41 41 39 43
```

このとおり出力しようとすると、ひと手間かかります。

* [join in itertools \- Rust](https://docs.rs/itertools/latest/itertools/fn.join.html)

`itertools::join(" ")` を使うと、配列などを空白区切りで出力できます。

```rust
let v = vec![28, 28, 25, 26];
let result = v.iter().join(" ");
println!("{result}"); // 28 28 25 26
```

## 実装例

### 前処理
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/004_grid.rs
