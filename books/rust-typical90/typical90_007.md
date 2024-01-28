---
title: "007 - CP Classes（★3）"
---

[007 \- CP Classes（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_g)

# アルゴリズム
## 二分探索 (Vec::binary_search())

クラスのレーティングが `v = vec![10, 20, 30]` の場合、生徒のレーティングに対する不満度を考えます。

|生徒＼候補|..|10|..|20|..|30|..|v.binary_search()|不満度|
|---|---|---|---|---|---|---|---|---|---|
|3|❌|🔍||||||`Err(&0)`|3|
|10||✅||||||`Ok(&0)`|0|
|18||🔍|❌|🔍||||`Err(&1)`|2|
|20||||✅||||`Ok(&1)`|0|
|23||||🔍|❌|🔍||`Err(&2)`|3|
|30||||||✅||`Ok(&2)`|0|
|33||||||🔍|❌|`Err(&3)`|3|

* [Vec in std::vec \- Rust > binary_search()](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.binary_search)

`v` が昇順に並んでいるときに、二分探索 `v.binary_search()` が使えます。ちょうどの場所 `Ok` が返ってくる場合は不満度ゼロ、間の場所 `Err` が返ってくる場合は隣の不満度のうち小さな方を返せば良いです。

# Tips

## シャドーイング

* [スコープとシャドーイング \- Rust By Example 日本語版](https://doc.rust-jp.rs/rust-by-example-ja/variable_bindings/scope.html)

```rust
for b in b {
    let result = f(&a, b);
    println!("{result}");
}
```

私はこのように「問題文中の配列 B」も、「配列 B の個々の要素」も両方変数 `b` を割り当てます。シャドーイングです。

次のような利点があります:

* 元の配列全体を間違って使わないように隠したい
  * 個々の値を見ているときに、元の配列 B を見たくなることはないはず
* 変数名を考える手間が減る
* 型が異なるので、間違って使ったとしてもコンパイルエラーになるはず

同じ名前の変数があると読みづらいという話もあり、プログラミング言語によってはサポートしていないこともあります。 Rust での競技プログラミングではアリだと思います。


# 実装例

## 二分探索 (Vec::binary_search())
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/007_binary_search.rs
