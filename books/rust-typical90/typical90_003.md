---
title: "003 - Longest Circular Road（★4）"
---

[003 \- Longest Circular Road（★4）](https://atcoder.jp/contests/typical90/tasks/typical90_c)


# アルゴリズム

都市の隣接関係をグラフ理論で考えます。

あるノード Node0 からもっとも遠いノード Node1 を探し、つぎに Node1 からもっとも遠いノード Node2 を探します。 Node1 と Node2 の距離が、この問題中にある最大の距離になります。ここに道を追加したものが答えの閉路です。


## 重みなし無向グラフを組み立てる

```
0 1
1 2
```
問題文には、ノード (都市) の接続関係を次にように書いています。

* ノード0 と ノード1 が接続している
* ノード1 と ノード2 が接続している

グラフを処理するときには「ノード1 がどノードと接続しているか」を、すぐに調べられるようにしたいです。そこで、配列の配列として組み立てます。

|ノード番号|隣り合うノード番号|
|---|---|
|0|1|
|1|0, 2|
|2|1|

`let graph = vec![vec![1], vec![0, 2], vec![1]];` という感じです。

```rust
fn build_ungraph(n: usize, uv: &[(usize, usize)]) -> Vec<Vec<usize>> {
    let mut graph = vec![vec![]; n]; // node, edge
    for &(u, v) in uv {
        graph[u].push(v);
        graph[v].push(u);
    }
    graph
}
```

グラフの組み立ては頻繁に使います。スニペット化がおすすめです。


## dijkstra (01-BFS)

あるノード `s` から他のノードに到達するときのコストを求めるには、dijkstra ダイクストラ法が使えます。

辺の重みがすべて同じなら、両端キュー `VecDeque` 構造を使うのが簡単です。辺の重さを考慮した重み付きキュー `BinaryHeap` でも良いです。

```rust
fn shortest_all_ungraph(s: usize, graph: &[Vec<usize>]) -> Vec<Option<usize>> {
    let n = graph.len();
    let mut v = vec![None; n];
    let mut queue = VecDeque::new();
    v[s] = Some(0usize);
    queue.push_back((0, s));

    while let Some((step, i)) = queue.pop_front() {
        for &j in &graph[i] {
            if v[j].is_none() {
                v[j] = Some(step + 1);
                queue.push_back((step + 1, j));
            }
        }
    }
    v
}
```

到達可能 `Some()` と到達不能 `None` を区別できるように、 `Option<usize>` を返すようにしています。この問題では「どの都市の間も、いくつかの道路を通って移動可能」 ですので `usize` をそのまま返せば十分です。ほかの問題でもそのまま使いまわせるように、という理由です。

`let mut v = vec![usize::MAX; n];` のように大きな値を入れる派の方もいると思います。その場で書くときにはその方が短く書けます。あとからコードを再利用するときに無効値が分かりにくいですので、私は `Option` 派です。

これを使い、ノード0 からもっとも離れているノード番号 `index` を求めるには、次のようにします。 `itertools::position_max()` を使います。

```rust
    let scores = shortest_all_ungraph(0, &graph);
    let index = scores.iter().position_max().unwrap();
```


## dijkstra (pathfinding)

* [dijkstra\_all in pathfinding::directed::dijkstra \- Rust](https://docs.rs/pathfinding/latest/pathfinding/directed/dijkstra/fn.dijkstra_all.html)

pathfinding クレートに、このダイクストラ法を行う関数が用意されています。 (到達可能なノード, (親ノード, コスト)) という対応の `HashMap` を返します。すべての辺にコスト1 を入れて流します。

```rust
    let reachables = dijkstra_all(&0, |&i| graph[i].iter().map(|&j| (j, 1usize)));
    let index = *reachables
        .iter()
        .max_by_key(|(_, &(_, step))| step)
        .unwrap()
        .0;
```

# Tips

## Usize1

* [proconio \- Rust](https://docs.rs/proconio/latest/proconio/)

競技プログラミングの問題文で出てくる配列は、インデックスが 1 から始まることがあります。 Rust では 0 から始まるほうが扱いやすいです。

そこで、読み込む時点で 1 数字を減らす機能が proconio に用意されています。

```
1 2
```

```rust
input!{ a: usize, b: usize } // a = 1, b = 2 
```

```rust
input!{ a: Usize1, b: Usize1 } // a = 0, b = 1
```

あとから 1引けば良い言ってしまえばそれまでですが、どこで 1-indexed と 0-indexed を切り替えるかが揃っていないとコードを読み書きするときに混乱します。基本的に Usize1 を使って問題読み込み時に寄せる、とするのは分かりやすいと思います。


# 実装例

## dijkstra (01-BFS)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/003_dijkstra.rs

## dijkstra (pathfinding)
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/003_pathfinding.rs
