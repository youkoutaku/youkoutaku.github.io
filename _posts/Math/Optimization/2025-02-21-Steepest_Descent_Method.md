---
title: 最急降下法(Steepest Descent Method)
date: 2025-02-21 18:04:00 +0900
categories:
  - Math
  - Optimization
tags:
  - Gradient Descent
  - Steepest Descent
  - Optimization
  - JP
author: Youkoutaku
math: true
mermaid: true
---

## 最急降下法（Steepest Descent Method）とは

最急降下法（Steepest Descent Method）は，目的関数 $f(x)$ を最も急激に減少させる方向（勾配）に進むことで，局所最小値を求める反復最適化アルゴリズムである．

---
## 探索方向の定義
点 $x_k$ における勾配 $\nabla f(x_k)$ に基づいて，探索方向 $d_k$ を次のように定める：

$$d_k = -\nabla f(x_k)$$

ここで：
- $\nabla f(x_k)$：点 $x_k$ における関数 $f$ の**勾配ベクトル（gradient vector）**
- $d_k$：探索方向（search direction）

---
## 減少性の確認
$\nabla f(x_k) \ne 0$ のとき，次が成り立つ（探索方向と勾配方向の内積）：

$$\nabla f(x_k)^T d_k = -\|\nabla f(x_k)\|^2 < 0$$

つまり，$d_k$ は $f$ の値を確実に減少させる方向である．

## アルゴリズム：最急降下法（Steepest Descent Method）

Algorithm: Steepest Descent method
- 初期点 $x_0 \in \mathbb{R}^n$ を選ぶ
- 繰り返し（$k=0,1,2,\dots$）：
    - 勾配のノルムが小さくなったら停止：
        $\nabla f(x_k)\| \approx 0$
    - 探索方向を計算：
        $d_k = -\nabla f(x_k)$
    - ステップサイズを決定：
        $\alpha_k = \arg\min_{\alpha > 0} f(x_k + \alpha d_k)$
    - 探索点を更新：
        $x_{k+1} = x_k + \alpha_k d_k$

## コメントと補足
- $\nabla f(x_k)$ は関数の等高線に垂直で，関数値が最も急激に**増加**する方向を示す．
- $-\nabla f(x_k)$ はその反対方向，つまり**最も急激に減少**する方向である．
- 直線探索でステップ幅 $\alpha_k$ を最適化することで，無駄な反復を避けられる．
- 局所最適性の確認には，ヘッセ行列 $H(x_k) = \nabla^2 f(x_k)$ の正定性をチェックする．

---
## 参考資料
- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.
- Nocedal, J., & Wright, S. J. (2006). *Numerical Optimization*. Springer.