---
title: "061 - Deck（★2）"
---

[061 \- Deck（★2）](https://atcoder.jp/contests/typical90/tasks/typical90_bi)


# アルゴリズム
## Vec

例題1を考えます

|t|x|意味|結果の配列|出力|
|---|---|---|---|---|
||||`[]`||
|1|2|先頭に 2 を追加|`[2]`||
|1|1|先頭に 1 を追加|`[1, 2]`||
|2|3|末尾に 3 を追加|`[1, 2, 3]`||
|3|1|1番目は?||1|
|3|2|2番目は?||2|
|3|3|3番目は?||3|

配列の値を「末尾に」動的に追加削除したいときには、`Vec` が効率的です。 $O(1)$ で行えます。しかしこの問題のように「先頭にも」動的に追加削除したいときは、とても効率が悪いです。

たとえば 1万要素の配列 `[1, 2, ..., 10000]` の先頭に `10001` を追加して `[10001, 1, 2, ..., 10000]` としたいときに、範囲内のすべての値をコピーすることになります。 $O(N)$ です。

|Vecのi|0|1|2|...|9999|10000|
|---|---|---|---|---|---|---|
|以前の値|1|2|3|...|10000||
|新しい値|⚡10001|⚡1|⚡2|...|⚡9999|⚡10000|

最悪ケースとしてずっと先頭に値の追加を行うと、N 回すべて値の書き換えが行われます。 $O(N^2)$ です。とても遅いです。

コード例です:

```rust
    let mut v = vec![];
    for (t, x) in tx {
        match t {
            1 => {
                v.insert(0, x);
            }
            2 => {
                v.push(x);
            }
            3 => {
                println!("{}", v[x - 1]);
            }
            _ => unreachable!(),
        }
    }
```

末尾に追加する `push(x)` に比べ、先頭に追加 (0番目の場所に挿入) する `insert(0, x)` はいかにも使いづらいです。

## VecDeque (デック)

VecDeque では先頭への追加削除も高速に行えます。

|Vecdeque||||||
|---|---|---|---|---|---|
|以前の値||1|2|...|10000|
|新しい値|⚡10001|1|2|...|10000|

値の参照速度は Vec も VecDeque も同じ `O(1)` です。一瞬です。これなら求まります。

```diff rust
-   let mut v = vec![];
+   let mut v = VecDeque::new();
    for (t, x) in tx {
        match t {
            1 => {
-               v.insert(0, x);
+               v.push_front(x);
            }
            2 => {
-               v.push(x);
+               v.push_back(x);
            }
            3 => {
                println!("{}", v[x - 1]);
            }
            _ => unreachable!(),
        }
    }
```

vec と似たインターフェースです。先頭への追加が `push_front(x)` と、自然に使えるようになっています。先頭への追加も $O(1)$ でできます。


# 実装例

## Vec
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/061_vec.rs

いちおう時間内に AC できます。

## VecDeque
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/061_deque.rs

