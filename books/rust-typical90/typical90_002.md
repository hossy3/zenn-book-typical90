---
title: "002 - Encyclopedia of Parentheses（★3）"
---

[002 \- Encyclopedia of Parentheses（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_b)


# アルゴリズム

## 組み合わせ (itertools::combinations())

4文字でかっこの対応関係が成り立つか確認する場合、4文字中 2文字が `(`, 残り 2文字が `)` となるものを全列挙すれば良いです。

これは順列・組み合わせの考え方で行えます。`0, 1, 2, 3` 番目のうち 2つ選び、それが `(` として、正しいかどうか確認すれば良いです。

|組み合わせ|文字列|結果|
|---|---|---|
|0, 1|(())|✅|
|0, 2|()()|✅|
|0, 3|())(|❌|
|1, 2|)(()|❌|
|1, 3|)()(|❌|
|2, 3|))((|❌|

n個の数字から n/2個を選ぶ組み合わせは、`itertools::combinations()` を次のように呼ぶとすべて求まります。

[Itertools in itertools \- Rust > combinations()](https://docs.rs/itertools/latest/itertools/trait.Itertools.html#method.combinations)

```rust
    for v in (0..n).combinations(n / 2) {
        // 条件を満たしていれば 1行出力
    }
```

先頭 1文字目だけ見るとその場で間違っていると分かる組み合わせもあります。無駄に調べるところもありますが、最大でも 20文字なら ${}_20 \mathrm{ C }_10 = 184756$ 通り調べれば良いですので、十分でしょう。

### 条件の確認方法

かっこの入れ子数が途中でマイナスにならないかを調べれば良いです。

|組み合わせ|文字列|[0]|[1]|[2]|[3]|結果|
|---|---|---|
|0, 1|(())|0 + 1 = 1|1 + 1 = 2|2 - 1 = 1|1 - 1 = 0 ✅|✅|
|0, 2|()()|0 + 1 = 1|1 - 1 = 0|0 + 1 = 1|1 - 1 = 0 ✅|✅|
|0, 3|())(|0 + 1 = 1|1 - 1 = 0|0 - 1 = -1 ❌||❌|
|1, 2|)(()|0 - 1 = -1 ❌||||❌|
|1, 3|)()(|0 - 1 = -1 ❌||||❌|
|2, 3|))((|0 - 1 = -1 ❌||||❌|

```rust
fn f(v: &[bool]) -> bool {
    let mut level = 0usize;
    for &b in v {
        if b {
            level += 1;
        } else {
            if level == 0 {
                return false;
            }
            level -= 1;
        }
    }

    level == 0 // return (level == 0); と同じ
}
```

終端の入れ子の深さは、組み合わせでは 0 になるはずです。つぎのbit全探索では `((((` と最後に深さが 0にならないことがありますので、ついでに確認しています。


## bit全探索

|10進数|2進数|文字列|結果|
|---|---|---|---|
|0|0b0000|((((|❌|
|1|0b0001|((()|❌|
|2|0b0010|(()(|❌|
|3|0b0011|(())|✅|
|4|0b0100|()((|❌|
|5|0b0101|()()|✅|
|6|0b0110|())(|❌|
|7|0b0111|()))|❌|
|8|0b1000|)(((|❌|
|9|0b1001|)(()|❌|
|10|0b1010|)()(|❌|
|11|0b1011|)())|❌|
|12|0b1100|))((|❌|
|13|0b1101|))()|❌|
|14|0b1110|)))(|❌|
|15|0b1111|))))|❌|

ビットの 0 が `(`, 1 が `)` に対応しているというように考えます。組み合わせより数が多いですが、 全部で $2^20 = 1024^2$ 通りしかないですから、やはり十分です。

```rust
    for i in 0..(1 << n) {
        // 条件を満たしていれば 1行出力
    }
```

`i` の `k` ビット目が立っているかどうかは `i & (1 << k) != 0` とすることで調べられます。書き間違える不安がある私は、 `i.bit_test(k)` と書けるスニペットを登録しています。

```rust
trait BitTest {
    fn bit_test(&self, i: usize) -> bool;
}

impl BitTest for usize {
    fn bit_test(&self, i: usize) -> bool {
        self & (1 << i) != 0
    }
}
```

## 再帰

再帰でも解けます。必要な探索だけ行いますので速く、コードも短くなります。


# 実装例

## 組み合わせ (itertools::combinations())
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/002_combination.rs

## bit全探索
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/002_bit_search.rs

## 再帰
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/002_recursive.rs

