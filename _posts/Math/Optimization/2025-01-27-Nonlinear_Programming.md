---
title: 非線形計画問題
date: 2024-11-27 13:00 +0900
categories: [Math, Optimization]
tags: [Math, Optimization, JP]
author: Youkoutaku
math: true
#img_path:
#image:
#  path:
#  alt:
---

## 拘束条件 constraint
$n$ 次元ベクトル $x\in\mathbb{R}^{n}$ を変数とするスカラー関数 $f(x)$ に対して，拘束条件を与える．

$$
\begin{aligned}
&g_i(x)\le 0 & (i=1,\dots,m)\\
&h_i(x)= 0 & (j=1,\dots,p)\\
\end{aligned}
$$

評価関数 $f(x)$ を最小にする $x=x\ast\in\mathbb{R}^n$ を求めることは，一般的な非線形計画問題である．ただし，$g_i(x)$ と $h_i(x)$ はそれぞれ**不等式拘束条件**(inequality constraint)と**等式拘束条件**(equality constraint)という．

拘束条件を満たす点 $x$ を **許容解** (feasible solution)といい，許容解全体の集合を**許容領域** (feasible region)という。すなわち，許容領域を $S$ とすると

$$
S=\{x\in\mathbb{R}^n:g_i(x)\le0\;(i=1,\dots,m), h_i(x)= 0\;(j=1,\dots,p)\}
$$

のように表される．許容領域が空集合だと最適化を考える意味がない．

## 最適解 optimal solution / 大域的最適解 global solution :

$x^\ast\in S$ が任意の $x\in S$ に対して $f(x^\ast)\le f(x)$ を満たす解は最適解である。

## 局所最適解 local optimal solution

$x^\ast\in S$ かつある$\epsilon>0$ が存在して任意の $x\in S\cap B(x^\ast,\epsilon)$ に対して $f(x^\ast)\le f(x)$ を満たす解は局所最適解である．ここで，$B(x^\ast,\epsilon)=\{x\in\mathbb{R}^n:\|x-x^\ast\|<\epsilon\}$ は中心 $x^\ast$，半径 $\epsilon$ の球の内部を表し，**開球** (open ball)と呼ばれる．

## 孤立局所最適解 Isolated optimal solution

$x^\ast\in S$ かつある $\epsilon>0$ が存在して任意の $x\in S\cap B(x^\ast,\epsilon)\backslash \{x^\ast_{other}\}$ に対して $f(x^\ast)\le f(x)$ を満たす解は局所最適解である．(近傍内に他の局所最適解が存在しない)

---

## ワイエルシュトラス定理([Weierstrass' Extreme Value Theorem - Wikipedia](https://en.wikipedia.org/wiki/Extreme_value_theorem))

許容領域 $S$ が有界閉集合で評価関数 $f$ が $S$ で連続ならば，最適解が存在する．

一般的に最適解を確実に見つけることは困難である．少なくとも局所最適解は存在するとして最適性条件と局所最適解の数値方法を考える．

---

## 参考資料
- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.
-  深津 卓弥, 菱沼 徹, 荒牧 大輔, PythonとCasADiで学ぶモデル予測制御, 講談社, 2024.