---
title: 共役勾配法(Conjugate Gradient Method)
date: 2025-02-22 20:00:00 +0900
categories:
  - Math
  - Optimization
tags:
  - Gradient Descent
  - Conjugate Gradient
  - Optimization
  - JP
author: Youkoutaku
math: true
mermaid: true
image:
  path: https://fmin.xyz/docs/methods/adaptive_metrics/cg_win.svg
  alt: https://fmin.xyz/docs/methods/adaptive_metrics/CG.html
---

## 背景と動機

最急降下法では，評価関数が最も急に減る方向（負の勾配方向）を探索方向として使われるが，この方向が常に最適解に効率的に近づくとは限らない．実際，勾配方向は等高線に対して垂直であるが，それが最短経路になるとは限らないため，効率が悪くなる場合がある．

## 二次関数の場合の理論
以下のような二次形評価関数を考える：

$$f(x)=\frac{1}{2}(x-x^\ast)Q(x-x^\ast)$$

ここで，
- $Q \in \mathbb{R}^{n \times n}$：正定値対称行列
- $x \in \mathbb{R}^{n}$：変数ベクトル
- $x^\ast \in \mathbb{R}^n$：最適解

ある初期点$x_0$から，方向$s_0$に対して線形探索を行うことで，$x_1 = x_0 + \alpha_0 s_0$が得られる．このとき，$s_0$の方向は，$f(x)$が最小となる方向であり，$x_1$において$s_0$は勾配ベクトルと直交する：

$$\nabla f(x_1)^Ts_0=(x_1-x^\ast)^TQs_0=0$$

次に，$s_1$が$(x_1 - x^\ast)$と同一方向にあると仮定します．このような$s_0, s_1$の関係を，$Q$に関して **共役（conjugate）** または **$Q$直交（$Q$-orthogonal）** と呼ぶ．

> 非ゼロベクトル $u,v$ が $u^T A v = 0$ を満たすとき，$u,v$ は $A$ に関して共役であるという．特に $A=I$ の場合は通常の直交性です．

## アルゴリズムの構築
- $x_k$: 状態変数（現在の点）
- $s_k$: 探索方向
- $\alpha_k$: ステップ長
- $d_k$: 最急降下方向

ステップ$k$において，$x_{k+1}$は以下で与えられる：

$$x_{k+1}=x_k+\alpha_ks_k$$

このとき$f(x_{k+1})$が最小化されるような$\alpha_k$は：

$$\begin{align*}
\frac{d}{d\alpha}f(x_k+\alpha s_k)\mid_{\alpha=\alpha_k}&=\frac{\partial f}{\partial x}(x_k+\alpha_ks_k)s_k\\&=(x_k+\alpha_ks_k-x^\ast)^TQs_k
\\&=(x_k-x^\ast)^TQs_k+\alpha_ks_k^TQs_k
\\&=0
\end{align*}$$

より，

$$\begin{align}
\alpha_k&=\frac{-(x_k-x^\ast)^TQs_k}{s_k^TQs_k}=\frac{-s_k^TQ(x_k-x^\ast)}{s_k^TQs_k}=\frac{s_k^Td_k}{s_k^TQs_k} \tag{1}
\end{align}$$

ここで，$x_k$ における2次関数 $f(x)$ の最急降下方向：

$$d_k:=-(\frac{\partial f(x_k)}{\partial x})^T=-Q(x_k-x^\ast) \tag{2}$$

新しい点 $x_{k+1}$ における探索方向 $s_{k+1}$ を最急降下方向 $d_{k+1}$ と直前の探索方向 $s_k$ とがはる平面上で決定することにし，

$$s_{k+1}=d_{k+1}+\beta_ks_k \tag{3}$$

が $s_k$ と共役になるようスカラー $\beta_k\in\mathbb{R}$ を決定する．

そして，共役の条件 $s_k^TQs_{k+1}=0$ から簡単な計算により,

$$\beta_k=-\frac{s_k^TQd_{k+1}}{s_k^TQs_k} \tag{4}$$

が得られる．さらに，$Q$ は正定なので，$s_k\ne0$ である限り $s_k^TQs_k>0$ であり，$\alpha_k$ と $\beta_k$ は定義される．

特に，最初の探索方向を最急降下方向とした $s_0=d_0$ 場合が一般的である．さらに，$k=0,\dots,n-1$に対して以下のように関係がある．

