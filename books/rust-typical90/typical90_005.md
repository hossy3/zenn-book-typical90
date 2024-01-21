---
title: "005 - Restricted Digits（★7）"
---

[005 \- Restricted Digits（★7）](https://atcoder.jp/contests/typical90/tasks/typical90_e)

# アルゴリズム

まず行列で DP する方針で考えます。たとえば「1,4,9 から作れる 3 桁の 7 の倍数」の個数を求めるときは、$x_{桁, 余り}$ を次のように更新します。

$$
\left(
\begin{matrix}
x_{k + 1, 0} \\
x_{k + 1, 1} \\
x_{k + 1, 2} \\
x_{k + 1, 3} \\
x_{k + 1, 4} \\
x_{k + 1, 5} \\
x_{k + 1, 6} \\
\end{matrix}
\right) = \left(
\begin{matrix}
0 & 1 & 1 & 0 & 1 & 0 & 0 \\
1 & 0 & 1 & 0 & 0 & 0 & 1 \\
1 & 0 & 0 & 0 & 1 & 1 & 0 \\
0 & 0 & 1 & 1 & 0 & 1 & 0 \\
1 & 1 & 0 & 1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 & 0 & 1 & 1 \\
0 & 0 & 0 & 1 & 1 & 0 & 1 \\
\end{matrix}
\right) \left(
\begin{matrix}
x_{k, 0} \\
x_{k, 1} \\
x_{k, 2} \\
x_{k, 3} \\
x_{k, 4} \\
x_{k, 5} \\
x_{k, 6} \\
\end{matrix}
\right)
$$

$$
\left(
\begin{matrix}
x_{3, 0} \\
\_ \\
\_ \\
\_ \\
\_ \\
\_ \\
\_ \\
\end{matrix}
\right) = \left(
\begin{matrix}
0 & 1 & 1 & 0 & 1 & 0 & 0 \\
1 & 0 & 1 & 0 & 0 & 0 & 1 \\
1 & 0 & 0 & 0 & 1 & 1 & 0 \\
0 & 0 & 1 & 1 & 0 & 1 & 0 \\
1 & 1 & 0 & 1 & 0 & 0 & 0 \\
0 & 1 & 0 & 0 & 0 & 1 & 1 \\
0 & 0 & 0 & 1 & 1 & 0 & 1 \\
\end{matrix}
\right) ^3 \left(
\begin{matrix}
1 \\
0 \\
0 \\
0 \\
0 \\
0 \\
0 \\
\end{matrix}
\right) = \left(
\begin{matrix}
3 & 5 & 5 & 3 & 5 & 3 & 3 \\
5 & 5 & 3 & 5 & 3 & 3 & 3 \\
5 & 3 & 5 & 3 & 3 & 3 & 5 \\
3 & 5 & 3 & 3 & 3 & 5 & 5 \\
5 & 3 & 3 & 3 & 5 & 5 & 3 \\
3 & 3 & 3 & 5 & 5 & 3 & 5 \\
3 & 3 & 5 & 5 & 3 & 5 & 3 \\
\end{matrix}
\right) \left(
\begin{matrix}
1 \\
0 \\
0 \\
0 \\
0 \\
0 \\
0 \\
\end{matrix}
\right) = \left(
\begin{matrix}
3 \\
\_ \\
\_ \\
\_ \\
\_ \\
\_ \\
\_ \\
\end{matrix}
\right)
$$

しかし計算が遅く TLE します。次にダブリングします。



## modint 余りを求める

「 $10^9 + 7$ で割った余りを求めてください。」 のような問題では、ac_library の `modint` が使えます。

```rust
type Mint = ac_library::ModInt1000000007;

let mut x = Mint::new(1);
x += Mint::new(2);
```

この問題では `modint` を使わなくても、`usize` で計算してそのつど `% 1000000007` すれば十分です。でも、余りの関心を構造体にお任せする方が簡単だと思います。


## 行列のかけ算

`Mint` を行列計算用のクレート `ndarray`, `nalgebra` で扱うことができれば、かけ算などが簡単なはずです。しかし `Mint` は `Zero` などの数値としてのトレイト境界を満たしていないため、使えません。

そこで `Vec<Vec<Mint>>` を行列として、このかけ算をする関数を用意します。

```rust
fn mat_mul(matrix0: &[Vec<Mint>], matrix1: &[Vec<Mint>]) -> Vec<Vec<Mint>> {
    let h0 = matrix0.len();
    let w0 = matrix0[0].len();
    let h1 = matrix1.len();
    let w1 = matrix1[0].len();
    assert!(h0 > 0);
    assert!(w0 > 0);
    assert!(h1 > 0);
    assert!(w1 > 0);
    assert_eq!(w0, h1);

    let mut matrix = vec![vec![Mint::new(0); w1]; h0];
    for i in 0..h0 {
        for j in 0..w1 {
            for k in 0..w0 {
                matrix[i][j] += matrix0[i][k] * matrix1[k][j];
            }
        }
    }
    matrix
}
```

## 行列のべき乗 (繰り返し二乗法, ダブリング)

行列 M に対して、べき乗 $M^10$ を計算したいとします。次の方法があります。

* 9回かけ算: $M \times M \times M \times M \times M \times M \times M \times M \times M \times M$ 
* 2の累乗単位でまとめる: $((M^2)^2 \times M)^2$

後者のような計算は `pow()` を提供している各ライブラリーでは行っているはずです。たとえば ac-library-rs の modint では次の実装になっています。

https://github.com/rust-lang-ja/ac-library-rs/blob/master/src/modint.rs#L674-L685

かけ算の対象を `Mint` から `Vec<Vec<Mint>>` に切り替えると完成です。

```rust
fn mat_pow(matrix: &Vec<Vec<Mint>>, mut exp: usize) -> Vec<Vec<Mint>> {
    let h = matrix.len();
    let w = matrix[0].len();
    assert!(h > 0);
    assert_eq!(h, w);
    assert_ne!(exp, 0);

    let mut base = matrix.clone();

    while exp & 1 == 0 {
        base = mat_mul(&base, &base);
        exp >>= 1;
    }
    if exp == 1 {
        return base;
    }

    let mut acc = base.clone();
    while exp > 1 {
        exp >>= 1;
        base = mat_mul(&base, &base);
        if exp & 1 == 1 {
            acc = mat_mul(&acc, &base);
        }
    }
    acc
}
```

## DP のダブリング

問題文にあるように 1000 までの余りを扱おうとすると、1000×1000 の行列が現れます。この計算は重いです。

同じダブリングの要領で、行列のべき乗ではなく DP 遷移を直接扱うと、オーダーが改善します。

(力不足で解説をうまく書けません。公式解説スライドをどうぞ。後日追記するかもしれません。)


## 実装例

### 行列のべき乗 (TLE)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/005_mat_pow_tle.rs

### DP のダブリング
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/005_doubling.rs
