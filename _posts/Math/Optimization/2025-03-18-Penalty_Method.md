---
title: ペナルティ法(Penalty Method)
date: 2025-03-18 14:03:00 +0900
categories:
  - Math
  - Optimization
tags:
  - Penalty Method
  - Optimization
  - JP
author: Youkoutaku
math: true
mermaid: true
---

## ペナルティ法

ペナルティ法は，**制約付き最適化問題（constrained optimization problem）** を **制約なし最適化問題（unconstrained optimization problem）** に変換して解く方法の一つである．
ペナルティ法では，制約条件の違反度合い（ペナルティ）を目的関数に加えることで，制約を間接的に満たす解を求める．

---

## 1. 問題の形式

### 制約付き最適化問題

$$
\min_{\mathbf{x}} f(\mathbf{x}) \quad \text{subject to} \quad g_i(\mathbf{x}) \leq 0, \, i = 1, \ldots, m \quad h_j(\mathbf{x}) = 0, \, j = 1, \ldots, p
$$

- $f(\mathbf{x})$：目的関数
- $g_i(\mathbf{x})$：不等式制約
- $h_j(\mathbf{x})$：等式制約
- $\mathbf{x} \in \mathbb{R}^n$：設計変数

---

## 2. ペナルティ関数の定義
ペナルティ法では，制約違反にペナルティを課すことで，以下の **ペナルティ関数** を最小化する．

$$
P(\mathbf{x})=\begin{cases} 0 & (\mathbf{x} \in S) \\ >0 & (\mathbf{x} \notin S) \end{cases}
$$

- $S$：拘束条件を考慮した許容領域

$$
S=\{\mathbf{x}\in\mathbb{R}^n:g_i(\mathbf{x})\leq 0, h_j(\mathbf{x})=0\}
$$

### (a) 不等式制約のペナルティ

$$
P_I(\mathbf{x}) = \sum_{i=1}^{m} \max(0, g_i(\mathbf{x}))^2
$$

制約 $g_i(\mathbf{x}) \leq 0$ を満たさない場合にペナルティが発生．

### (b) 等式制約のペナルティ

$$
P_E(\mathbf{x}) = \sum_{j=1}^{p} h_j(\mathbf{x})^2
$$

等式制約 $h_j(\mathbf{x}) = 0$ から離れるほどペナルティが大きくなる．

---

## 3. ペナルティ関数の構築

最適化問題を以下のように変更する：

$$
\min_{\mathbf{x}} \phi(\mathbf{x}, r) = f(\mathbf{x}) + \frac{r}{2} \left[ P_I(\mathbf{x}) + P_E(\mathbf{x}) \right]
$$

- $r > 0$：ペナルティ係数（ペナルティパラメータ）
- 大きな $r$ を選ぶと，制約違反のペナルティが大きくなる．
- $r \to \infty$ で制約違反を完全に排除するが，数値的不安定性が増す．

---
## 4. ペナルティ法のアルゴリズム

### Step 1: 初期化
- 初期点 $\mathbf{x}^{(0)}$ を設定
- 初期ペナルティ係数 $r_0 > 0$ を設定

### Step 2: ペナルティ関数の最適化

$$
\mathbf{x}^{(k+1)} = \arg \min_{\mathbf{x}} \phi(\mathbf{x}, r_k)
$$

### Step 3: ペナルティ係数の更新

$$
r_{k+1} = \beta \cdot r_k, \quad \beta > 1
$$

### Step 4: 収束判定
収束条件 $\|\nabla \phi(\mathbf{x}, r)\| \leq \epsilon$ を満たせば終了
- $\epsilon \approx 0$

---

## 5. まとめ
- 長所
	- 制約付き最適化を標準の制約なし最適化法で解ける
	- 実装が比較的容易
- 短所
	- ペナルティ係数 $r$ が大きいと数値計算が不安定になる
	- 解の精度がペナルティ係数の選択に依存

---
## 参考資料
- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.
- Nocedal, J., & Wright, S. J. (2006). *Numerical Optimization*. Springer.