---
title: "055 - Select 5（★2）"
---

[055 \- Select 5（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_bc)


# アルゴリズム

## 全探索

数列から 5つの数を取り出してかけ算したときに、`(v[0] * v[1] * v[2] * v[3] * v[4]) % p == q` となる組み合わせがいくつあるか、という問題です。

個数 N の制約は $N\le100$ です。最大で、100個の数字から 5個を選ぶすべての組み合わせがあります。

$$ _{100}C_5 = \frac{100 \times 99 \times 98 \times 97 \times 96}{5 \times 4 \times 3 \times 2 \times 1} = 75,287,520 $$

1000万通り以内です。全通り調べても時間内に間に合います。

```rust
fn main() {
    input! {
        n: usize,
        p: usize,
        q: usize,
        a: [usize; n]
    }
    // count を計算
    println!("{count}");
}
```

count を計算する方法は多数あります。

## 5重ループ

8個から 5個選ぶとします、左から順に 👍 を選び、次の候補はその右から選びます。

最初の 1つを選ぶ候補 🔍 は 8 - (5 - 1) = 4 個になります。一番右端を選んだとしても、その右から残り 4つを選べるように、です。

配列 [0, 1, 2, 3, 4, 5, 6, 7] から (1, 2, 5, 6, 7) 番目を選び、10 で割った余りが 0 になるものを探すとします。次の表のようになります。

||0|1|2|3|4|5|6|7|x|
|---|---|---|---|---|---|---|---|---|---|
||🔍|🔍|🔍|🔍|❌|❌|❌|❌|
|i0 = 1||👍|||||||1 % 10 = 1|
||||🔍|🔍|🔍|❌|❌|❌|
|i1 = 2|||👍||||||(1 * 2) % 10 = 2|
|||||🔍|🔍|🔍|❌|❌|
|i2 = 5||||||👍|||(2 * 5) % 10 = 0|
||||||||🔍|❌|
|i3 = 6|||||||👍||(0 * 6) % 10 = 0|
|||||||||🔍|
|i4 = 7||||||||👍|(0 * 7) % 10 = 0|

(1 * 2 * 5 * 6 * 7) を 10で割った余りと、1つかけ算するたびに 10で割った余りは同じです。大きな数のかけ算を繰り返すと `u64` 型などの範囲におさまらずずにオーバーフローします。そこで毎回余りを取ります。

コードです:

```rust
let mut count = 0usize;
for i0 in 0..(n - 4) {
    for i1 in (i0 + 1)..(n - 3) {
        let x = (a[i0] * a[i1]) % p;
        for i2 in (i1 + 1)..(n - 2) {
            let x = (x * a[i2]) % p;
            for i3 in (i2 + 1)..(n - 1) {
                let x = (x * a[i3]) % p;
                for i4 in (i3 + 1)..n {
                    let x = (x * a[i4]) % p;
                    if x == q {
                        count += 1;
                    }
                }
            }
        }
    }
}
```

これで AC できます。


## 再帰

5重ループだと似たコードの繰り返しが現れます。再帰でも解けます。

|rest|x|&a|0|1|2|3|4|5|6|7|次の x|
|---|---|---|---|---|---|---|---|---|---|---|---|
|4|1|[0, 1, 2, 3, 4, 5, 6, 7]|🔍|🔍|🔍|🔍|❌|❌|❌|❌|
|||||👍|||||||1 % 10 = 1|
|3|1|[2, 3, 4, 5, 6, 7]|||🔍|🔍|🔍|❌|❌|❌|
||||||👍||||||(1 * 2) % 10 = 2|
|2|2|[3, 4, 5, 6, 7]||||🔍|🔍|🔍|❌|❌|
|||||||||👍|||(2 * 5) % 10 = 0|
|1|0|[6, 7]|||||||🔍|❌|
||||||||||👍||(0 * 6) % 10 = 0|
|0|0|[7]||||||||🔍|
|||||||||||👍|(0 * 7) % 10 = 0|


コードです:

```rust
fn f(cur: usize, rest: usize, p: usize, q: usize, a: &[usize]) -> usize {
    if rest == 0 {
        a.iter().filter(|&&x| (cur * x) % p == q).count()
    } else {
        let mut count = 0;
        for (i, &x) in a[..(a.len() - rest)].iter().enumerate() {
            count += f((cur * x) % p, rest - 1, p, q, &a[(i + 1)..]);
        }
        count
    }
}
```

```rust
let cur = 1;
let rest = 4;
let count = f(cur, rest, p, q, &a);
```

繰り返しは減りました。ループの方が楽かもしれません。


## 組み合わせ

* [Itertools in itertools \- Rust > combinations()](https://docs.rs/itertools/latest/itertools/trait.Itertools.html#method.combinations)
* [Itertools in itertools \- Rust > tuple_combinations()](https://docs.rs/itertools/latest/itertools/trait.Itertools.html#method.tuple_combinations)

Itertools の組み合わせ関数を使うと、5重ループも再帰もなく簡単に書けます。

```rust
let count = a
    .iter()
    .combinations(5)
    .filter(|v| v.iter().fold(1, |acc, &&x| (acc * x) % p) == q)
    .count();
```

```rust
let count = a
    .iter()
    .tuple_combinations()
    .filter(|x: &(&_, &_, &_, &_, &_)| {
        (((((((x.0 * x.1) % p) * x.2) % p) * x.3) % p) * x.4) % p == q
    })
    .count();
```

しかしループや再帰に比べると一桁遅いです。 TLE しないものの制限時間が危ない感じになってしまいます。[^1] どちらの方法でも速度は変わりませんでした。

[^1]: 組み合わせが求まってからまとめて計算すると、同じ計算の繰り返しが現れることが理由だと思います。組み合わせをイテレーターで取り出せる `Vec` に組み立てるところでも追加コストがかかりそうです。


# 実装例

提出したときの実行時間:
||実行時間|
|---|---|
|5重ループ|257 ms|
|5重ループ (低速)|917 ms|
|再帰|245 ms|
|combinations|2367 ms|
|tuple_combinations|2696 ms|

## 5重ループ
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/055_5_loops.rs

## 5重ループ (低速)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/055_5_loops_slow.rs

## 再帰
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/055_recursive.rs

## combinations
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/055_combinations.rs

## tuple_combinations
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/055_tuple_combinations.rs
