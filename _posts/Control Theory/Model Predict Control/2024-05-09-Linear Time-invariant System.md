---
title: Linear Time-invariant System
date: 2024-05-09 13:05:09 +0900
categories: [Control Theory, Model Predict Control]
tags: [Linear System, State-space equation, Laplace transform]
author: Youkoutaku
math: true
mermaid: true
#pin: 
#img_path: 
#image:
#  path: 
#  alt: 
---

## State-space representation

$$
\dot{x}(t)=Ax(t)+Bu(t)
$$

$$
y(t)=Cx(t)+Du(t)
$$

## Exponential of matrix

$$
\dot{x}=ax \implies x=x(0)e^{at}
$$

where $a$ is a scale constant.

$$
\dot{x}=Ax \implies x=x(0)e^{At}
$$

where $A$ is a matrix.

- Taylor expansions of exponential

$$
{e}^{at}=1+at+\frac{1}{2!}(at)^{2}+\frac{1}{3!}(at)^{3}+\cdots $$

- Taylor expansion of matrix exponential

$$
{e}^{At}=I+At+\frac{1}{2!}(At)^{2}+\frac{1}{3!}(At)^{3}+\cdots $$

Therefor, It‘s simple to get the derivative of matrix exponential

$$
\frac{de^{At}}{dt}=0+A+\frac{2}{2!}A^{2}t+\frac{3}{3!}A^{3}t^{2}+\cdots=Ae^{At}$$

---

## Solution of State-Space Equations

### Laplace transform

$$
sX(s)-x(0)=AX(s)+BU(s)$$

$$
(sI-A)X(s)=x(0)+BU(s)$$

$$
X(s)=(sI-A)^{-1}x(0)+(sI-A)^{-1}BU(s)$$

>$e^{At}=L^{-1}\left[(sI-A)^{-1}\right]$

>Convolution: $(f*g)(t)= L[f]\times L[g] =\int_{}f(\tau)\cdot g(t-\tau)d\tau$

$$
x(t)=L^{-1}[X(s)]=e^{At}x(0)+\int^{t}_{0}e^{A(t-\tau)}Bu(\tau)d\tau$$

>Note that we can only get the solution when the initial time is 0 by using the differential properties of laplace.

---

### Direct
- Let’s multiply both sides of the equation by $e^{-At}$ 

$$
{e}^{-At} \frac{d}{dt}x(t)={e}^{-At}Ax(t)+{e}^{-At}Bu(t)$$

$$
{e}^{-At} \frac{d}{dt}x(t)-{e}^{-At}Ax(t)={e}^{-At}Bu(t)$$

> $\frac{d(f(t)g(t))}{dt}=\dot{f}(t)g(t)+f(t)\dot{g}(t)$

$$
\implies \frac{d({e}^{-A t}x(t))}{dt}={e}^{-A t}Bu{(t)}$$

$$
\int_{t_0}^{t}\frac{d({e}^{-A\tau}x{(\tau)})}{d\tau} {d}\tau=\int_{t_0}^{t}{e}^{-A\tau}Bu{(\tau)} \mathrm{d}\tau $$

$$
e^{-At}x(t)-e^{-At_0}x(t_0)=\int_{t_0}^{t}{e}^{-A\tau}Bu{(\tau)} \mathrm{d}\tau $$

$$
\implies  x(t)=e^{A(t-t_0)}x(t_0)+\int^{t}_{t_0}e^{A(t-\tau)}Bu(\tau)d\tau$$

---
## Reference
1. 控制之美（卷2）—最优化控制MPC与卡尔曼滤波器, 王天威, 黄军魁, 清华大学出版社
2. MATLAB/Simulinkによる制御工学入門, 川田昌克, 森北出版
3. Feedback control of dynamic systems, Gene F. Franklin, J. David Powell, Abbas Emami-Naeini, Seventh Edition