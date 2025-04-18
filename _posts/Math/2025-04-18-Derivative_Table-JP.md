---
title: 微分、全微分、勾配、ヤコビ行列、ヘッセ行列、ガトー微分、フレシェ微分の比較表
date: 2025-04-18 13:04:00 +0900
categories: [Math]
tags: [Gradient, Jacobian, Hessian, JP]
author: Youkoutaku
math: true
mermaid: true
---

|              概念名               | 対象                                  | 意味                                           | 数式                                                                                                                                  |
| :----------------------------: | ----------------------------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
|       **微分**（derivative）       | $$f:\mathbb{R} \to \mathbb{R}$$     | 1変数関数の変化率・傾きを調べる                             | $$f'(x) = \lim_{h\to0} \frac{f(x+h)-f(x)}{h}\in\mathbb R$$​                                                                         |
|   **全微分**（total derivative）    | $$f:\mathbb{R}^n \to \mathbb{R}$$   | 多変数関数の**線形近似**を**勾配の内積として表現**される             | $$df = \nabla f(\boldsymbol{x})^T d\boldsymbol{x} \in \mathbb R$$                                                                   |
|        **勾配**（gradient）        | $$f:\mathbb{R}^n \to \mathbb{R}$$   | 全微分の係数ベクトル：**最大変化方向を示す**                     | $$\nabla f(\boldsymbol{x}) = \left[ \frac{\partial f}{\partial x_1}, ..., \frac{\partial f}{\partial x_n} \right]^T\in\mathbb R^n$$ |
|      **ヤコビ行列**（Jacobian）       | $$f:\mathbb{R}^n \to \mathbb{R}^m$$ | 多変数関数の**偏微分を行列**として整理                        | $$J(x)=[J_{ij}] = \left[\frac{\partial f_i}{\partial x_j} \right]\in\mathbb R^{m\times n}$$                                         |
|       **ヘッセ行列**（Hessian）       | $$f:\mathbb{R}^n \to \mathbb{R}$$   | **2階の偏微分**（曲率情報）を対称行列に整理                         | $$H(x)=[H_{ij}] = \left[ \frac{\partial^2 f}{\partial x_i \partial x_j}\right] \in\mathbb R^{n\times n}$$​                          |
| **ガトー微分**（Gateaux derivative）  | $$F: X \to X$$                      | 任意の方向 $h$ に沿った関数の変化率(**方向微分**)                         | $$D_G F(x; h) = \lim_{\epsilon \to 0} \frac{F(x + \epsilon h) - F(x)}{\epsilon}\in\mathbb R$$                                       |
| **フレシェ微分**（Frechet derivative） | $$F: X \to Y$$（Banach空間）            | 関数の**全方向における最良線形近似**：任意の方向におけるリップスティック条件を満たす | $$\lim_{\|h\|\to 0}\frac{\|F(x+h)−F(x)−A(h)\|}{\|h\|}=0$$                                                                           |

