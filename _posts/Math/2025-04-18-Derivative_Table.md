---
title: Comparison Table of Derivative, Total Derivative, Gradient, Jacobian, Hessian, Gateaux Derivative and Frechet Derivative
date: 2025-04-18 13:04:00 +0900
categories: [Math]
tags: [Gradient, Jacobian, Hessian]
author: Youkoutaku
math: true
mermaid: true
---

|              Concept               | Target Function                                  | Meaning                                           | Mathematical Expression                                                                                                                                  |
| :----------------------------: | ----------------------------------- | -------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------- |
|       Derivative       | $$f:\mathbb{R} \to \mathbb{R}$$     | Measures the rate of change or slope of a single-variable function                             | $$f'(x) = \lim_{h\to0} \frac{f(x+h)-f(x)}{h}\in\mathbb R$$​                                                                         |
|   Total Derivative    | $$f:\mathbb{R}^n \to \mathbb{R}$$   | Linear approximation of multivariable functions; expressed via the dot product with the gradient             | $$df = \nabla f(\boldsymbol{x})^T d\boldsymbol{x} \in \mathbb R$$                                                                   |
|        Gradient        | $$f:\mathbb{R}^n \to \mathbb{R}$$   | Coefficient vector of the total derivative; indicates the direction of the steepest ascent                     | $$\nabla f(\boldsymbol{x}) = \left[ \frac{\partial f}{\partial x_1}, ..., \frac{\partial f}{\partial x_n} \right]^T\in\mathbb R^n$$ |
|      Jacobian       | $$f:\mathbb{R}^n \to \mathbb{R}^m$$ | Organizes all first-order partial derivatives into a matrix                        | $$J(x)=[J_{ij}] = \left[\frac{\partial f_i}{\partial x_j} \right]\in\mathbb R^{m\times n}$$                                         |
|       Hessian       | $$f:\mathbb{R}^n \to \mathbb{R}$$   | Organizes all second-order partial derivatives into a symmetric matrix; encodes curvature                        | $$H(x)=[H_{ij}] = \left[ \frac{\partial^2 f}{\partial x_i \partial x_j}\right] \in\mathbb R^{n\times n}$$​                          |
| Gateaux Derivative  | $$F: X \to X$$                      | Derivative in a specified direction $h$                         | $$D_G F(x; h) = \lim_{\epsilon \to 0} \frac{F(x + \epsilon h) - F(x)}{\epsilon}\in\mathbb R$$                                       |
| Frechet Derivative | $$F: X \to Y$$（Banach空間）            | Best linear approximation in all directions | $$\lim_{\|h\|\to 0}\frac{\|F(x+h)−F(x)−A(h)\|}{\|h\|}=0$$                                                                           |

