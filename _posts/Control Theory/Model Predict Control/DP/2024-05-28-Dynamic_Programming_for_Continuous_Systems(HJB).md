---
title: Dynamic Programming for Continuous System (HJB)
date: 2024-05-28 11:05:21 +0900
categories: [Control Theory, Model Predict Control]
tags:
  - Optimal Control
  - Control
  - Dynamic Programming
  - Continuous
  - HJB
  - Hamiltonian
author: Youkoutaku
math: true
mermaid: true
#pin:
#img_path:
#image:
#  path: 
#  alt: 
---

## Continuous System

$$\dot x(t)=f\left(x(t),u(t),t\right)$$

- $x(t):$ system states
- $u(t):$ input
- $f():$ linear or nonlinear function

## Performance function
The performance function from anytime $t$ to terminal time $t_f$:

$$J_{t\to t_f}(x(t),t,u(r))=h(x(t_f),t_f)+\int^{t_f}_{t}g(x(r),u(r),r)dr,\;(t\le r\le t_f)$$

- $h():$ the terminal cost
- $g():$ the running cost

## Control Task
Find the optimal input $$u(r)^*,\; r\in[t,t_f]$$ that results minimum performance function

$$J^*_{t\to t_f}(x(t),t)=\min_{u(r)}\left\{h(x(t_f),t_f)+\int^{t_f}_{t}g(x(r),u(r),r)dr\right\},t\le r\le t_f$$

---
## Backward multi stages
Divide time $[t,t_f]$ into $[t,t+\Delta t]$ and $[t+\Delta t,t_f]$.

$$\begin{aligned}
J^*_{t\to t_f}(x(t),t)&=\min_{u(r)}\Big\{h(x(t_f),t_f)+\int^{t_f}_{t+\Delta t}g(x(r),u(r),r)dr\\ & \int^{t+\Delta t}_{t}g(x(r),u(r),r)dr\Big\},\;t\le r\le t_f
\end{aligned}
$$

According to the performance function, we have

$$
\begin{aligned}
	h(x(t_f),t_f)+\int^{t_f}_{t+\Delta t}g(x(r),u(r),r)dr&=J_{t+\Delta t\to t_f}(x(t),t,u(r)),\\ &t+\Delta t \le r\le t_f
\end{aligned}
$$

- $J_{t+\Delta t\to t_f}:$ the performance function from $t+\Delta t$ to $t_f$.

For the $J_{t+\Delta t\to t_f}$, by the Bellman optimal theory , $$J^*_{t+\Delta t\to t_f}\left(x(t+\Delta t) ,t+\Delta t\right)$$ is the optimal cost for $$J_{t+\Delta t\to t_f}(x(t),t,u(r))$$. Then, we can obtain following equation

$$
\begin{aligned}
J^*_{t\to t_f}(x(t),t)=\min_{u(r)}\Big\{J^*_{t+\Delta t\to t_f}\left(x(t+\Delta t),t+\Delta t\right) + \\ \int^{t+\Delta t}_{t}g(x(r),u(r),r)dr\Big\},\;t \le r\le t+\Delta t
\end{aligned}$$

where it contains two terms, the optimal cost to go for  $r\in[t+\Delta t, t_f]$ and the cost for $[t,t+\Delta t]$.

Assuming that the second-order partial derivative of $$J^*_{t+\Delta t\to t_f}\left(x(t+\Delta t),t+\Delta t\right)$$ exists and is bounded,  the Taylor series expansion at $(x(t),t)$ as

$$\begin{aligned}
J^*_{t+\Delta t\to t_f}\left(x(t+\Delta t),t+\Delta t\right)&=J^*_{t\to t_f}(x(t),t)+\left[\frac{\partial J^*_{t+\Delta t\to t_f}(x(t),t)}{\partial t}\right]\Delta t\\ &+\left[\frac{\partial J^*_{t+\Delta t\to t_f}(x(t),t)}{\partial x}\right]^T\left(x(t+\Delta t)-x(t)\right)\\&+\text{Higher-order terms}
\end{aligned}
$$

