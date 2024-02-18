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

```rust
match a.binary_search(&b) {
    Ok(_) => 0,
    Err(i) => {
        if i == 0 {
            a[0] - b
        } else if i == a.len() {
            b - a[i - 1]
        } else {
            (b - a[i - 1]).min(a[i] - b)
        }
    }
}
```

「`v` が昇順に並んでいるときに」ということで、事前に入力をソートしておきます。

```rust
a.sort();
```

これで解けます。


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

## usize::saturating_sub()

先ほどのコードを見直します。

```rust
if i == 0 {
    a[0] - b
} else if i == a.len() {
    b - a[i - 1]
} else {
    (b - a[i - 1]).min(a[i] - b)
}
```

いつも両隣を見るように、 `i - 1` すれば良いように思います。整数型と非負整数型をあまり区別しない言語ならこのように書けます。

```rust
let x0 = a[(i - 1).max(0)];
let x1 = a[i.min(a.len() - 1)];
((b - x0).abs(x0)).min((b - x1).abs(x1))
```

しかし、Rust で `i` を配列アクセスのために非負整数型 `usize` にしていると、 `i == 0` のときに引き算できないという理由で panic します。

こんな感じで書けば panic しませんが、読みづらいです。

```rust
let x0 = a[(i as i64 - 1).max(0) as usize];
```

* [usize \- Rust > saturating_sub()](https://doc.rust-lang.org/std/primitive.usize.html#method.saturating_sub)

飽和する引き算を使うと、`as` よりスッキリ書けます。

```rust
let x0 = a[i.saturating_sub(1)];
```

`saturating_sub()` と `wrapping_add_signed()` を競技プログラミングで良く使う印象があります。


# 実装例

## 二分探索 (Vec::binary_search())
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/007_binary_search.rs

## 二分探索 (Vec::binary_search(), saturating_sub())
https://github.com/hossy3/atcoder-solutions/blob/abee46dd3a49158b6cedd2eb8803981d6981c8cd/atcoder/typical90/src/bin/007_binary_search_compact.rs#L6-L10

この行以外は上と同じです。
