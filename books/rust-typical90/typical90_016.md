---
title: "016 - Minimum Coins（★3）"
---

[016 \- Minimum Coins（★3）](https://atcoder.jp/contests/typical90/tasks/typical90_p)

# アルゴリズム

## 全探索

3種類の硬貨 A, B, C を使ってちょうど N 円支払う方法のうち、硬貨の枚数が最小になるものを調べる、という問題です。合計 9999 枚以内で支払えることが制約として保証されています。

A, B それぞれ 0枚から 9999枚まで動かす全探索を行い、C を適切に設定すれば N 円ちょうどにできるかというように読み替えます。 C が 10000枚以上でも解になってしまいますが、その後より少ないものが見つかって解が上書きされるはずです。

10000×10000 回調べるのは十分高速です。


# 実装例

## 全探索
https://github.com/hossy3/atcoder-solutions/blob/main/atcoder/typical90/src/bin/016_full_search.rs

