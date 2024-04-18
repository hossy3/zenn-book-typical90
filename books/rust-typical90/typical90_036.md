---
title: "036 - Max Manhattan Distance（★5）"
---

[036 \- Max Manhattan Distance（★5）](https://atcoder.jp/contests/typical90/tasks/typical90_aj)


# アルゴリズム

## マンハッタン距離

次のグラフのような点に対して、それぞれの点からマンハッタン距離 $|x_1-x_0|+|y_1-y_0|$ がもっとも大きな値を求めたい、という問題です。

![](https://storage.googleapis.com/zenn-user-upload/86c26a849a28-20240418.png)

原点 `(0, 0)` からもっとも離れた点は `(-2, -3)` です。距離は `5` です。

マンハッタン距離が等しい位置 $|x|+|y|=k$ を等高線のように描くと、斜め 45° の線になります。この一番外側を見つける問題です。

![](https://storage.googleapis.com/zenn-user-upload/13626dc31738-20240418.png)

左上の点 `(-1, 2)` からもっとも離れた点も `(-2, -3)` です。距離は `6` です。

同じくマンハッタン距離が等しい位置 $|x+1|+|y-2|=k$ を等高線のように描くと、斜め 45° の線になります。


![](https://storage.googleapis.com/zenn-user-upload/04b41663c8b0-20240418.png)

この斜めを向いた正方形群の一番外側になるものの候補は、斜め45°の直線 $x+y$ 最大値上の点、最小値上の点、 $x-y$ 最大値上の点、最小値上の点、計 4通り調べれば十分です。

斜め向きの境界ボックス (bounding box) を作れば、ほぼ答えです。

![](https://storage.googleapis.com/zenn-user-upload/e4736492fc37-20240418.png)

45°回転しても良いです。こうすると各軸の最大値・最小値を求めれば終了です。

![](https://storage.googleapis.com/zenn-user-upload/29ccbba197ba-20240418.png)


## x + y の最小値

一行プログラミングで書けます。

```rust
let add_min = xy.iter().map(|&(x, y)| x + y).min().unwrap();
```

# 実装例

## マンハッタン距離
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/036_manhattan_2.rs
