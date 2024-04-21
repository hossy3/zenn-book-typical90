---
title: "038 - Large LCM（★3）"
---

[038 \- Large LCM（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_al)


# アルゴリズム

## u128 の lcm

最小公倍数 LCM を求める問題です。64bit 整数型と 64bit 整数型の LCM は 64bit 整数型の範囲に収まらないことがあります。

`num_integer` クレートに、 `u128` 型の LCM が用意されています。これを呼べば終了です。

* [lcm in num\_integer \- Rust](https://docs.rs/num-integer/latest/num_integer/fn.lcm.html)

```rust
input! {
    a: u128,
    b: u128,
}
let lcm = a.lcm(&b);
if lcm > 10u128.pow(18) {
    println!("Large");
} else {
    println!("{lcm}");
}
```

## u64 の範囲でオーバーフローを避ける

最小公倍数は大きな値になりますが、最大公約数なら大きくなりません。先に最大公約数を求めて、$a / gcd * b > 10^{18}$ を $b > 10^{18} / (a / gcd)$ かどうかで判定すれば良いです

```rust
let gcd = a.gcd(&b);
if (b, 0) > 10u64.pow(18).div_mod_floor(&(a / gcd)) {
    println!("Large");
} else {
    let lcm = a / gcd * b;
    println!("{lcm}");
}
```

## gcd ライブラリー無し

GCD はライブラリーを使わなくても、ユークリッドの互除法で簡単に求められます。 [022 - Cubic Cake（★2）](./typical90_022) でも取り上げました。

```rust
fn gcd(a: u128, b: u128) -> u128 {
    if b == 0 {
        a
    } else {
        gcd(b, a % b)
    }
}
```

# 実装例

## u128
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/038_lcm_u128.rs

## u64
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/038_gcd_u64.rs

