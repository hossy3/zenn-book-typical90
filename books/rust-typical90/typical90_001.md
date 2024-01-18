---
title: "001 - Yokan Party（★4）"
---

[001 \- Yokan Party（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_a)


# アルゴリズム

## 二分探索 (partition_point)

|1|2|3|4|5|6|7|8|9|10|11|12|13|14|15|16|17|18|19|20|21|22|23|24|25|26|27|28|29|30|31|32|33|34|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|✅|✅|✅|✅|✅|✅|✅|✅|✅|✅|✅|✅|✅|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|❌|


「ようかんの長さを手前から見て X 以上になったところで分けたときに、ピース数を K + 1 以上にできるか」という問題を考えます。試しに切ってみるのは簡単です。これができれば、スコア X になります。

分けられる✅と、分けられない❌の境界を見つければ、それが最大スコアとなります。二分探索で求まります。

二分探索では値の上下関係を調べる `lower_bound()`, `upper_bound()` を使うことが多いです。この問題では二値が求まれば良いですから、どこで ✅❌ が切り替わるか `partition_point()` が分かれば十分です。

* [Vec in std::vec \- Rust > partition_point](https://doc.rust-lang.org/std/vec/struct.Vec.html#method.partition_point)

Rust では `Vec` に対して `partition_point()` を呼び出せます。 `let v = vec![1, 2, 3, ...];` みたいなものに対して調べる感じです。しかし残念ながらこの問題ではようかんの長さが $10^9$ あります。このサイズの Vec はメモリが足りません。

そこで、`Range` に対しても `partition_point()` できるようなコードを、`Vec` の実装を写経して作ります。いちど作れば、次回からはこのコードをそのまま使いまわせます。

```rust
trait PartitionPoint {
    fn partition_point<P>(&self, pred: P) -> usize
    where
        P: Fn(usize) -> bool;
}

impl<T: std::ops::RangeBounds<usize>> PartitionPoint for T {
    /// Returns the index of the partition point according to the given predicate
    /// (the index of the first element of the second partition).
    ///
    /// # Examples
    /// ```
    /// assert_eq!((0..).partition_point(|i| i < 10), 10);
    /// assert_eq!((0..1000).partition_point(|_| false), 0);
    /// assert_eq!((10..=1000).partition_point(|_| true), 1001);
    /// ```
    fn partition_point<P>(&self, pred: P) -> usize
    where
        P: Fn(usize) -> bool,
    {
        let mut r = match self.end_bound() {
            std::ops::Bound::Included(r) => r + 1,
            std::ops::Bound::Excluded(r) => *r,
            std::ops::Bound::Unbounded => usize::MAX,
        };
        let mut l = match self.start_bound() {
            std::ops::Bound::Included(l) => *l,
            std::ops::Bound::Excluded(l) => l + 1,
            std::ops::Bound::Unbounded => 0,
        };

        assert!(l <= r);
        while l != r {
            let mid = l + (r - l) / 2;
            if pred(mid) {
                l = mid + 1;
            } else {
                r = mid;
            }
        }
        l
    }
}
```

# Tips

## 単体テスト

「ようかんの長さを手前から見て X 以上になったところで分けたときに、ピース数を K + 1 以上にできるか」 というコードは 10行くらいで書けるはずです。しかし短くても、うっかりバグを埋め込んでしまうかもしれません。長いコードを書くときには、「ここはぜったい大丈夫」「ここは新しく書いたところだから心配」のようにコード品質を区別したいです。

Rust ではコードの横に単体テストを気軽に書けます。単体テストが通っていれば安心です。

```rust
fn f(l: usize, k: usize, v: &[usize]) -> bool {
    let mut num = 0;
    let mut cur = 0;
    for &x in v {
        cur += x;
        if cur >= l {
            num += 1;
            cur = 0;
        }
    }
    num > k
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]
    fn test_func() {
        assert_eq!(f(12, 2, &[8, 5, 13, 8]), true);
        assert_eq!(f(13, 2, &[8, 5, 13, 8]), true);
        assert_eq!(f(14, 2, &[8, 5, 13, 8]), false);
    }
}
```

入力例 1 の答えが 13 ですので、その前後 1つを試してみました。

時間制限のあるコンテスト中にテストを書くのは無駄そうにも思います。でも私の場合は、 printf デバッグやステップ実行で 5分で解消しなければ単体テストを書くのに移った方が、結果的に早く解決しがちです。

* [rust-analyzer > User Manual > Magic Completions](https://rust-analyzer.github.io/manual.html#magic-completions)

rust-analyzer を入れた開発環境で `tmod` (test module) と入力すると、モジュールのテストコードのテンプレートが入ります。おすすめです。


## 実装例

### Range
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/001_range.rs

### Vec (RE)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/001_vec_re.rs

Range との違いは `PartitionPoint` とこちらです:

```diff rust
-   let result = (1..=l).partition_point(|i| f(i, k, &v)) - 1;
+   let v0: Vec<usize> = (1..=l).collect();
+   let result = v0.partition_point(|&i| f(i, k, &v));
```