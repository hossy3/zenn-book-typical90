---
title: "041 - Piles in AtCoder Farm（★7）"
---

[041 \- Piles in AtCoder Farm（★7）](https://atcoder.jp/contests/typical90/tasks/typical90_ao)


# アルゴリズム

## 例題

杭が 6本、以下の座標に打たれているとします。

```
6
1 3
1 6
4 9
5 1
7 6
9 6
```

![](https://storage.googleapis.com/zenn-user-upload/676c7f72f069-20240430.png)

この杭を囲むもっとも小さな多角形を作ります。

![](https://storage.googleapis.com/zenn-user-upload/09f2cd78258c-20240430.png)

多角形の中の格子点に新しく緑の杭を追加します。

![](https://storage.googleapis.com/zenn-user-upload/1a16b1bfb6bb-20240430.png)

この追加する杭の本数を求める問題です。例題では 38 になります。

## 三角形の面積
$(x_0, y_0), (x_1, y_1), (x_2, y_2)$ の 3頂点の三角形の面積を求めます。

頂点間の距離をベクトル $(dx_0, dy_0) = (x_1 - x_0, y_1 - y_0), (dx_1, dy_1) = (x_2 - x_1, y_2 - y_1)$
と置きます。このベクトルの外積が、平行四辺形の面積になります。

```rust
// 3角形の面積の2倍
fn area2((x0, y0): &(i64, i64), (x1, y1): &(i64, i64), (x2, y2): &(i64, i64)) -> i64 {
    let (dx0, dx1) = (x1 - x0, x2 - x1);
    let (dy0, dy1) = (y1 - y0, y2 - y1);
    dx0 * dy1 - dx1 * dy0
}
```

三角形の面積は平行四辺形の面積の半分です。整数の計算中に 0.5 が現れないように、2倍した値を返すようにします。

$(x_0, y_0), (x_1, y_1), (x_2, y_2)$ が反時計周りに並ぶときに、正の面積としています。時計回りのときは負になります。この性質を使い、上に凸か下に凸かを調べられます。


## 凸包
杭を包む凸多角形を作ります。これを凸包と言います。

![](https://storage.googleapis.com/zenn-user-upload/09f2cd78258c-20240430.png)

凸包の上側をまず調べます。

頂点を x 座標を第一キー、y 座標を第二キーとして小順位並び替えておきます。

左の頂点から順に、上側に凸かどうかを調べます。凸でなければ前回追加したものを捨てます。

|座標|上に凸?|上側凸包候補|
|---|---|---|
|(1, 2)||(1, 2)|
|(1, 6)||(1, 2), (1, 6)|
|(4, 9)|✅|(1, 2), (1, 6), (4, 9)|
|(5, 1)|✅|(1, 2), (1, 6), (4, 9), (5, 1)|
|(7, 6)|❌|(1, 2), (1, 6), (4, 9)|
||✅|(1, 2), (1, 6), (4, 9), (7, 6)|
|(9, 6)|❌|(1, 2), (1, 6), (4, 9)|
||✅|(1, 2), (1, 6), (4, 9), (9, 6)|

![](https://storage.googleapis.com/zenn-user-upload/6d164ad83223-20240430.png)

```rust
xy.sort();

let mut upper = vec![xy[0], xy[1]]; // 上側凸包候補
for &xy in &xy[2..] {
    while upper.len() >= 2 && area2(&upper[upper.len() - 2], &upper[upper.len() - 1], &xy) >= 0
    {
        upper.pop();
    }
    upper.push(xy);
}
```

下側凸包も同様に求められます。

![](https://storage.googleapis.com/zenn-user-upload/b156b0739ffa-20240430.png)


```rust
let mut lower = vec![xy[0], xy[1]]; // 下側凸包候補
for &xy in &xy[2..] {
    while lower.len() >= 2 && area2(&lower[lower.len() - 2], &lower[lower.len() - 1], &xy) <= 0
    {
        lower.pop();
    }
    lower.push(xy);
}
```

上側凸包も下側凸包も、並び替えた調点群の始終点を使っています。下側凸包に、上側凸包の始終点以外を逆順につなぎ合わせると、全体の凸包が得られます。

```rust
for &x in upper[1..(upper.len() - 1)].iter().rev() {
    lower.push(x);
}
lower
```

## 多角形の面積

多角形の面積は、三角形の面積の合計で表せます。

![](https://storage.googleapis.com/zenn-user-upload/9d2b0bbf72a8-20240430.png)


|三角形|面積 * 2|
|---|---|
|(1, 3), (5, 1), (10, 6)|(4 * 5 + 4 * 2) = 28|
|(1, 3), (10, 6), (4, 9)|(8 * 3 + 3 * 5) = 39|
|(1, 3), (4, 9), (1, 6)|(3 * -3 + 6 * 3) = 9|

多角形の面積は (28 + 39 + 9) / 2 = 38 です。

```rust
let convex_area2: i64 = v[1..]
    .windows(2)
    .map(|v0| area2(&v[0], &v0[0], &v0[1]))
    .sum();
```

## ピックの定理

![](https://storage.googleapis.com/zenn-user-upload/5f2ec0deacb4-20240430.png)

ピックの定理: 多角形の面積を S, 辺上の格子点数を b, 内部の格子点数を i とするとき、 $S=i+\frac{1}{2}b-1$ となります。

多角形の面積 38 は求め終わっています。辺上の赤い格子点がどれだけあるかが分かれば、内部の格子点数も求まるはずです。

各辺の赤い格子点の数は、縦と横の最小公倍数から 1 を引いた数になります。合計 5 個あります。

多角形の頂点を含めると、辺上の点は 10個あります。

$38=34+\frac{1}{2}\times 10-1$ より、内側の格子点は 34個あります。

問題文では、杭は最初に 6本打たれていました。 辺上と内側の格子点を合計すると 34 + 10 = 44本あります。この差 38 が答えになります。

```rust
fn ngrid_on_edge((x0, y0): &(i64, i64), (x1, y1): &(i64, i64)) -> i64 {
    let dx = x1 - x0;
    let dy = y1 - y0;
    dx.gcd(&dy) - 1
}
```

```rust
let ngrid_on_edges: i64 = (0..v.len())
    .map(|i| ngrid_on_edge(&v[i], &v[(i + 1) % v.len()]))
    .sum();
let ngrid_on_loop = ngrid_on_edges + v.len() as i64;
let result = (convex_area2 + ngrid_on_loop) / 2 - n as i64 + 1;
```

## floor_sum

* [floor\_sum in ac\_library::math \- Rust](https://docs.rs/ac-library-rs/0.1.1/ac_library/math/fn.floor_sum.html)

ピックの定理を使う代わりに、 AtCoder Library の floor_sum を使っても解けます。

$$\sum_{i = 0}^{n - 1} \lfloor \frac{a \times i + b}{m} \rfloor$$

となるように、上側凸包「以下」の格子点群数から、下側凸包「未満」の格子点群数を引けば良いです。

凸包の各辺を (x0, y0) から (x1, y1) とすると、辺「以下」にある格子点の数は $m = x_1 - x_0$ として、次のようになります。

$$\sum_{i = 0}^{m - 1} \lfloor \frac{(y_1-y_0) \times i + y_0 \times m}{m} \rfloor$$

![](https://storage.googleapis.com/zenn-user-upload/83638c8b0c59-20240430.png)

下側凸包は未満として数えるように、b を 1 小さな値にします。

$$\sum_{i = 0}^{m - 1} \lfloor \frac{(y_1-y_0) \times i + (y_0 \times m - 1)}{m} \rfloor$$


![](https://storage.googleapis.com/zenn-user-upload/3cef7a500e57-20240430.png)

引き算した結果が、凸包内部の格子点の数になります。

![](https://storage.googleapis.com/zenn-user-upload/ab66bd7dcb12-20240430.png)

```rust
let mut ngrid = 0i64;
for v in upper.windows(2) {
    let ((x0, y0), (x1, y1)) = (v[0], v[1]);
    let m = x1 - x0;
    ngrid += floor_sum(m, m, y1 - y0, m * y0);
}
for v in lower.windows(2) {
    let ((x0, y0), (x1, y1)) = (v[0], v[1]);
    let m = x1 - x0;
    ngrid -= floor_sum(m, m, y1 - y0, m * y0 - 1);
}
ngrid += upper.last().unwrap().1 - lower.last().unwrap().1 + 1;

let result = ngrid - n as i64;
```

おそらく C++ の ac-library ではこれで終わります。残念ながら ac-library-rs 0.1.1 の floor-sum には $0 \leq a, b \leq m$ という制約があります。呼び出し方を調整して、終了です。

```rust
// ac_library::floor_sum() の $0 \leq a, b \leq m$ 制約を除いたもの
fn floor_sum(n: i64, m: i64, a: i64, b: i64) -> i64 {
    let (a_div, a_mod) = a.div_mod_floor(&m);
    let (b_div, b_mod) = b.div_mod_floor(&m);

    a_div * n * (n - 1) / 2 + b_div * n + ac_library::floor_sum(n, m, a_mod, b_mod)
}
```

ac-library-rs に、この制約を解消する PR が出ているようです。いつかマージされることを期待です。


# 実装例

## ピックの定理
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/041_convex_hull_pick.rs

## floor-sum
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/041_convex_hull_floor_sum.rs

