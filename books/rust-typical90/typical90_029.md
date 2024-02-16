---
title: "029 - Long Bricks（★5）"
---

[029 \- Long Bricks（★5）](https://atcoder.jp/contests/typical90/tasks/typical90_ac)


# アルゴリズム

https://qiita.com/hossie/items/f8cf08824c8a5acdef10

1年半前に Qiita に記事を書いていました。セグメント木などのデータ構造の仕組みを含めて。今回は解き方だけです。


## 遅延セグメント木

|区間|1|2|3|🟦区間内最大高さ|🔴高さ|
|---|---|---|---|---|---|
|[1, 2]|🔴|🔴||0|1|
|[2, 2]|<br>🟦|🔴<br>🟦||1|2|
|[2, 3]|<br><br>🟦|🔴<br>🟦<br>🟦|🔴<br>⬜<br>⬜-|2|3|
|[3, 3]|<br><br><br>🟦|<br>🟦<br>🟦<br>🟦|🔴<br>🟦<br>⬜<br>⬜|3|4|
|[1, 2]|🔴<br>⬜<br>⬜<br>🟦|🔴<br>🟦<br>🟦<br>🟦|🟦<br>🟦<br>⬜<br>⬜|3|4|


区間 [2, 3] の中で最大高さ `2` を求めて、区間 [2, 3] の高さをすべて `2+1` で上書きすることで解けます。

このように「区間の値確認」と「区間の更新」両方を行いたいときは、遅延セグメント木が使えます。 遅延適用成分が複雑な場合は設定が難しいですが、このように新しい値が最大値になるなら簡単です。 `MaxAdd`, `MaxMax` はよく使います。

```rust
struct MaxMax;
impl MapMonoid for MaxMax {
    type M = Max<usize>;
    type F = usize;

    fn identity_map() -> Self::F {
        0
    }
    fn mapping(&f: &Self::F, &x: &<Self::M as Monoid>::S) -> <Self::M as Monoid>::S {
        f.max(x)
    }
    fn composition(&f: &Self::F, &g: &Self::F) -> Self::F {
        f.max(g)
    }
}

fn main() {
    input! {
        w: usize,
        lr: [(Usize1, Usize1)],
    }

    let mut segtree: LazySegtree<MaxMax> = vec![0usize; w].into();
    for &(l, r) in &lr {
        let h = segtree.prod(l..=r) + 1;
        segtree.apply_range(l..=r, h);
        println!("{h}");
    }
}
```

## BTreeMap

高さが変わる境界の値を `BTreeMap` で覚える、という方法もあります。二分探索は十分に早いです。高さが変わる箇所が多いと区間全体を調べるのが遅そうにも思いますが、その後同じ高さに揃ってまた境界の数が減りますので、問題なくなります。

|区間|1|2|3|(左端, 個数)[]|
|---|---|---|---|---|
|||||[(1, 0), (4, 0)]|
|[1, 2]|🔴|🔴||[(1, 1), (3, 0), (4, 0)]|
|[2, 2]|<br>🟦|🔴<br>🟦||[(1, 1), (2, 2), (3, 0), (4, 0)]|
|[2, 3]|<br><br>🟦|🔴<br>🟦<br>🟦|🔴<br>⬜<br>⬜-|[(1, 1), (2, 3), (4, 0)]|
|[3, 3]|<br><br><br>🟦|<br>🟦<br>🟦<br>🟦|🔴<br>🟦<br>⬜<br>⬜|[(1, 1), (2, 3), (3, 4), (4, 0)]|
|[1, 2]|🔴<br>⬜<br>⬜<br>🟦|🔴<br>🟦<br>🟦<br>🟦|🟦<br>🟦<br>⬜<br>⬜|[(1, 4), (4, 0)]|

* [BTreeMap in std::collections \- Rust > range()](https://doc.rust-lang.org/std/collections/struct.BTreeMap.html#method.range)

[006 - Smallest Subsequence（★5）](./typical90_006) でも使った `BTreeMap::range()` を使います。

同じ高さの値が残っていると邪魔になります。そこで `l..=r` 範囲内のものを `Vec<>` に詰めなおし、まとめて削除します。 map の区間内をループしながら消すことはできません。どこまでループを回していたか分からなくなりますので。

```rust
let mut m = BTreeMap::new();
m.insert(1usize, 0usize);
m.insert(w + 1, 0);

for &(l, r) in &lr {
    let (_, &l0) = m.range(..=l).last().unwrap();
    let (_, &r0) = m.range(..=(r + 1)).last().unwrap();

    let h = m.range(l..=r).fold(l0, |acc, (_, &h)| acc.max(h)) + 1;
    let v = m.range(l..=r).map(|(&k, _)| k).collect_vec();
    for k in v {
        m.remove(&k);
    }

    m.insert(l, h);
    m.insert(r + 1, r0);

    println!("{h}");
}
```


# 実装例

## 遅延セグメント木
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/029_lazysegtree.rs

## BTreeMap
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/029_btreemap.rs
