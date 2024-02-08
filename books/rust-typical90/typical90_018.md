---
title: "018 - Statue of Chokudai（★3）"
---

[001 \- Yokan Party（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_a)


# アルゴリズム

## 三角形の角度を求める (atan2())

観覧車 `(0, y0, z0)` から、像 `(x, y, 0)` への俯角 = どれだけ下に傾いているかは、地面に投影したときの地面上の距離 `(-x, y0 - y)` を `x1` とすると、次の式で求まります。

$rad = tan^{-1}\dfrac{z_0}{x_1}$

[f64 \- Rust > atan()](https://doc.rust-lang.org/std/primitive.f64.html#method.atan)

この分数の結果を渡してアークタンジェントを求める関数 `atan()` があります。しかし分母が 0 になることがあり、その場合は 0 の割り算ができずに失敗します。

* [f64 \- Rust > atan2()](https://doc.rust-lang.org/std/primitive.f64.html#method.atan2)
  
代わりに、分子分母の両方を引数として渡す  `atan2()` を使います。

ちなみに、地面上に投影したときの地面上の距離は、三平方の定理から求まります。

$x{_1}^2 = (0 - x)^2 + (y_0 - y)^2$


# Tips

## 実数のテスト (cargo-compete の場合)

この問題では「相対誤差または絶対誤差が $10^{-7}$ 以下の時に正答」となります。浮動小数点の計算では誤差がどうしても現れます。テストを書くときに、整数のような完全一致を確認できません。

cargo-compete では実数をこのように誤差付きで評価してくれます。以下は出力例と実際の出力が少しずれていても Accepted となっています。

```terminal
> cargo compete test 018
:
1/2 ("sample1") Accepted (162 ms)
stdin:
4
2 1 1
4
0
1
2
3
expected:
0.000000000000
24.094842552111
54.735610317245
45.000000000000
actual:
0
24.0948425521107
54.735610317245346
45.00000000000001
```


# 実装例

## atan2
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/018_atan2.rs
