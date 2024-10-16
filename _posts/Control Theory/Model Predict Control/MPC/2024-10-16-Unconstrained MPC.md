---
title: Unconstrained MPC
date: 2024-10-16 16:00:00 +0900
categories:
  - Control Theory
  - Model Predict Control
tags:
  - Optimal
  - MPC
  - Matlab
author: Youkoutaku
math: true
mermaid: true
---

## System Modeling
### Discrete linear time-invariant system

The dynamics of the system are represented by the following state-space equation:

$$x_{[k+1]}=Ax_{[k]}+Bu_{[k]}$$

where:
- $x_{[k]}\in\mathbb{R}^n$ is the states vector at time step $k$.
- $A\in\mathbb{R}^{n\times n}$ is the state transition matrix.
- $u_{[k]}\in\mathbb{R}^{p}$ is the control input vector.
- $B\in\mathbb{R}^{n\times p}$ is the input matrix.

### Performance function

$$J_k=\frac{1}{2}x^T_{[h]}Q_fx_{[h]}+\frac{1}{2}\sum^{h-1}_{k=0}\left(x_{[k]}^TQx_{[k]}+u^T_{[k]}Ru_{[k]}\right)$$

- $Q_f=\text{diag}(s_1,\dots,s_n)\in\mathbb{R}^{n\times n}, Q_f\ge0$ is the terminal weight matrix.
- $Q=\text{diag}(q_1,\dots,q_n)\in\mathbb{R}^{n\times n}, Q\ge0$ is the state weight matrix.
- $R=\text{diag}(r_1,\dots,r_n)\in\mathbb{R}^{n\times n}, R\ge0$ is the cost weight matrix.
- $h$ is the prediction horizon.

## Prediction horizon

In MPC, for time step $k$, the states $x_{[k+1\mid k]},\dots,x_{[k+h\mid k]}$ are predicted.

$$\begin{aligned}
x_{[k+1\mid k]} &= Ax_{[k\mid k]}+Bu_{[k\mid k]}\\
x_{[k+2\mid k]} &= Ax_{[k+1\mid k]}+Bu_{[k+1\mid k]}\\
&\quad\vdots \\
x_{[k+h\mid k]} &= Ax_{[k+h\mid k]}+Bu_{[k+h\mid k]}
\end{aligned}$$

For simple to denote, Define:

$$X_{[k]}:=\begin{bmatrix}
x_{[k+1\mid k]}\\ x_{[k+2\mid k]}\\ \vdots \\x_{[k+h\mid k]}
\end{bmatrix}\in\mathbb{R}^{nh}$$

$$U_{[k]}:=\begin{bmatrix}
u_{[k\mid k]}\\u_{[k+1\mid k]}\\\vdots\\u_{[k+h-1\mid k]}
\end{bmatrix}\in\mathbb{R}^{ph}$$

We have the compact expression for prediction horizon

$$X_{[k]}=A_p x_{[k\mid k]}+B_p U_{[k]}$$

where

$$A_p=\begin{bmatrix}
A\\ A^2 \\ \vdots \\ A^h
\end{bmatrix}\in\mathbb{R}^{nh\times n}$$

$$B_p=\begin{bmatrix}
B&0_{n\times p}&\dots&0_{n\times p}\\AB&B&\dots&0_{n\times p}\\ \vdots&\vdots &\ddots &\vdots\\ A^{h-1}B&A^{h-2}B&\dots&B
\end{bmatrix}\in\mathbb{R}^{nh\times ph}$$

## Control task

The task is to find the optimal control input sequence:

$$U^*_{[k]}:=\begin{bmatrix}
u^*_{[k\mid k]}\\u^*_{[k+1\mid k]}\\\vdots\\u^*_{[k+h-1\mid k]}
\end{bmatrix}\in\mathbb{R}^{ph}$$

which minimizes the performance function $J_k$ .

What we should do is to express the performance function $J_k$ in terms of the control input sequence $U_{[k]}$ so that it forms a standard quadratic programming (QP) problem. 

Because solvers for QP problems are well-developed, it is important for MPC to transform the problem into a QP form so that these solvers can effectively solve it.

## Solution
### Transform the problem into a QP form

$$J_k=\frac{1}{2}x^T_{[k+h\mid k]}Q_fx_{[k+h\mid k]}+\frac{1}{2}\sum^{h-1}_{i=0}\left(x_{[k+i\mid k]}^TQx_{[k+i\mid k]}+u^T_{[k+i\mid k]}Ru_{[k+i\mid k]}\right)$$

- Take out the initial cost $x^T_{[k\mid k]}Qx_{[k\mid k]}$ from the sum.

