---
title: "027 - Sign Up Requests （★2）"
---

[027 \- Sign Up Requests （★2）](https://atcoder.jp/contests/typical90/tasks/typical90_aa)


# アルゴリズム

## 集合 set

集合 set で文字列を管理すれば良いです。新規文字列かどうか、簡単に分かります。

|番号|元の set|追加|追加成功?|
|---|---|---|---|
|1|[]|"e869120"|✅|
|2|["e869120"]|"atcoder"|✅|
|3|["atcoder", "e869120"]|"e869120"|❌|
|4|["atcoder", "e869120"]|"square1001"|✅|
|5|["atcoder", "e869120", "square1001"]|"square1001"|❌|

rust では、ハッシュ `HashSet` や、二分木 `BTreeSet` が使えます。

* [ハッシュ集合 \- Rust By Example 日本語版](https://doc.rust-jp.rs/rust-by-example-ja/std/hash/hashset.html)
* [HashSet in std::collections::hash\_set \- Rust](https://doc.rust-lang.org/std/collections/hash_set/struct.HashSet.html)
* [BTreeSet in std::collections \- Rust](https://doc.rust-lang.org/std/collections/struct.BTreeSet.html)

`HashSet` と `BTreeSet` は、速度・消費メモリ・順序の扱いについて違いがあります。今回の問題についてはどちらでも良いです。

`HashSet<&String>` で重複をはじくなら、次のように書きます。 `h.insert(s)` が `true` になるのは追加成功 ✅ のときです。

```rust
input! {
    n: usize,
    s: [String; n],
}
let mut h = HashSet::new();
for (i, s) in s.iter().enumerate() {
    if h.insert(s) {
        println!("{}", i + 1);
    }
}
```

文字の配列で重複をはじく `HashSet<&Vec<char>>` でも良いです。コードが 1行変わります。

```rust
input! {
    n: usize,
    s: [Chars; n],
}
```

# 実装例

## HashSet<&String>
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/027_string.rs

## HashSet<&Vec<char>>
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/027_chars.rs

## BTreeSet<&String>
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/027_string_btreeset.rs
