---
title: "018 - Statue of Chokudai（★3）"
---

[001 \- Yokan Party（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_a)


# アルゴリズム

## 三角形の角度を求める (atan2())

観覧車 `(0, y0, z0)` から、像 `(x, y, 0)` への俯角 = どれだけ下に傾いているかは、地面に投影したときの地面上の距離 `(-x, y0 - y)` を `x1` とすると、次の式で求まります。

$rad = tan^{-1}\dfrac{z_0}{x_1}$

この分数の結果を渡してアークタンジェントを求める関数 `atan()` があります。しかし分母が 0 になることがあり、その場合は 0 の割り算ができずに失敗します。

代わりに、分子分母の両方を引数として渡す  `atan2()` を使います。


ちなみに、地面上に投影したときの地面上の距離は、三平方の定理から求まります。

$x{_1}^2 = (0 - x)^2 + (y_0 - y)^2$


# Tips

## 実数のテスト

TODO

# 実装例

## atan2
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/018_atan2.rs
