---
title: バリア法(Barrier Method)
date: 2025-03-18 16:03:00 +0900
categories:
  - Math
  - Optimization
tags:
  - Barrier Method
  - Optimization
  - JP
author: Youkoutaku
math: true
mermaid: true
---

## バリア法（Barrier Method）

バリア法（Barrier Method）は，**制約付き最適化問題**の解法の一種で，**内点法**（Interior Point Method） に分類される．特に不等式制約のある最適化問題に対して有効である．バリア法は，許容領域の内点のみで評価関数が定義されるので，内点ペナルティ法(interior penalty method)または内点法(interior point method)ともいう．


## 1. 問題の定式化

一般的な制約付き最適化問題は以下の形式となる：

$$
\text{minimize } f(x) \quad \text{subject to } g_i(x) \leq 0 \quad \text{for } i = 1, 2, \ldots, m
$$

- $f(x)$: 目的関数
- $g_i(x)$: 不等式制約

> バリア法の前提条件として許容領域の**内点**(interior point)が存在し，かつ内点全体$int\;S$の**閉包**(closure)が$S$に一致する．等式拘束条件が内点を持たないので，バリア法を適用できない．

---

## 2. バリア法の基本アイデア

バリア法は，制約条件 $g_i(x) \leq 0$ を **ペナルティ関数（Barrier Function）** に変換し，目的関数に追加して制約を満たす解を求める手法である．

### バリア関数の定義

バリア法では，目的関数に **ロジバリア関数（Logarithmic Barrier Function）** を加える：

$$
\phi(x) = -\sum_{i=1}^{m} \log (-g_i(x))
$$

通常，以下の形式で補正された目的関数を解く：

$$
\min_x \left[ f(x) + \frac{1}{t} \phi(x) \right]
$$

- $t$ は **バリアパラメータ（Barrier Parameter）** で，大きな値ほど制約境界に近づくことを許容する．
- バリア関数は，**制約境界** $g_i(x) = 0$ に近づくと無限大に発散するため，解は制約領域の内側に留まる．

---

## 3. よく使われるバリア関数

バリア関数として，以下の関数が一般的に使用されます：

### a. ロジバリア関数（Logarithmic Barrier Function）

$$
\phi(x) = -\sum_{i=1}^{m} \log (-g_i(x))
$$

- 制約境界に近づくと $\phi(x) \to \infty$ となる．
- 内点法で最も広く使われる関数．

### b. 逆べき乗バリア関数（Inverse Power Barrier Function）

$$
\phi(x) = -\sum_{i=1}^{m} \frac{1}{g_i(x)^p} \quad \text{(通常は } p = 1 \text{ または } 2)
$$

- 制約違反を防ぐ強いペナルティを与える．

---

## 4. バリア法のアルゴリズム

### Step 1: 初期化

- 初期点 $x_0$ を制約領域の内部に選ぶ．
- 初期バリアパラメータ $t > 0$ を設定する．
- 許容誤差 $\epsilon$ を設定する．

### Step 2: 近似問題の解決

解くべき補正問題：

$$
\min_x f(x) + \frac{1}{t} \phi(x)
$$

- ニュートン法や勾配法を用いて解く．
- 得られた解を $x^{\ast}(t)$ とする．

### Step 3: バリアパラメータの更新

- $t$ を増加させる（通常 $t \leftarrow \mu t$ で，$\mu > 1$）．
- 更新した $t$ で新しい補正問題を解く．

### Step 4: 収束判定

- 収束条件を満たすか確認：$\frac{m}{t} \leq \epsilon$ なら終了．(mは不等式制約の数)
- そうでなければ，ステップ 2 に戻る．

---

## 5. まとめ

### メリット
- 制約を厳密に守ることができる．
- 収束速度が速く，精度の高い解が得られる．
- 内点法の一部として，大規模問題にも適用可能．

### デメリット
- バリア関数は制約境界付近で非常に急激に変化するため，数値計算の精度が重要．
- バリアパラメータ $t$ を適切に制御しないと，収束が遅くなる可能性がある．

---

## 参考文献

- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.
- Nocedal, J., & Wright, S. J. (2006). *Numerical Optimization*. Springer.