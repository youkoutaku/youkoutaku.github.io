---
title: 微分、全微分、勾配、ヤコビ行列、ヘッセ行列、ガトー微分、フレシェ微分の比較表
date: 2025-04-18 13:04:00 +0900
categories: [Math]
tags: [Gradient, Jacobian, Hessian, JP]
author: Youkoutaku
math: true
mermaid: true
---

## 数学における様々な微分の理解
多変数関数や関数空間における解析では，関数の定義域と値域に応じてさまざまな微分の概念が使い分けられる．この記事では，通常の微分（常微分），全微分，勾配，ヤコビ行列，ヘッセ行列，ガトー微分，フレシェ微分などの主要な微分の定義と違いを簡単にまとめてみます．

---
## 常微分（Derivative）
- 対象:
    $$f:\mathbb{R} \to \mathbb{R}$$
- 数式:
    $$f'(x) = \lim_{h\to0} \frac{f(x+h)-f(x)}{h}\in\mathbb R$$
- 意味: 1変数関数における瞬間的な変化率（傾き）を表す．

---
## 全微分（Total Derivative）
- 対象:
    $$f:\mathbb{R}^n \to \mathbb{R}$$
- 数式:
    $$df = \nabla f(\boldsymbol{x})^T d\boldsymbol{x} \in \mathbb R$$
- 意味: 多変数関数の最も良い線形近似を与える微分である．勾配と変位の内積として表される．

---
## 勾配（Gradient）
- 対象:
    $$f:\mathbb{R}^n \to \mathbb{R}$$
- 数式:
    $$\nabla f(\boldsymbol{x}) = \left[ \frac{\partial f}{\partial x_1}, ..., \frac{\partial f}{\partial x_n} \right]^T\in\mathbb R^n$$
- 意味: 関数の最急上昇方向を示すベクトルであり，最適化などで重要な概念である．

---
## ヤコビ行列（Jacobian Matrix）
- 対象:
    $$f:\mathbb{R}^n \to \mathbb{R}^m$$
- 数式:
    $$J(x)=[J_{ij}] = \left[\frac{\partial f_i}{\partial x_j} \right]\in\mathbb R^{m\times n}$$
- 意味: ベクトル値関数の1階偏微分をすべてまとめた行列で，線形写像の導関数の役割を持つ．

---
## ヘッセ行列（Hessian Matrix）
- 対象:
    $$f:\mathbb{R}^n \to \mathbb{R}$$
- 数式:
    $$H(x)=[H_{ij}] = \left[ \frac{\partial^2 f}{\partial x_i \partial x_j}\right] \in\mathbb R^{n\times n}$$
- 意味: 2階偏微分からなる対称行列で，関数の曲率や凹凸性を評価するのに用いられる．

---
## ガトー微分（Gateaux Derivative）
- 対象: (ノルム空間)
    $$F: X \to X$$
- 数式:
    $$D_G F(x; h) = \lim_{\epsilon \to 0} \frac{F(x + \epsilon h) - F(x)}{\epsilon}\in\mathbb R$$
- 意味: 無限次元空間におけるある方向 $h$ に沿った方向微分である．

---
## フレシェ微分（Fréchet Derivative）
- 対象: (Banach空間)
    $$F: X \to Y$$
- 数式:
    $$\lim_{\|h\|\to 0}\frac{\|F(x+h)−F(x)−A(h)\|}{\|h\|}=0$$
- 意味: すべての方向での最良線形近似を与える最も強い定義の微分で，関数空間における微分の理論的基盤となる．


## まとめ

|              概念名               | 対象                                  | 意味                                           | 数式                                                                                                                                  |
| :----------------------------: | ----------------------------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
|       **微分**（derivative）       | $$f:\mathbb{R} \to \mathbb{R}$$     | 1変数関数の変化率・傾きを調べる                             | $$f'(x) = \lim_{h\to0} \frac{f(x+h)-f(x)}{h}\in\mathbb R$$​                                                                         |
|   **全微分**（total derivative）    | $$f:\mathbb{R}^n \to \mathbb{R}$$   | 多変数関数の**線形近似**を**勾配の内積として表現**される             | $$df = \nabla f(\boldsymbol{x})^T d\boldsymbol{x} \in \mathbb R$$                                                                   |
|        **勾配**（gradient）        | $$f:\mathbb{R}^n \to \mathbb{R}$$   | 全微分の係数ベクトル：**最大変化方向を示す**                     | $$\nabla f(\boldsymbol{x}) = \left[ \frac{\partial f}{\partial x_1}, ..., \frac{\partial f}{\partial x_n} \right]^T\in\mathbb R^n$$ |
|      **ヤコビ行列**（Jacobian）       | $$f:\mathbb{R}^n \to \mathbb{R}^m$$ | 多変数関数の**偏微分を行列**として整理                        | $$J(x)=[J_{ij}] = \left[\frac{\partial f_i}{\partial x_j} \right]\in\mathbb R^{m\times n}$$                                         |
|       **ヘッセ行列**（Hessian）       | $$f:\mathbb{R}^n \to \mathbb{R}$$   | **2階の偏微分**（曲率情報）を対称行列に整理                         | $$H(x)=[H_{ij}] = \left[ \frac{\partial^2 f}{\partial x_i \partial x_j}\right] \in\mathbb R^{n\times n}$$​                          |
| **ガトー微分**（Gateaux derivative）  | $$F: X \to X$$                      | 任意の方向 $h$ に沿った関数の変化率(**方向微分**)                         | $$D_G F(x; h) = \lim_{\epsilon \to 0} \frac{F(x + \epsilon h) - F(x)}{\epsilon}\in\mathbb R$$                                       |
| **フレシェ微分**（Frechet derivative） | $$F: X \to Y$$（Banach空間）            | 関数の**全方向における最良線形近似**：任意の方向におけるリップスティック条件を満たす | $$\lim_{\|h\|\to 0}\frac{\|F(x+h)−F(x)−A(h)\|}{\|h\|}=0$$                                                                           |

