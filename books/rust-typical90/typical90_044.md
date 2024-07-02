---
title: "044 - Shift and Swapping（★3）"
---

[044 \- Shift and Swapping（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_ar)


# アルゴリズム

## 両端キュー (VecDeque)

長さ N の整数列に対して、入力で指示された通りの 3種類の操作を行う、という問題です。

|入力|操作|高速に行いたいこと|
|---|---|---|
|T = 1, x, y|$A_x$ と $A_y$ を入れ替える|ランダムアクセス|
|T = 2, x, y|数列を右シフトし、 $A_N, A_1, A_2, ..., A_{N-1}$ のようにする|末尾から1つ除く、先頭に 1つ追加|
|T = 3, x, y|$A_x$ を出力する|ランダムアクセス|

* [VecDeque in std::collections \- Rust](https://doc.rust-lang.org/std/collections/struct.VecDeque.html)

ランダムアクセスと両端の追加削除を行いたいとなると、 `VecDeque` (C++ で言う `std::deque`) の出番です。

次のように、そのまま解けます。

|入力 T x y|0|1|2|3|4|5|6|7|
|---|---|---|---|---|---|---|---|---|
||6|17|2|4|17|19|1|7|
|2➡️ 0 0|➡️7|➡️6|➡️17|➡️2|➡️4|➡️17|➡️19|➡️1|
|1⚡ 7 2|7|⚡19|17|2|4|17|⚡6|1|
|1⚡ 2 6|7|⚡17|17|2|4|⚡19|6|1|
|1⚡ 4 5|7|17|17|⚡4|⚡2|19|6|1|
|3🔍 4 0|7|17|17|🔍4|2|19|6|1|

```rust
fn main() {
    input! {
        n: usize,
        q: usize,
        a: [usize; n],
        txy: [(u8, usize, usize); q],
    }
    let mut v = VecDeque::from(a);
    for (t, x, y) in txy {
        match t {
            1 => {
                v.swap(x - 1, y - 1);
            }
            2 => {
                let x = v.pop_back().unwrap();
                v.push_front(x);
            }
            3 => {
                println!("{}", v[x - 1]);
            }
            _ => unreachable!(),
        }
    }
}
```

## 配列 (Vec) (TLE)

`Vec` にも先頭に要素を追加する仕組みがあります。しかし TLE になります。配列の要素のコピーを、要素数分繰り返すためです。

```diff rust
fn main() {
    input! {
        n: usize,
        q: usize,
        a: [usize; n],
        txy: [(u8, usize, usize); q],
    }
-   let mut v = VecDeque::from(a);
+   let mut v = a.clone();
    for (t, x, y) in txy {
        match t {
            1 => {
                v.swap(x - 1, y - 1);
            }
            2 => {
-               let x = v.pop_back().unwrap();
-               v.push_front(x);
+               let x = v.pop().unwrap();
+               v.insert(0, x);
            }
            3 => {
                println!("{}", v[x - 1]);
            }
            _ => unreachable!(),
        }
    }
}
```

## 配列 (Vec) とオフセット

この問題では、操作を行っても長さは変化しません。 `Vec` の要素コピーを行わないようにオフセットを組み合わせる形にする、という解き方もあります。

「2➡️」 を、次から操作するインデックスを `-1` する、というようにみなし、オフセットを -1 します。 Rust では非負のインデックス指定を引き算するのは相性が悪いため、代わりに `N - 1` を足すことにします。

「1⚡ 7 2」 6番目と 1番目を入れ替える という指示が、オフセット `7` のときには `(6 + 7) % 8` 番目と  `(1 + 7) % 8` 番目を入れ替える、というように変わります。ひと手間かかります。

|入力 T x y|offset|0|1|2|3|4|5|6|7|
|---|---|---|---|---|---|---|---|---|---|
||0|6|17|2|4|17|19|1|7|
|2➡️ 0 0|➡️7|6|17|2|4|17|19|1|7|
|1⚡ 7 2|7|⚡19|17|2|4|17|⚡6|1|7|
|1⚡ 2 6|7|⚡17|17|2|4|⚡19|6|1|7|
|1⚡ 4 5|7|17|17|⚡4|⚡2|19|6|1|7|
|3🔍 4 0|7|17|17|🔍4|2|19|6|1|7|


```diff rust
 fn main() {
     input! {
         n: usize,
         q: usize,
         a: [usize; n],
         txy: [(u8, usize, usize); q],
     }
-    let mut v = VecDeque::from(a);
+    let mut v = a.clone();
+    let mut offset = 0;
+    let f = |x, offset| (x - 1 + offset) % n;
     for (t, x, y) in txy {
         match t {
             1 => {
-                v.swap(x - 1, y - 1);
+                v.swap(f(x, offset), f(y, offset));
             }
             2 => {
-                let x = v.pop_back().unwrap();
-                v.push_front(x);
+                offset = f(n, offset);
             }
             3 => {
-                println!("{}", v[x - 1]);
+                println!("{}", v[f(x, offset)]);
             }
             _ => unreachable!(),
         }
     }
 }
```

この方法でも解けますが、 `VecDeque` がより簡単です。


# 実装例

## VecDeque
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/044_vecdeque.rs

## Vec (TLE)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/044_vec_tle.rs

## Vec とオフセット
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/044_vec_offset.rs
