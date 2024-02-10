---
title: "022 - Cubic Cake（★2）"
---

[022 \- Cubic Cake（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_v)


# アルゴリズム

## 最大公約数 Greatest Common Divisor (GCD)

直方体を切って立方体にします。縦・横・高さの最大公約数の長さにできます。たとえば (2, 2, 4) の場合、最大公約数 2 になります。

最大公約数はよく使いますので、誰かが用意しているはずです。 Rust では `num-integer` が使えます。

* [Integer in num\_integer \- Rust > gcd()](https://docs.rs/num-integer/latest/num_integer/trait.Integer.html#tymethod.gcd)

```rust
let w = a.gcd(&b).gcd(&c);
```

ユークリッドの互除法で実装するのも簡単です。

```rust
fn gcd(a: usize, b: usize) -> usize {
    if b == 0 {
        a
    } else {
        gcd(b, a % b)
    }
}
```

# 実装例

## num_integer::gcd()
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/022_gcd_num_integer.rs

## ユークリッドの互除法
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/022_gcd_num.rs
