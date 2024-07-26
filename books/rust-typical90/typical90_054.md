---
title: "054 - Takahashi Number（★6）"
---

[054 \- Takahashi Number（★6）](https://atcoder.jp/contests/typical90/tasks/typical90_bb)


# アルゴリズム

## グラフで解く

```
2 6 9 10
1 3 8
2 4 6 8 10
6 7
5 6 7 8
```
4頂点 (2, 6, 9, 10) は、2頂点の組 (2, 6), (2, 9), (2, 10), (6, 9), (6, 10), (9, 10) それぞれの間に距離 1 の辺があるというように読みます。

```mermaid
graph LR;
subgraph 共著1
2---6---9---10
2---9
2---10
6---10
end
```

```mermaid
graph LR;
subgraph 共著2
1---3---8
1---8
end
```

```mermaid
graph LR;
subgraph 共著3
2---4---6---8---10
2---6---10
4---8
2---8
4---10
2---10
end
```

```mermaid
graph LR;
subgraph 共著4
6---7
end
```


```mermaid
graph LR;
subgraph 共著5
5---6---7---8
5---7
6---8
5---8
end
```

このとき、各頂点の 1 からの距離はどうか、という問題です。

```mermaid
graph LR;
subgraph 高橋数0
1
end
subgraph 高橋数1
3
8
end
subgraph 高橋数2
2
4
5
6
7
10
end
subgraph 高橋数3
9
end
subgraph 高橋数なし
11
end

2---6---9---10
2---9
2---10
6---10
1---3---8
1---8
2---4---6---8---10
2---6---10
4---8
2---8
4---10
2---10
6---7
5---6---7---8
5---7
6---8
5---8
```

これで Dijkstra 法を使えば高橋数が求まります。

しかし辺の数が多く、ごちゃっとしています。共著者 m 人のときに、$_mC_2= m(m-1)/2$ 本の辺が現れます。制約であり得る共著者 10万人になると、もれなく TLE 確定です。


## 超頂点 (virtual node) を使う

```mermaid
graph LR;
subgraph 共著1
2---6---9---10
2---9
2---10
6---10
end
```

を、頂点を増やす形で書き直してみます。

```mermaid
graph TD;
12((共著1))
12---2
12---6
12---9
12---10
```

頂点 `2` と頂点 `6` の位置関係が、隣から、頂点 `共著1` を挟んで 2辺を通る場所に変わりました。距離は増えますがグラフはスッキリした形になります。

他も同様に行います。

```mermaid
graph TD;
13((共著2))
13---1
13---3
13---8
```

```mermaid
graph TD;
14((共著3))
14---2
14---4
14---6
14---8
14---10
```

```mermaid
graph TD;
15((共著4))
15---6
15---7
```

```mermaid
graph TD;
16((共著5))
16---5
16---6
16---7
16---8
```

共著者 m 人のときに、辺の数が m 本に抑えられました。

すべてつなげると次のようになります。


```mermaid
graph TB;
subgraph 距離0_高橋数0
1
end
subgraph 距離1
13
end
subgraph 距離2_高橋数1
3
8
end
subgraph 距離3
14
16
end
subgraph 距離4_高橋数2
2
4
5
6
7
10
end
subgraph 距離5
12
15
end
subgraph 距離6_高橋数3
9
end
subgraph 高橋数なし
11
end

12((共著1))
12---2
12---6
12---9
12---10
13((共著2))
13---1
13---3
13---8
14((共著3))
14---2
14---4
14---6
14---8
14---10
15((共著4))
15---6
15---7
16((共著5))
16---5
16---6
16---7
16---8
```

目で見ても接続関係が分かります。

超頂点が上側、元の頂点が下側に並ぶ、二部グラフのような形になります。 [^1]

[^1]: 二部グラフのような、とにごしています。二部グラフ内に、頂点11 のように接続関係がない頂点を含めて良いのか分かりません。

コード例です。頂点数 N=11、共著論文数 M=5 なら、共著論文を超頂点とした頂点数 16 の無向グラフを作ります。

```rust
let mut graph = vec![vec![]; n + m];
for i in 0..m {
    let j = n + i; // 超頂点

    input! {
        k: usize,
        r: [Usize1; k],
    }
    for x in r {
        graph[j].push(x);
        graph[x].push(j);
    }
}
```

グラフができれば、あとは [003 - Longest Circular Road（★4）](./typical90_003) と同じように Dijkstra を解くことで終了でうす。 `pathfinding` でも良いです。


# 実装例

## 超頂点 + dijkstra (01-BFS)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/054_bipartite_dijkstra.rs

## 超頂点 + dijkstra (pathfinding)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/054_bipartite_pathfinding.rs
