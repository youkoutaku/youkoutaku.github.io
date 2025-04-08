---
title: 勾配降下法(Gradient Descent)
date: 2025-02-20 11:03:00 +0900
categories:
  - Math
  - Optimization
tags:
  - Gradient Descent
  - Optimization
  - JP
author: Youkoutaku
math: true
mermaid: true
---

## 1. 最適化問題の定式化
勾配降下法（Gradient Descent）は，関数$f(x)$の最小値を求めるための最適化アルゴリズムの一つである．
ここでは，最小化したい関数$f(x)$を以下のように定義する：

$$
\min_{x \in \mathbb{R}^n} f(x)
$$

関数$f(x)$は通常，微分可能な連続関数と仮定する．

---

## 2. テイラー展開による勾配の解釈
関数$f(x)$をある点$x_k$の周りで 1 次のテイラー展開をすると，

$$
f(x_k + d) \approx f(x_k) + \nabla f(x_k)^T d
$$

ここで，$\nabla f(x_k)$は$f(x)$の勾配（Gradient）であり，関数の変化方向を示す．

- **もし$\nabla f(x_k) = 0$なら，$x_k$は極値点の候補である．**
- **もし$\nabla f(x_k) \neq 0$なら，関数を最小化する方向に進む必要がある．**

関数を減少させる方向を考えるため，**負の勾配方向** に向かって更新を行う：

$$
d_k = -\nabla f(x_k)
$$

---

## 3. 勾配降下法の更新式の導出
最小化のために，現在の点$x_k$から負の勾配方向に移動する．
このとき，**学習率（ステップサイズ）**$\alpha_k > 0$を導入すると，次のように更新式を得る：

$$
x_{k+1} = x_k - \alpha_k \nabla f(x_k)
$$

この式が **勾配降下法（Gradient Descent）** の基本的な更新則である．

---

## 4. 学習率$\alpha_k$の選び方
学習率$\alpha_k$は，最適化の収束速度や安定性に影響を与える．

- **固定ステップサイズ（Fixed Step Size）**  
	 一定の値$\alpha_k = \alpha$を用いる方法．
  - 大きすぎると発散する可能性がある
  - 小さすぎると収束が非常に遅くなる

- **アダプティブステップサイズ** （直線探索）
	各ステップごとに$\alpha_k$を調整する．例えば，最急降下法では次のように **Armijo条件** や **バックトラッキング** を用いて$\alpha_k$を調整する：
  
$$
  \alpha_k = \arg\min_{\alpha > 0} f(x_k - \alpha \nabla f(x_k))
$$

---
## 5. 勾配降下法の収束条件
勾配降下法が収束するためには，以下の条件が必要とされる：

- $f(x)$が **強凸関数（strongly convex）** である場合，適切な$\alpha_k$を選べば，収束が保証される．
- $\nabla f(x)$が **リプシッツ連続（Lipschitz continuous）** であれば，勾配降下法の収束率は次のように評価できる：
    - 固定ステップサイズ： $O(1/k)$
    - 適切なステップサイズ調整： $O(e^{-k})$（指数的収束）

---

## 6. 勾配降下法の種類
勾配降下法には以下のようなバリエーションがある．

- 最急降下法（Steepest Descent）
	- 勾配方向に沿って最適な$\alpha_k$を毎ステップ探索する．
- 共役勾配法（Conjugate Gradient Method）
	- 二次関数に対して高速な収束性を持つ．
- 確率的勾配降下法（SGD: Stochastic Gradient Descent）
	- 全データではなくミニバッチを用いる
	- 大規模データに適用可能

---

## 7. まとめ
勾配降下法は，関数$f(x)$の最小値を求めるための最も基本的な最適化手法であり，その理論的背景と実装上のポイントを以下にまとめる：

1. **テイラー展開**から勾配が関数の変化方向を示すことを理解する．
2. 最小化のためには**負の勾配方向**へ進むのが有効である．．
3. **更新式：** $x_{k+1} = x_k - \alpha_k \nabla f(x_k)$ を導出．
4. **学習率の選択**は，収束性と性能に直結する重要な要素．

勾配法は，機械学習・深層学習・制御最適化など多くの分野で応用されている．

---
## 参考資料
- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.
- Nocedal, J., & Wright, S. J. (2006). *Numerical Optimization*. Springer.