>Notes 
>
>$$\frac{\partial J^*_{t+\Delta t\to t_f}\left(x(t),t\right)}{\partial x}=\begin{bmatrix}\frac{\partial J_{t+\Delta t\to t_f}^{*}(x_{(t)},t)}{\partial x_{1}}\\\vdots\\\frac{\partial J_{t+\Delta t\to t_f}^{*}\left(x(t),t\right)}{\partial x_{n}}\end{bmatrix}$$
>
>and
>
>$$\begin{aligned}
\lim_{\Delta t\to0}\frac{\partial J_{t+\Delta t\to t_{t}}^{*}(x_{(t)},t)}{\partial t}&=\frac{\partial J_{t\to t_{t}}^{*}(x_{(t)},t)}{\partial t}\triangleq J_{t}^{*}(x_{(t)},t)\\ \lim_{\Delta t\to0}\frac{\partial J_{t+\Delta t\to t_{t}}^{*}(x_{(t)},t)}{\partial x}&=\frac{\partial J_{t\to t_{t}}^{*}(x_{(t)},t)}{\partial x}\triangleq J_{x}^{*}(x_{(t)},t)
\end{aligned}
$$
>
>$$\begin{aligned}
&\lim_{\Delta t\to0}(x_{(t+\Delta t)}-x_{(t)})=\dot{x}_{(t)}\Delta t\\
&\lim_{\Delta t\to0}\int_{t}^{t+\Delta t}g\left(x_{(\tau)},u_{(\tau)},\tau\right)\mathrm{d}\tau=g\left(x_{(t)},u_{(t)},t\right)\Delta t\\ &\lim_{\Delta t\to0}\text{Higher-order terms} = 0\end{aligned}$$

Therefor, we can rewrite the above equation as 

$$\begin{aligned} J_{t\to t_f}^{*}(x_{(t)},t)&=\min_{u_{(t)}}\Big\{J_{t\to t_f}^{*}(x_{(t)},t)+J_{t}^{*}(x_{(t)},t)\Delta t+\\&J_{x}^{*}\left(x_{(t)},t\right)^T\dot{x}_{(t)}\Delta t+g\left(x_{(t)},u_{(t)},t\right)\Delta t \Big\}\end{aligned}$$

$$
0=J_{t}^{*}(x_{(t)},t)\Delta t+\min_{u_{(t)}}\Big\{J_{x}^{*}\left(x_{(t)},t\right)^Tf(x_{(t)},u_{(t)},t)\Delta t+g\left(x_{(t)},u_{(t)},t\right)\Delta t \Big\}$$

$$
0=J_{t}^{*}(x_{(t)},t)+\min_{u_{(t)}}\Big\{J_{x}^{*}\left(x_{(t)},t\right)^Tf(x_{(t)},u_{(t)},t)+g\left(x_{(t)},u_{(t)},t\right)\Big\}$$

> Notes that $$J^*_{t_f\to t_f}(x_{(t_f)},t_f)=h(x_{(t_f)},t_f)$$ for $t=t_f$.

Then, we obtain the **Hamiltonian-Jacobi-Bellman equation** called **HJB** equation as 

$$\begin{cases}
0=J_{t}^{*}(x_{(t)},t)+\min_{u_{(t)}}\Big\{J_{x}^{*}\left(x_{(t)},t\right)^Tf(x_{(t)},u_{(t)},t)+g\left(x_{(t)},u_{(t)},t\right)\Big\}\\
J^*_{t_f\to t_f}(x_{(t_f)},t_f)=h(x_{(t_f)},t_f)
\end{cases}
$$

The solution of the equation is the result of the optimal control strategy. And the **Hamiltonian** as following equation.

$$
\mathcal{H}(x_{(t)},u_{(t)},J_{x}^{*},t)\triangleq J_{x}^{*}(x_{(t)},t)^Tf(x_{(t)},u_{(t)},t)+g(x_{(t)},u_{(t)},t)$$

For the optimal control $$u^*_{(t)}$$, the Hamiltonian is minimum as 

$$\mathcal{H}(x_{(t)},u^{*}(x_{(t)},J_{x}^{*},t),J_{x}^{*},t)=\min_{u_{(t)}}\mathcal{H}(x_{(t)},u_{(t)},J_{x}^{*},t)$$

Therefore, We obtain a more concise HJB equation as 

$$0=J_{t}^{*}(x_{(t)},t)+\mathcal{H}(x_{(t)},u^{*}(x_{(t)},J_{x}^{*},t),J_{x}^{*},t)$$

where it contains two terms, the partial derivative of optimal cost  and the minimum Hamiltonian.

---
## Reference
1. [Optimal Control by DR_CAN ](https://space.bilibili.com/230105574/channel/collectiondetail?sid=1814750)
2. 王天威. 控制之美(卷2). 清华大学出版社. 2023.
3. Rakovic, S. V., & Levine, W. S. Handbook of model predictive control. 2018. 