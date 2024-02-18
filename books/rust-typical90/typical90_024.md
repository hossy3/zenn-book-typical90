---
title: "024 - Select +／- One（★2）"
---

[024 \- Select \+／\- One（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_x)


# アルゴリズム

## 偶奇性

例題1 a `[1, 3]` を b `[2, 1]` に 5手で変更できるかを考えます。

a を b に最短手順で移動するには `[+1, -2]` 3手かかります。残り 2手余裕があります。

2手を使って同じ場所に戻るには `[+1 -1, 0]` や `[0, -1 +1]` など往復すれば良いです。

今回は 2手余りましたが、偶数ならこの 2手を繰り返すことで、同じように元の場所に戻れます。奇数では戻れません。

つまり、こちらで解けます。ループカウンター `i` で `a[i]`, `b[i]` と回しています。

```rust
let diff = (0..n).map(|i| a[i].abs_diff(b[i])).sum();
let yes = (k >= diff) && ((k - diff) % 2 == 0);
```

# Tips

## iter::zip()

* [zip in std::iter \- Rust](https://doc.rust-lang.org/std/iter/fn.zip.html)

`a`, `b` の要素数が同じなら、 `iter::zip` でひとまとめにすると扱いやすいことがあります。

```rust
let diff = std::iter::zip(a, b).map(|(a, b)| a.abs_diff(b)).sum();
```

コード量は増えています。その代わり「`a` の要素数は `n` であっていたか」「ループカウンターを間違えて `a[i]` `b[j]` としていないか」のような心配がなくなります。

## num_integer::is_even()

* [Integer in num\_integer \- Rust > is_even()](https://docs.rs/num-integer/latest/num_integer/trait.Integer.html#tymethod.is_even)

```rust
let yes = (k >= diff) && (k - diff).is_even();
```

`% 2 == 0` を書き換えられます。


# 実装例

## ループカウンター
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/024_loop_counter.rs

## iter::zip()
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/024_zip.rs

## iter::zip() + num_integer::is_even()
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/024_zip_even.rs
