---
title: Prescribed Performance Control
date: 2024-05-07 12:05:07 +0900
categories: [Control Theory, Prescribed Performance Control]
tags: [Prescribed Performance Control, Control]
author: Youkoutaku
math: true
mermaid: true
#pin:
#img_path:
image:
  path: https://i.redd.it/tga2ckx15wcd1.png
  alt: 
---

> As to control systems, transient performance is as important as steady-state performance. For some special dynamic systems, transient performance is a more prior index in comparison with the steady-state one. Prescribed performance control (PPC) has been proved to be a powerful tool that guarantees control system outputs/errors with desired transient performance as well as steady-state performance.[^1]

---

A new control methodology namely prescribed performance control (PPC) was proposed for the first time by Bechlioulis [^2] [^3] in 2008 for the purpose of satisfying prescribed performance.

## A brief introduction of PPC

The core issues of PPC include

- persormance function
  - Applied to impose boundary constraints on tracking errors
- equivalent transformation
  - Based on the error transformation approach, a transformed error is defined.
  - The tansformed error, instead of the original tracking error, is used to devise feedback controllers.
- controller design

## Performance function

- $P(t)$ is smooth, positive and decreasing for all $t\ge 0$
- $\lim_{t\to0} P(t)=p_0$, $\lim_{t\to\infty} P(t)=p_{\infty}$, and $p_0>p_{\infty}>0$

$$
P(t)=(p_0-p_{\infty})e^{-e^{\iota t}}+p_{\infty}
$$

where $p_0, p_{\infty}, \iota \in\mathbb R^{+}$.

- $P(t)\in[p_0, p_{\infty})$
- $\iota$ is the convergence rate of $P(t)$

![](/src/PPC/Graphical-representation-of-constraint-boundary-2.png)

$$\left.\left\{\begin{matrix}-\sigma P(t)<e(t)<P(t),e(0)>0,\\-P(t)<e(t)<\sigma P(t),e(0)<0,\end{matrix}\right.\right.$$

where $0\le\sigma\le 1$ for all $t\ge 0$. By choosing $p_0,p_{\infty}$ and $\iota$ for $P(t)$, the transient performance such as overshoot and convergence time of $e(t)$ can be guaranteed. Moreover, the steady-state performance of $e(t)$ also can be achieved $-\sigma p_{\infty}<e(\infty)<p_{\infty}$ or $- p_{\infty}<e(\infty)<\sigma p_{\infty}$.

However, it is impossible to directly develop controllers using the above constrained system. We need use the error transformation which is capable of Transforming the above equation into the following "unconstranined " formulation.

$$e(t)=E(\xi)P(t),$$

where $\xi$ is a transformed error and the funciton $E(\xi)$ satisifies

1. $E(\xi)$ is smooth, bounded and strictly increasing.
2. $E(\xi)\in(-\sigma, 1)$ if $e(0)>0$ and $E(\xi)\in(-1,\sigma)$ if $e(0)<0$.
3. $\lim_{\xi\to-\infty} E(\xi)=-\sigma$ and $\lim_{\xi\to+\infty} E(\xi)=1$ if $e(0)>0$, and $\lim_{\xi\to-\infty} E(\xi)=-1$ and $\lim_{\xi\to+\infty} E(\xi)=\sigma$ if $e(0)<0$

$$E(\xi)=\begin{cases}\frac{e^\varsigma-\sigma e^{-\xi}}{e^\xi+e^{-\xi}},\mathrm{if~}e(0)\geq0\\\frac{\sigma e^\xi-e^{-\xi}}{e^\xi+e^{-\xi}},\mathrm{if~}e(0)<0.&&\end{cases}$$

Therefor, we have the tansformed error

$$\xi=E^{-1}\left(\frac{e(t)}{P(t)}\right)=\begin{cases}\frac12\mathrm{ln}\left(\frac{e(t)/P(t)+\sigma}{1-e(t)/P(t)}\right),\mathrm{if}e(0)\geq0\\\frac12\mathrm{ln}\left(\frac{e(t)/P(t)+1}{\sigma-e(t)/P(t)}\right),\mathrm{if}e(0)<0,&\end{cases}$$

Where $E^{-1}$ is the inverse funcitos.

## Controller design based on PPC

Consider a SISO dynamic system, given by

$$\dot x = f(x)+g(x)u$$

where $x,u$ are system state and control input, and $f(x), g(x)$ are continuous functions.

Define tracking error

$$e(t)=x-x^{ref}$$

$$
\begin{aligned}
\dot{\boldsymbol{\xi}}=& \Delta_{\xi}\left(\dot{e}(t)-\frac{P(t)}{P(t)}e(t)\right) \\
=& \Delta_\xi\left(f(x)+g(x)u-\dot{x}^\mathrm{ref}-\frac{\dot{P}(t)}{P(t)}e(t)\right),
\end{aligned}
$$

$$\Delta_\xi=\begin{cases}\frac{1+\sigma}{2P(t)[e(t)/P(t)+\sigma][1-e(t)/P(t)]}>0,\mathrm{if~}e(0)\geq0\\\frac{1+\sigma}{2P(t)[e(t)/P(t)+1][\sigma-e(t)/P(t)]}>0,\mathrm{if~}e(0)<0.&\end{cases}$$

As a result, it is convenient for controller design using $\dot \xi(t)$ rather than $\dot e(t)$. The Lyapunov synthesis approach can guarantee the convergence of $\xi$.

$$V(\xi)=\frac{1}{2}\xi^T\xi,\quad \dot V(\xi) =\xi^T \dot\xi $$

Design the input $u$ to satisfied

$$\dot V(\xi) +  V(\xi) \le 0$$

---

If the initial value of tracing error $e(t)$ is known, the constraint boundary can be simplified as follows:

$$-\sigma_LP(t)<e(t)<\sigma_RP(t)$$

$$E(\xi)=\frac{\sigma_Re^{\xi}-\sigma_Le^{-\xi}}{e^{\xi}+e^{-\xi}}$$

Then, a transformed error

$$\xi=E^{-1}\left(\frac{e(t)}{P(t)}\right)=\frac12\ln\left( \frac{e(t)/P(t)+\sigma_L}{\sigma_R-e(t)/P(t)} \right)$$

$$\Delta_\xi=\frac{1}{2P(t)\left(e(t)/P(t)+\sigma_L\right)}-\frac{1}{2P(t)\left(e(t)/P(t)-\sigma_R\right)}>0$$

---
## References

[^1]: [Prescribed performance control approaches, applications and challenges: A comprehensive survey - Bu - 2023 - Asian Journal of Control - Wiley Online Library](https://onlinelibrary.wiley.com/doi/10.1002/asjc.2765)
[^2]: [Robust Adaptive Control of Feedback Linearizable MIMO Nonlinear Systems With Prescribed Performance \| IEEE Journals & Magazine \| IEEE Xplore](https://ieeexplore.ieee.org/document/4639441)
[^3]: [Prescribed performance adaptive control of SISO feedback linearizable systems with disturbances \| IEEE Conference Publication \| IEEE Xplore](https://ieeexplore.ieee.org/document/4601971)
