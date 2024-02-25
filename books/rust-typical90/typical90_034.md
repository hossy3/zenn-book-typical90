---
title: "034 - There are few types of elements（★4）"
---

[034 \- There are few types of elements（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_ah)


# アルゴリズム

## 尺取り法

[009 - Three Point Angle（★6）](./typical90_009) でも行った尺取り法を使います。

例題 3、2種類以下の文字を含む最も広い区間を求める場合を考えます。

10 2
|半開区間|1|2|3|4|4|3|2|1|2|3|状態|OK|次|
|---|---|---|---|---|---|---|---|---|---|---|---|---|---|
|0..1|🔍1||||||||||`{1: 1}`|✅|伸ばす|
|0..2|🔍1|🔍2|||||||||`{1: 1, 2: 1}`|✅|伸ばす|
|0..3|🔍1|🔍2|🔍3||||||||`{1: 1, 2: 1, 3: 1}`|❌|縮める|
|1..3||🔍2|🔍3||||||||`{2: 1, 3: 1}`|✅|伸ばす|
|1..4||🔍2|🔍3|🔍4|||||||`{2: 1, 3: 1, 4: 1}`|❌|縮める|
|2..4|||🔍3|🔍4|||||||`{3: 1, 4: 1}`|✅|伸ばす|
|2..5|||🔍3|🔍4|🔍4||||||`{3: 1, 4: 2}`|✅|伸ばす|
|2..6|||🔍3|🔍4|🔍4|🔍3|||||`{3: 2, 4: 2}`|✅|伸ばす|
|2..7|||🔍3|🔍4|🔍4|🔍3|🔍2||||`{2: 1, 3: 2, 4: 2}`|❌|縮める|
|:||||||||||||||
|9..10||||||||||🔍3|`{3: 1}`|✅|伸ばす|

このように進めると、 ✅ 有効な区間の中で最も広いものが `2..6` と分かります。

次のようなコードになります。

```rust
let mut m = HashMap::<usize, usize>::new();
let mut r = 0;
let mut max_len = 0;
for l in 0..n {
    while r < n && (m.len() < k || (m.len() == k && m.contains_key(&a[r]))) {
        *m.entry(a[r]).or_insert(0) += 1;
        r += 1;
    }
    max_len = max_len.max(r - l);
    if let Some(x) = m.get_mut(&a[l]) {
        *x -= 1;
        if *x == 0 {
            m.remove(&a[l]);
        }
    };
}
println!("{max_len}");
```

* `HashMap` や `BTreeMap` に出現した要素の個数を詰めます
* 右に伸ばせる条件は、右端までたどり着いていない、かつ伸ばしても `k` 種類以内の場合です
* 右に伸ばすときに数を 1つ増やします
* 左を縮めるときに数を 1つ減らし、0になったときには map からキーを除きます


# Tips

## 尺取り法をイテレーターで行う

尺取り法のコードは短いですが、短い中に複数の処理が入ってきて、実装時に混乱しがちです。さらに問題設定によっては「左が右を追い越さないように」ということも考える必要があります。

イテレーターで先ほどの表の状態が順に渡ってくると扱いやすいです・

```rust
let max_len = TwoPointer::from((k, a))
    .map(|(l, r, valid)| if valid { r - l } else { 0 })
    .max()
    .unwrap();
```

この問題のような状態のある尺取り法は、これらを順に埋めていくと解けるはずです。

* 状態の型は何か
* 伸ばすときに状態をどう変更するか
* 縮めるときに状態をどう変更するか
* 有効な状態 (= 次にも伸ばしたい状態) は何か

```rust
type S = HashMap<usize, usize>;

trait TwoPointerOps {
    fn update_state_on_expand(&mut self);
    fn update_state_on_shrink(&mut self);
    fn is_valid(&self, state: &S) -> bool;
}

struct TwoPointer {
    n: usize,
    l: usize,
    r: usize,
    state: S,
    valid: bool,

    a: Vec<usize>,
    k: usize,
}

impl TwoPointerOps for TwoPointer {
    fn update_state_on_expand(&mut self) {
        *self.state.entry(self.a[self.r]).or_insert(0) += 1;
    }

    fn update_state_on_shrink(&mut self) {
        if let Some(x) = self.state.get_mut(&self.a[self.l]) {
            *x -= 1;
            if *x == 0 {
                self.state.remove(&self.a[self.l]);
            }
        };
    }

    fn is_valid(&self, state: &S) -> bool {
        state.len() <= self.k
    }
}
```

しかし、一般化は大変そうです。 009 で行った尺取り法と比較します。

||009|034|
|---|---|---|
|有効条件|区間が180°以内|区間の文字種類数|
|状態|不要|必要|
|区間の使用方法|区間の角度|有効な区間の長さ|

使いやすい尺取り法ライブラリを書くなら、それだけで記事一本分になりそうです。欲しいです。


# 実装例

## 尺取り法
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/034_two_pointer.rs

## 尺取り法 / iterator
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/034_two_pointer_iter.rs

