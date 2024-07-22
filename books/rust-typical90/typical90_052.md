---
title: "052 - Dice Product（★3）"
---

[052 \- Dice Product（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_az)


# アルゴリズム

## 計算式をまとめる

問題1 のサイコロ 2つを考えます。

* 1 2 3 5 7 11
* 4 6 8 9 10 12

得点は 2つのサイコロの出目の積です。次の表になります。

|サイコロ1\2|4|6|8|9|10|12|和|
|---|---|---|---|---|---|---|---|
|1|1 * 4|1 * 6|1 * 8|1 * 9|1 * 10|1 * 12|1 * (4+6+8+9+10+12)|
|2|2 * 4|2 * 6|2 * 8|2 * 9|2 * 10|2 * 12|2 * (4+6+8+9+10+12)|
|3|3 * 4|3 * 6|3 * 8|3 * 9|3 * 10|3 * 12|3 * (4+6+8+9+10+12)|
|5|5 * 4|5 * 6|5 * 8|5 * 9|5 * 10|5 * 12|5 * (4+6+8+9+10+12)|
|7|7 * 4|7 * 6|7 * 8|7 * 9|7 * 10|7 * 12|7 * (4+6+8+9+10+12)|
|11|11 * 4|11 * 6|11 * 8|11 * 9|11 * 10|11 * 12|11 * (4+6+8+9+10+12)|

すべての合計は (1+2+3+5+7+11) * (4+6+8+9+10+12) = 29 * 49 = 1421 になります。サイコロごとの総和を求めて掛け算、です。

## sum() で足し合わせてかけ算

各サイコロの合計を配列 `b` に一度詰めます。

|i|a[i][0]|a[i][1]|a[i][2]|a[i][3]|a[i][4]|a[i][5]|b[i]|
|---|---|---|---|---|---|---|---|
|0|1|2|3|5|7|11|1+2+3+5+7+11 = 29|
|1|4|6|8|9|10|12|4+6+8+9+10+12 = 49|

* [Iterator in std::iter \- Rust > sum()](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.sum)

配列の和 $\sum a_i$ は `Iterator::sum()` で行えます。

* [Iterator in std::iter \- Rust > product()](https://doc.rust-lang.org/std/iter/trait.Iterator.html#method.product)

また、配列の積は  $\Pi b_i$ は `Iterator::product()` で行えます。しかし b を複数かけ合わせると 64bit 整数型の範囲を超えます。そこで `fold()` や `reduce()` で掛け算するたびに余りを求めます。

```rust
const MOD: usize = 1_000_000_007;
let b: Vec<usize> = a.iter().map(|x| x.iter().sum()).collect();
let result = b.iter().fold(1, |acc, x| (acc * x) % MOD);
```

これが答えです。

変数 `b` を経由しなくても、メソッドチェーンでも書けます。

```rust
let result = a
    .iter()
    .map(|x| x.iter().sum())
    .fold(1, |acc, x: usize| (acc * x) % MOD);
```

## modint

`% MOD` を書き忘れてしまいそうなときは、`modint` を使うのも良いです。こちらなら `product()` で計算が終わります。

```rust
type Mint = ac_library::ModInt1000000007;

let b: Vec<_> = a
    .iter()
    .map(|x| Mint::new(x.iter().sum::<usize>()))
    .collect();
let score: Mint = b.iter().product();
```

```rust
let result: Mint = a
    .iter()
    .map(|x| Mint::new(x.iter().sum::<usize>()))
    .product();
```


# Tips

## sum() と product() の結果の型指定

先ほど

```rust
let b: Vec<usize> = a.iter().map(|x| x.iter().sum()).collect();
```

のように `b` の型を明示しました。 `a` が `Vec<Vec<usize>>` なら、`Vec<_>` で型推論してくれそうにも思います。しかし、残念ながらうまくいきません。


```rust
let a = [1_usize, 2, 3, 5, 7, 11];
let result = a.iter().sum();
println!("{result}");
```

```
error[E0283]: type annotations needed
    --> src/main.rs:3:9
     |
3    |     let result = a.iter().sum();
     |         ^^^^^^            --- type must be known at this point
```

`sum()` の入力の型と出力の型は異なっていても良いため、だそうです。

* [Why can't Rust infer the resulting type of Iterator::sum? \- Stack Overflow](https://stackoverflow.com/questions/41017140/why-cant-rust-infer-the-resulting-type-of-iteratorsum)
![](https://storage.googleapis.com/zenn-user-upload/ac5094980803-20240120.png)

私は不便だと思います……。

というわけで、`sum()` したいときには型ヒントを与えるか、または `fold()`, `reduce()` にお任せするようにします。

```rust
let a = [1, 2, 3, 5, 7, 11];
let result: usize = a.iter().sum();
assert_eq!(result, 29);

let result = a.iter().sum::<usize>();
assert_eq!(result, 29);

let result = a.iter().fold(0, |acc, &x| acc + x); // 空配列の和は 0 (単位元)
assert_eq!(result, 29);

let result = a.into_iter().reduce(|acc, x| acc + x).unwrap();
assert_eq!(result, 29);
```

`product()` も同様です。

```
let b = [29, 49];
let result: usize = b.iter().product();
assert_eq!(result, 29 * 49);

let result = b.iter().product::<usize>();
assert_eq!(result, 29 * 49);

let result = b.iter().fold(1, |acc, &x| acc * x); // 空配列の積は 1 (単位元)
assert_eq!(result, 29 * 49);

let result = b.into_iter().reduce(|acc, x| acc * x).unwrap();
assert_eq!(result, 29 * 49);
```

# 実装例

## % MOD
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/052_product_fold_2.rs

## modint
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/052_product_modint_2.rs
