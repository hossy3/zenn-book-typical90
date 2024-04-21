---
title: "問題一覧"
---

筆者は水色コーダーです。前の方から解こうとしていますが、難しい問題は力不足により飛ばす場合があります。

|問題|アルゴリズム|Tips|
|---|---|---|
|[001 - Yokan Party（★4）](./typical90_001)|二分探索, `partition_point()`|スニペット, 単体テスト|
|[002 - Encyclopedia of Parentheses（★3）](./typical90_002)|`itertools::combinations()`, bit全探索, 再帰||
|[003 - Longest Circular Road（★4）](./typical90_003)|dijkstra, 01-bfs, `pathfinding`|Usize1|
|[004 - Cross Sum（★2）](./typical90_004)|前処理|`itertools::join()`|
|[005 - Restricted Digits（★7）](./typical90_005)|`modint`, 行列, 繰り返し二乗法, ダブリング||
|[006 - Smallest Subsequence（★5）](./typical90_006)|二分探索, `BTreeSet::range()`|`let else`|
|[007 - CP Classes（★3）](./typical90_007)|二分探索, `Vec::binary_search()`|シャドーイング, `saturating_sub()`|
|[008 - AtCounter（★4）](./typical90_008)|DP|`proconio::Chars`, `Iterator::enumerate()`|
|[009 - Three Point Angle（★6）](./typical90_009)|尺取り法, `partition_point()`, 実数の比較||
|[010 - Score Sum Queries（★2）](./typical90_010)|累積和|unreachable!|
|[011 - Gravy Jobs（★6）](./typical90_011)|DP, 履歴数を減らす||
|[012 - Red Painting（★4）](./typical90_012)|Disjoint Set (Union Find)|クロージャ, 上下左右を辿る, 余白|
|[013 - Passing（★5）](./typical90_013)|dijkstra, `pathfinding`||
|[014 - We Used to Sing a Song Together（★3）](./typical90_014)|ソート, 合計値||
|[015 - Don't be too close（★6）](./typical90_015)|組み合わせ, 階乗, 階乗の逆数||
|[016 - Minimum Coins（★3）](./typical90_016)|全探索||
|[017 - Crossing Segments（★7）](./typical90_017)|遅延セグメント木, セグメント木, フェニック木||
|[018 - Statue of Chokudai（★3）](./typical90_018)|`atan2()`|実数のテスト|
|[019 - Pick Two（★6）](./typical90_019)|区間DP||
|[020 - Log Inequality（★3）](./typical90_020)|`pow()`||
|[021 - Come Back in One Piece（★5）](./typical90_021)|強連結成分, SCC, `pathfinding`||
|[022 - Cubic Cake（★2）](./typical90_022)|最大公約数, GCD||
|[023 - Avoid War（★7）](./typical90_023)|2次元DP||
|[024 - Select +／- One（★2）](./typical90_024)|偶奇性|`iter::zip()`, `is_even()`|
|[025 - Digit Product Equation（★7）](./typical90_025)|重複組み合わせ, `combinations_with_replacement()`, 再帰||
|[026 - Independent Set on a Tree（★4）](./typical90_026)|二部グラフ, DFS, BFS, Disjoint Set||
|[027 - Sign Up Requests （★2）](./typical90_027)|集合, `HashSet`, `BTreeSet`||
|[028 - Cluttered Paper（★4）](./typical90_028)|二次元いもす法||
|[029 - Long Bricks（★5）](./typical90_029)|遅延セグメント木, `BTreeMap`||
|[030 - K Factors（★5）](./typical90_030)|素因数列挙|`Vec<u8>` メモリを減らす|
|[031 - VS AtCoder（★6）](./typical90_031)|Grundy 数||
|[032 - AtCoder Ekiden（★3）](./typical90_032)|順列, `permutations()`, 再帰||
|[033 - Not Too Bright（★2）](./typical90_033)|コーナーケース||
|[034 - There are few types of elements（★4）](./typical90_034)|尺取り法|尺取り法 iter|
|[035 - Preserve Connectivity（★7）](./typical90_035)|オイラーツアー, ダブリング||
|[036 - Max Manhattan Distance（★5）](./typical90_036)|マンハッタン距離, bounding box||
|[037 - Don't Leave the Spice（★5）](./typical90_037)|スライド最小値, セグメント木|`assert!`|
|[038 - Large LCM（★3）](./typical90_038)|`u128`, LCM, GCD||
|039 - Tree Distance（★5）|||
|040 - Get More Money（★7）|||
|041 - Piles in AtCoder Farm（★7）|||
|042 - Multiple of 9（★4）|||
|043 - Maze Challenge with Lack of Sleep（★4）|||
|044 - Shift and Swapping（★3）|||
|045 - Simple Grouping（★6）|||
|046 - I Love 46（★3）|||
|047 - Monochromatic Diagonal（★7）|||
|048 - I will not drop out（★3）|||
|049 - Flip Digits 2（★6）|||
|050 - Stair Jump（★3）|||
|051 - Typical Shop（★5）|||
|052 - Dice Product（★3）|||
|053 - Discrete Dowsing（★7）|||
|054 - Takahashi Number（★6）|||
|055 - Select 5（★2）|||
|056 - Lucky Bag（★5）|||
|057 - Flip Flap（★6）|||
|058 - Original Calculator（★4）|||
|059 - Many Graph Queries（★7）|||
|060 - Chimera（★5）|||
|061 - Deck（★2）|||
|062 - Paint All（★6）|||
|063 - Monochromatic Subgrid（★4）|||
|064 - Uplift（★3）|||
|065 - RGB Balls 2（★7）|||
|066 - Various Arrays（★5）|||
|067 - Base 8 to 9（★2）|||
|068 - Paired Information（★5）|||
|069 - Colorful Blocks 2（★3）|||
|070 - Plant Planning（★4）|||
|071 - Fuzzy Priority（★7）|||
|072 - Loop Railway Plan（★4）|||
|073 - We Need Both a and b（★5）|||
|074 - ABC String 2（★6）|||
|075 - Magic For Balls（★3）|||
|076 - Cake Cut（★3）|||
|077 - Planes on a 2D Plane（★7）|||
|078 - Easy Graph Problem（★2）|||
|079 - Two by Two（★3）|||
|080 - Let's Share Bit（★6）|||
|081 - Friendly Group（★5）|||
|082 - Counting Numbers（★3）|||
|083 - Colorful Graph（★6）|||
|084 - There are two types of characters（★3）|||
|085 - Multiplication 085（★4）|||
|086 - Snuke's Favorite Arrays（★5）|||
|087 - Chokudai's Demand（★5）|||
|088 - Similar but Different Ways（★6）|||
|089 - Partitions and Inversions（★7）|||
|090 - Tenkei90's Last Problem（★7）|||
