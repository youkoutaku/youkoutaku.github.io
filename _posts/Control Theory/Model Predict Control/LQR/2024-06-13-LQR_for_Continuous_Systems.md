---
title: LQR for Continuous Systems
date: 2024-06-13 10:20:28 +0900
categories: [Control Theory, Model Predict Control]
tags:
  - Optimal Control
  - Control
  - Dynamic Programming
  - LQR
  - Continuous
  - Riccati
  - HJB
author: Youkoutaku
math: true
mermaid: true
#pin:
#img_path:
#image:
#  path: 
#  alt: 
---

By the HJB equation, we discuss the linear quadratic regulators (LQR) for continuous systems. 

## Continuous systems
Consider a continuous system as the following equation.

$$\dot{x}_{(t)}=f(x_{(t)},u_{(t)},t)=A_{(t)}x_{(t)}+B_{(t)}u_{(t)}$$

where $x_{(t)}\in\mathbb{R}^n,u_{(t)}\in\mathbb{R}^p$ are system state and input, $A_{(t)}\in\mathbb{R}^{n\times n}$, $B_{(t)}\in\mathbb{R}^{n\times p}$ are state matrices for system.

## Cost function
Similar the discrete-time system, we define the quadratic cost function as

$$J=h(x_{(t_{f})},t_{f})+\int_{0}^{t_{f}}g(x_{(\tau)},u_{(\tau)},\tau)\mathrm{d}\tau$$

where the $t_f$ is the terminal time, 

$$\begin{aligned}&h(x_{(t_{f})},t_{t})=\frac{1}{2}x_{(t_{f})}^{T}Sx_{(t_{f})},\\&g(x_{(t)},u_{(t)},t)=\frac{1}{2}(x_{(t)}^{T}Q_{(t)}x_{(t)}+u_{(t)}^{T}R_{(t)}u_{(t)}).\end{aligned}$$

$S,Q_{[t]}\in\mathbb{R}^{n\times n}$ are symmetrically positive semi definite matrix as the weight matrix for terminal cost and running cost. $R_{[t]}\in\mathbb{R}^{p\times p}$ is symmetrically positive definite matrix as the weight matrix for input cost. 

The cost of anytime $0\le t\le t_f$  is defined as

$$J_{t\to t_{f}} (x_{(t)},t)=h(x_{(t_{f})},t_{f})+\int_{t}^{t_{f}}g(x_{(\tau)},u_{(\tau)},\tau)\mathrm{d}\tau $$

We can obtain the cost for $t+\Delta t\to t_f$ as

$$\begin{aligned}
J_{t+\Delta t\to t_f}(x_{(t+\Delta t)},t+\Delta t,u_{(\tau)}) &= h(x_{(t_{f})},t_{f})+\int_{t+_\Delta t}^{t_{f}}g(x_{(\tau)},u_{(\tau)},\tau)\mathrm{d}\tau \\&=\frac{1}{2} x_{(t_{f})}^{T}Sx_{(t_{f})}+\frac{1}{2}\int_{t+\Delta t}^{t_{f}}(x_{(\tau)}^{T}Q_{(\tau)}x_{(\tau)}+u_{(\tau)}^{T}R_{(\tau)}u_{(\tau)})\mathrm{d}\tau
\end{aligned}$$

---
**Fact**: the optimal cost $J_{t\to t_f}^*\left(x_{(t)}\right)$ is quadratic, as

$$J_{t\to t_f}^*(x_{(t)})=\frac{1}{2}x_{(t)}^TP_{(t)}x_{(t)}$$

where $P_{(t)}=P_{(t)}^T>0$. 

Then, We can also obtain the cost for $t+\Delta t\to t_f$ as

$$\begin{aligned}
J^*_{t+\Delta t\to t_f}(x_{(t+\Delta t)})&=\frac{1}{2}x_{(t+\Delta t)}^TP_{(t+\Delta t)}x_{(t+\Delta t)}
\\&=\frac{1}{2}\left(x_{(t)}+\Delta t(Ax(t)+Bu(t))\right)^T(P_{(t)}+\Delta t \dot P_{(t)} )\left(x_{(t)}+\Delta t(Ax(t)+Bu(t))\right)
\end{aligned}$$

And, the cost-to-go for $t\to t+\Delta t$ as

