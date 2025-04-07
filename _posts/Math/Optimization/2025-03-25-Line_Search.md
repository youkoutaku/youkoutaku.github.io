---
title: 直線探索(Line Search)
date: 2025-03-25 14:16:00 +0900
categories:
  - Math
  - Optimization
tags:
  - Line search
  - Golden-section search
  - Bracketing
  - Armijo condition
  - Curvature condition
  - Wolfe conditions
  - JP
author: Youkoutaku
math: true
mermaid: true
---

## 直線探索
**直線探索 (Line Search)** とは，最適化問題を解く過程で，解を進める方向を決定した後，その方向に沿って進む最適なステップサイズ（進行量）を見つける手法である．
直線探索は，**最適化アルゴリズム（例えば，勾配法やニュートン法）** の一部として使用され，解の収束を早めるために，探索する方向に沿って適切なステップサイズを決定する．

> なぜ「直線」というと，この探索方向が既に勾配法やニュートン法によって求められたベクトルである．この探索方向が決定変数 $x$ に対して最適な方向であるため，直線で探索すれば，最も効果的に最適解を求めるでしょう．

### 直線探索の目的
最適化の過程で，最適解を求めるためには，方向を決定した後にその方向に沿って移動する「進行量（ステップサイズ）」を選ぶ必要がある．**直線探索**は，これを効率的に求める手法である．
- 目的関数を最小化する方向に沿って，進行量を調整することで解を最適化
- 進行量が大きすぎると最適解を飛び越してしまい，小さすぎると収束が遅くなるため，最適なステップサイズが求められる

### 定義
最適化では，次のように解 $x_k$​ から更新する新しい点 $x_{k+1}$​ を計算する：

$$
x_{k+1} = x_k + \alpha_kd_k$$

- $x_k\in\mathbb{R}^n$​：現在の点（反復ごとの解）
- $d_k\in\mathbb{R}^n$​：探索方向（勾配方向やニュートン方向など）
- $\alpha_k>0$​：進行量（ステップサイズ）

ここで， 目的関数 $f$ を最小化するための $\alpha_k$ を選ぶ必要があり，直線探索を利用する．次の更新点の評価関数 $f(x_{k}+\alpha_kd_k)$ を最小化する $\alpha_k$ を求める．

$$
f'(\alpha_k)=\frac{\partial f(x_k+\alpha_kd_k)}{\partial\alpha_k}=\frac{\partial f}{\partial x}(x_k+\alpha_kd_k)d_k = \nabla f(x_k+\alpha_kd_k)^Td_k$$

### 精密な直線探索方法
精密な直線探索では，**最適な $\alpha_k$ を正確に求める**ことを目指す．ただし，計算コストが高いため，一般には単純な最小化アルゴリズムが利用される．

#### バックトラッキング(bracketing)
- 概要: 目的関数 $f(\alpha)$ の最小値にする $\alpha$ の範囲を特定する．
- アルゴリズム:
    1. 初期点 $\alpha_0=0$, $k=0$ とし，十分小さいスカラー $h>0$を与える
    2. ループ：
	    1. $\alpha_{k+1}=\alpha_k+h$
	    2. $f(\alpha_{k+1})\ge f(\alpha_k)$ ならば，ループ停止．
	    3. $k=k+1$

- 極小点範囲
	- $k=0$ 停止の場合：極小点範囲 $[0, \alpha_1]$
	- $k>0$ 停止の場合：極小点範囲 $[\alpha_{k-1}, \alpha_{k+1}]$

#### 黄金分割法(golden-section search method)
- 概要: 目的関数が単一の極小点を持つ関数である場合，**黄金比 $\varphi=\frac{1+\sqrt{5}}{2} \approx 1.618, \frac{1}{\varphi}=\frac{\sqrt{5}-1}{2} \approx 0.618$** を利用して極小点範囲を効率的に狭める．
- アルゴリズム:
- $\alpha$の極小区間 $[a, b]$ を与える．
- 区間の内部点 $\alpha_1$ と $\alpha_2$ を設定($a < \alpha_1 < \alpha_2 < b$)：

$$\alpha_1 = b - \frac{b - a}{\varphi}, \quad \alpha_2 = a + \frac{b - a}{\varphi}$$

- if $f(\alpha_1)<f(\alpha_2)$, 極小区間は$[a, \alpha_1]$となる．
    1. 更新 $b=\alpha_2,\;\alpha_2=\alpha_1$

$$\alpha_1 = b - \frac{b - a}{\varphi}$$

- $f(\alpha_1)>f(\alpha_2)$, 極小区間は$[\alpha_2, b]$となる．
	1. 更新 $a=\alpha_1,\;\alpha_1=\alpha_2$

$$\alpha_2 = a + \frac{b - a}{\varphi}$$

- 収束条件 $\vert b - a \vert < \epsilon$ で終了．


[Golden-section search - Wikipedia](https://en.wikipedia.org/wiki/Golden-section_search):

```python
"""
Python program for golden section search.  This implementation
does not reuse function evaluations and assumes the minimum is c
or d (not on the edges at a or b)
"""

import math

invphi = (math.sqrt(5) - 1) / 2  # 1 / phi

def gss(f, a, b, tolerance=1e-5):
    """
    Golden-section search
    to find the minimum of f on [a,b]

    * f: a strictly unimodal function on [a,b]

    Example:
    >>> def f(x): return (x - 2) ** 2
    >>> x = gss(f, 1, 5)
    >>> print(f"{x:.5f}")
    2.00000

    """
    while b - a > tolerance:
        c = b - (b - a) * invphi
        d = a + (b - a) * invphi
        if f(c) < f(d):
            b = d
        else:  # f(c) > f(d) to find the maximum
            a = c

    return (b + a) / 2
```

### 粗い直線探索方法
粗い直線探索では，**厳密な $\alpha_k$ ではなく，十分に良い $\alpha_k$ を素早く見つける** ことを目指す．これにより，反復計算が高速化される．

#### Armijo Condition
- 概要: $\alpha_k$ に対して，目的関数 $f(x_k + \alpha d_k)$ が十分に減少するため，$0<c_1<1$倍傾きの直線で課す.
- 条件：

$$
f(x_k+\alpha_kd_k)\le f(x_k)+c_1\nabla f(x_k)^Td_k\alpha_k \quad (\text{with}\;0<c_1<1)$$

#### Curvature condition (曲率条件)
- 概要: $\alpha_k$ が小さすぎて非効率にならないように，導関数の情報を用いて制約する
- 条件：

$$
\nabla f(x_k+\alpha_kd_k)^Td_k \ge c_2\nabla f(x_k)^Td_k \quad (\text{with}\;0<c_1<c_2<1)$$

#### Wolfe Conditions
- 概要: Armijo条件と曲率条件を組み合わせたもの．
- Wolfe条件: $\alpha_k$ が適切であるためには，Armijo ConditionとCurvature condition 二つ条件を満たす必要がある．

![Wolfe Condition](https://preview.redd.it/wolfe-condition-v0-otakpbgdsrqe1.jpg?width=640&crop=smart&auto=webp&s=67702633b9e8e729ce5425f8ba2ce296f7d9ae75)

## まとめ

- 精密な直線探索: 最適な $\alpha_k$ を正確に見つけるが，計算コストが高い（バックトラッキング，黄金分割法）
- 粗い直線探索: 高速だが，近似的な $\alpha_k$ を求める（Armijo Rule，Curvature, Wolfe)

## 参考資料
- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.
- Nocedal, J., & Wright, S. J. (2006). *Numerical Optimization*. Springer.