- $d_{k+1}\bot\{d_0,\dots,d_k\}$：次の $k+1$ 最急降下方向はこれまでの最急降下方向と直交する．
- $d_{k+1}\bot\{s_0,\dots,s_{k+1}\}$：次の $k+1$ 最急降下方向はこれまでの探索方向と直交する．
- すべての探索方向 $\{s_0,\dots,s_{k}\}$ は $Q$ に関して共役であり，1次独立である．
- $f(x)$ を $x_0+span\{s_0,\dots,s_k\}$ において $x_{k+1}$ は最小化される．

共役勾配法が **最大 $n$ 回の反復で正確に最適解に到達できる理由** ：
- 探索方向 $s_k$​ が Q-共役であり，それぞれが直交しているため，探索方向が重複しない．
- $n$ 次元空間では，最大 $n$ 本の独立なベクトルで空間を張ることができる．
- 各探索方向 $s_k$​ に沿って最適なステップ $\alpha_k$​ を選ぶことで，各ステップで異なる成分を効率的に削減できる．
- したがって，$n$ 回の反復で最適解 $x^\ast$ に到達する．

これは，単なる最急降下法 $O(n^2)$ の計算コストに比べて，より高速に収束することを意味する．

---
## $\alpha_k$と$\beta_k$の計算式の整理

(3)により：

$$s_{k}=d_{k}+\beta_{k-1}s_{k-1},s_0=d_0$$

内積により：

$$s_k^Td_k=d_k^Td_k+\beta_{k-1}s_{k-1}^Td_k=d_k^Td_k+0=\|d_k\|^2$$

$$\alpha_k=\frac{\|d_k\|^2}{s_k^TQs_k}$$

$x_{k+1}=x_k+\alpha_ks_k$と(3)により，

$$d_{k+1}=-Q(x_{k+1}-x^\ast)=-Q(x-x^\ast)-\alpha_kQs_k=d_k-\alpha_kQs_k$$

⇒

$$Qs_k=-\frac{d_{k+1}-d_k}{\alpha_k}$$

$$s_k^TQ=-\frac{d_{k+1}^T-d_k^T}{\alpha_k}$$

- $\beta_k$の分子：

$$s_k^TQd_{k+1}=-\frac{(d_{k+1}^T-d_k^T)d_{k+1}}{\alpha_k}=-\frac{d_{k+1}^Td_{k+1}}{\alpha_k}$$

- $\beta_k$の分母：

$$s_k^TQs_k=-\frac{s_k^T(d_{k+1}-d_k)}{\alpha_k}=\frac{d_k^Td_k}{\alpha_k}$$

ゆえに，

$$\beta_k=-\frac{-\|d_{k+1}\|^2}{\|d_k\|^2}=\frac{\|d_{k+1}\|^2}{\|d_k\|^2}$$

ここで，
- $d_k$は最急降下方向であり，勾配から求められる．
- $\alpha_k$は数式を使わずに直線探索も見つける．
- $\beta_k$は上式で勾配のみから計算できる．

---
## アルゴリズム（Conjugate Gradient Method）
1. $k \leftarrow 0$
2. 初期点 $x_0 \in \mathbb{R}^n$
3. $s_0 \leftarrow d_0 := -\nabla f(x_0)$
4. 繰り返し：
    1. $\mid\mid d_k \mid\mid \approx 0$ なら終了
    2. $\alpha_k = \frac{\mid\mid d_k \mid\mid ^2}{s_k^T Q s_k}$
    3. $x_{k+1} = x_k + \alpha_k s_k$
    4. $d_{k+1} = -\nabla f(x_{k+1})$
    5. $\beta_k$ の計算
    6. $s_{k+1} = d_{k+1} + \beta_k s_k$
    7. $k \leftarrow k + 1$

- $\beta_k$ の計算式
	- $\beta_k \gets -{s_k^TQd_{k+1}}/{s_k^TQs_k}$：ポラック・リビエ・ポリャック法(Polak-Ribiere-Polyak method)
	- $\beta_k \gets \mid\mid d_{k+1} \mid\mid ^2 /  \mid\mid d_k \mid\mid ^2$：フレッチャー・リーブス法(Fletcher-Reeves method)

> 評価関数が2次関数であれば，両者が等価ですが，一般の関数の場合には等価とはいえない．また，一般の関数では，$n$回の直線探索によって最適解に到達するとは限らないので，そのあとは再び最急降下法方向から探索方向の生成を再開する．
