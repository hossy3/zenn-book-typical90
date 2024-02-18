---
title: "020 - Log Inequality（★3）"
---

[020 \- Log Inequality（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_t)


# アルゴリズム

## 整数の累乗 (pow())

$\log_2 a < b \log_2 c$

を判定する問題です。右辺は

$b \log_2 c = \log_2 c^b$

と変形できます。そうすると、

$\log_2 a < \log_2 c^b$

$a < c^b$

を判定する問題になります。

$c^b$ がともに制約条件で最大の $13^{17}$ だったとしても 64bit 非負整数型の範囲内です。$16^{16} = 2^{64}$ よりも小さそうです。

* [usize \- Rust > pow()](https://doc.rust-lang.org/std/primitive.usize.html#method.pow)

累乗は `pow()` で求まります。 指数部は `u32` 型です。`usize` はそのままでは使えません。

# 実装例

## 整数の累乗 (pow())
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/020_pow.rs