$$\begin{aligned}
	J_{t\to t+\Delta t}&=\frac{1}{2}\int_{t}^{t+\Delta t}g(x_{(\tau)},u_{(\tau)},\tau)\mathrm{d}\tau \\&=\frac{1}{2}\int_{t}^{t+\Delta t}(x_{(\tau)}^{T}Q_{(\tau)}x_{(\tau)}+u_{(\tau)}^{T}R_{(\tau)}u_{(\tau)})\mathrm{d}\tau
	\\&=\frac{1}{2}\Delta t(x_{(t)}^{T}Q_{(t)}x_{(t)}+u_{(t)}^{T}R_{(t)}u_{(t)})
\end{aligned}$$

Then, we can rewrite the optimal cost for $t\to t_f$ as

$$\begin{aligned}
	J_{t\to t_f}^*(x_{(t)})&=J_{t\to t+\Delta t}+J^*_{t+\Delta t\to t_f}(x_{(t+\Delta t)})\\&=\frac{1}{2}\left[ x_{(t)}^TP_{(t)}x_{(t)}+\right.\Delta t\left(x_{(t)}^TQ_{(t)}x_{(t)}+u_{(t)}^TR_{(t)}u_{(t)} \right.
	\\&\left.\left. +(Ax_{(t)}+Bu_{(t)})^TP_{(t)}x_{(t)}+x_{(t)}^TP_{(t)}(Ax_{(t)}+Bu_{(t)})+x_{(t)}^TP_{(t)}x_{(t)}\right) \right]
\end{aligned}$$

To the minimum of cost
$$\frac{\partial J^*_{t\to t_f}(x_{(t)})}{\partial u_{(t)}}=0$$
, we obtain the equation to get optimal $u_{(t)}$ as 

$$\Delta t \left(u_{(t)}^TR_{(t)}+x_{(t)}^TP_{(t)}B\right)=0$$

Then, we have the optimal input:

$$u^*_{(t)}=-R_{(t)}^{-1}B^TP_{(t)}x_{(t)}$$

The linear state feedback:

$$u(t)=K_{(t)}x_{(t)}, \; K_{(t)}=-R_{(t)}^{-1}B^TP_{(t)}$$

---
## HJB equation
using the optimal input to the the cost for $t\to t_f$ as 

$$\begin{aligned}
J^*_{t\to t_f}(x_{(t)})&=\frac{1}{2}x_{(t)}^TP_{(t)}x_{(t)} +\frac{1}{2}\left(x_{(t)}+\Delta t(Ax(t)+Bu^*(t))\right)^T(P_{(t)}+\Delta t \dot P_{(t)} )\left(x_{(t)}+\Delta t(Ax(t)+Bu^*(t))\right)
\end{aligned}$$

According to the $$J_{t\to t_f}^*(x_{(t)})=\frac{1}{2}x_{(t)}^TP_{(t)}x_{(t)}$$, we have the following equation:

$$\frac{1}{2}x_{(t)}^TP_{(t)}x_{(t)}=\frac{1}{2}x_{(t)}^TP_{(t)}x_{(t)} +\frac{1}{2}\left(x_{(t)}+\Delta t(Ax(t)+Bu^*(t))\right)^T(P_{(t)}+\Delta t \dot P_{(t)} )\left(x_{(t)}+\Delta t(Ax(t)+Bu^*(t))\right)$$

Then, the equation can be simplified as 

$$-\dot P_{(t)}=A^TP_{(t)}+P_{(t)}A-P_{(t)}BR_{(t)}^{-1}B^TP_{(t)}+Q$$

which is the **Riccati differential equation** for the LQR problem.

In additional, for linear time-invariant systems, the $\dot P_{(t)}=0$ for $t\to\infty$.

Then, we have the **algebraic Riccati equation** as 

$$0=A^TP+PA-PBR^{-1}B^TP+Q$$

Using the condition from systems, we solve it to get the $P_{(t)}$. Then, We have the optimal input. 

## Reference
1. [clqr.dvi (stanford.edu)](https://web.stanford.edu/class/ee363/lectures/clqr.pdf)
2. 王天威. 控制之美(卷2). 清华大学出版社. 2023.
3. Rakovic, S. V., & Levine, W. S. Handbook of model predictive control. 2018. 