$$J_k=\frac{1}{2}x^T_{[k\mid k]}Qx_{[k\mid k]}+\frac{1}{2}x^T_{[k+h\mid k]}Q_fx_{[k+h\mid k]}+\frac{1}{2}\sum^{h-1}_{i=1}x_{[k+i\mid k]}^TQx_{[k+i\mid k]}+\frac{1}{2}\sum^{h-1}_{i=0}u^T_{[k+i\mid k]}Ru_{[k+i\mid k]}$$

- Rewrite by the compact expression.

$$J_k=\frac{1}{2}x^T_{[k\mid k]}Qx_{[k\mid k]}+\frac{1}{2}X^T_{[k]}\begin{bmatrix}
Q&\dots&0_{n\times n}\\\vdots&Q&\vdots\\0_{n\times n}&\cdots&Q_f
\end{bmatrix}X_{[k]}+\frac{1}{2}U^T_{[k]}\begin{bmatrix}
R&\dots&0_{n\times n}\\\vdots&R&\vdots\\0_{n\times n}&\cdots&R
\end{bmatrix}U_{[k]}$$

- Define the new weight matrix and rewrite.

$$Q_p:=\begin{bmatrix}
Q&\dots&0_{n\times n}\\\vdots&Q&\vdots\\0_{n\times n}&\cdots&Q_f
\end{bmatrix}\in\mathbb{R}^{nh\times nh}$$

$$R_p:=\begin{bmatrix}
R&\dots&0_{n\times n}\\\vdots&R&\vdots\\0_{n\times n}&\cdots&R
\end{bmatrix}\in\mathbb{R}^{ph\times ph}$$

$$J_k=\frac{1}{2}x^T_{[k\mid k]}Qx_{[k\mid k]}+\frac{1}{2}X^T_{[k]}Q_pX_{[k]}+\frac{1}{2}U^T_{[k]}R_pU_{[k]}$$

> $x_{[k\mid k]}$ , the initial states for time step $k$, is known. 

- According to the prediction horizon,  we can express the $X_{[k]}$ in terms of the $U_{[k]}$.

$$\begin{aligned}
X^T_{[k]}Q_pX_{[k]}&=(A_p x_{[k\mid k]}+B_p U_{[k]})^TQ_p(A_p x_{[k\mid k]}+B_p U_{[k]})\\
&=(x^T_{[k\mid k]}A_p^T+U_{[k]}^TB_p^T)Q_p(A_p x_{[k\mid k]}+B_p U_{[k]})\\
&=x^T_{[k\mid k]}A_p^TQ_pA_p x_{[k\mid k]}+x^T_{[k\mid k]}A_p^TQ_pB_p U_{[k]}+U_{[k]}^TB_p^TQ_pA_p x_{[k\mid k]}+U_{[k]}^TB_p^TQ_pB_p U_{[k]}\\
\end{aligned}$$

> $x^T_{[k\mid k]}A_p^TQ_pB_p U_{[k]}=U_{[k]}^TB_p^TQ_pA_p x_{[k\mid k]}$

$$
X^T_{[k]}Q_pX_{[k]}=x^T_{[k\mid k]}A_p^TQ_pA_p x_{[k\mid k]}+2x^T_{[k\mid k]}A_p^TQ_pB_p U_{[k]}+U_{[k]}^TB_p^TQ_pB_p U_{[k]}
$$

- New performance function

$$J_k=\frac{1}{2}x^T_{[k\mid k]}(Q+A_p^TQ_pA_p)x_{[k\mid k]}+x^T_{[k\mid k]}A_p^TQ_pB_p U_{[k]}+\frac{1}{2}U^T_{[k]}(R_p+B_p^TQ_pB_p)U_{[k]}$$

-4444 Obtain the QP form

$$J_k=x_{[k\mid k]}^TFU_{[k]}+\frac{1}{2}U^T_{[k]}HU_{[k]}+C$$

where 

$$F:=A_p^TQ_pB_p\in\mathbb{R}^{n\times ph}$$

$$H:=R_p+B_p^TQ_pB_p\in\mathbb{R}^{ph\times ph}$$

$$C:=\frac{1}{2}x^T_{[k\mid k]}(Q+A_p^TQ_pA_p)x_{[k\mid k]}$$

> C is a constant regarding initial states.

### Analytical solution

$$ U_{[k\mid k]}^*=-H^{-1}F^Tx_{[x\mid x]}$$

$$ u_{[k\mid k]}^*=-k_{mpc}x_{[x\mid x]}$$

where

$$k_{mpc}:=\begin{bmatrix}
I_{p\times p}&0_{p\times p}&\cdots& 0_{p\times p}
\end{bmatrix}_{p\times ph}H^{-1}F^T$$

---
## Reference
- Handbook of Model Predictive Control
- 王天威. 控制之美(卷2). 清华大学出版社. 2023.
