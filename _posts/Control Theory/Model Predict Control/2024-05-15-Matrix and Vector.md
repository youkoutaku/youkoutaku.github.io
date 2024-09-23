---
title: Matrix and Vector
date: 2024-05-15 12:34:07 +0900
categories: [Control Theory, Model Predict Control]
tags: [Derivatives, Matrix, Vector]
author: Youkoutaku
math: true
mermaid: true
#pin:
#img_path:
image:
  path: https://pbs.twimg.com/media/GSnWcDWacAANv6i?format=png&name=360x360
  alt:
---

## 1. The derivatives of scalar function by vector

Consider a scalar function by a variable as

$$f(u)=u^2-2u-1$$

where $f,u\in\mathbb{R}$.

$$\frac{df(u)}{du}=2u-2$$

The extremum is at $u=1$ in $f(u)$

$$\frac{df(u)}{du}|_{u=1}=0$$

---

Consider a scalar funciton by two varibales as

$$f(u)=u_1^2+u_2^2+2u_1$$

To calculate the positon of extremum,

$$\begin{cases}\frac{\partial f(u_1,u_2)}{\partial u_1}=0\\\frac{\partial f(u_1,u_2)}{\partial u_2}=0\end{cases}$$

Define a vactor as $\mathbf{u} = [u_1\;u_2]^T$, We obtain

$$
\frac{\partial f(\mathbf u)}{\partial \mathbf u}=\begin{bmatrix}
\frac{\partial f(\mathbf u)}{\partial u_1}\\\frac{\partial f(\mathbf u)}{\partial u_2}
\end{bmatrix}=\begin{bmatrix}0\\0\end{bmatrix}
$$

---

Consider a scalar function $f(\mathbf u)\in\mathbb{R}$ by a vector $\mathbf u =[u_1\;\cdots\;u_n]^T \in\mathbb{R}^n$, We define the derivative of Scalar function by vector as following:

### denominator layout

$$\frac{\partial f(\mathbf {u})}{\partial\mathbf{u}}\triangleq\begin{bmatrix}\frac{\partial f(\mathbf{u})}{\partial{u}_1}\\ \vdots \\ \frac{\partial f(\mathbf{u})}{\partial{u}_2}\\\vdots\\\frac{\partial f(\mathbf{u})}{\partial{u}_n}\end{bmatrix}$$

where $\frac{\partial f(\mathbf {u})}{\partial\mathbf{u}}\in\mathbb{R}^n$ is a column vector.

### numerator layout

$$\frac{\partial f(\mathbf {u})}{\partial\mathbf{u}}\triangleq\begin{bmatrix}\frac{\partial f(\mathbf{u})}{\partial{u}_1}& \cdots & \frac{\partial f(\mathbf{u})}{\partial{u}_2}& \cdots & \frac{\partial f(\mathbf{u})}{\partial{u}_n}\end{bmatrix}$$

where $\frac{\partial f(\mathbf {u})}{\partial\mathbf{u}}\in\mathbb{R}^{1\times n}$ is a row vector.

---

## 2. The derivatives of vector function by vector

By using **denominator layout**, We defined a vector function as
$f(u)=[f_1(u)\; \cdots \;f_m(u)]^T\in\mathbb{R}^m$. Then, we have the derivative of vector function by a scalar $u$ as following:

$$\frac{\partial f(u)}{\partial u}\triangleq\begin{bmatrix}\frac{\partial f_1(u)}{\partial u}&\cdots&\frac{\partial f_m(u)}{\partial u}\end{bmatrix}$$

where $\frac{\partial f(u)}{\partial u}\in\mathbb{R}^{1\times m}$.

When $u=[u_1\;\cdots\;u_n]^T\in\mathbb{R}^{n}$, We have the derivative as following:

$$\frac{\partial f(u)}{\partial u}\triangleq\begin{bmatrix}\frac{\partial f(u)}{\partial u_1}\\\frac{\partial f(u)}{\partial u_2}\\\vdots\\\frac{\partial f(u)}{\partial u_n}\end{bmatrix}=\begin{bmatrix}\frac{\partial f_1(u)}{\partial u_1}&\frac{\partial f_2(u)}{\partial u_1}&\cdots&\frac{\partial f_m(u)}{\partial u_1}\\\frac{\partial f_1(u)}{\partial u_2}&\frac{\partial f_2(u)}{\partial u_2}&\cdots&\frac{\partial f_m(u)}{\partial u_2}\\\vdots&\vdots&\ddots&\vdots\\\frac{\partial f_1(u)}{\partial u_n}&\frac{\partial f_2(u)}{\partial u_n}&\cdots&\frac{\partial f_m(u)}{\partial u_n}\end{bmatrix}$$

where $\frac{\partial f(u)}{\partial u}\in\mathbb{R}^{n\times m}$.

This is called **Jacobi matrix** .

> When using numerator layout, $J_{numerator}={J_{denominator}}^T$

---

## 3. The formula of matrix differentiation

### 1

$$\frac{\partial(u^Tf)}{\partial u}=f$$

where $u,f\in\mathbb{R}^n$.

#### Proof:

$$u^{T}f=\begin{bmatrix}u_1&\cdots&u_n\end{bmatrix}\begin{bmatrix}f_1\\\vdots\\f_n\end{bmatrix}=f_1u_1+\cdots+f_nu_n$$

$$\frac{\partial(u^Tf)}{\partial u}=\begin{bmatrix}\frac{\partial u^Tf}{\partial u_1}\\ \vdots \\ \frac{\partial u^Tf}{\partial u_n} \end{bmatrix}=\begin{bmatrix}\frac{\partial(f_1u_1+f_2u_2+\cdots+f_nu_n)}{\partial u_1}\\\vdots\\\frac{\partial(f_1u_1+f_2u_2+\cdots+f_nu_n)}{\partial u_n}\end{bmatrix}=\begin{bmatrix}f_1\\\vdots\\f_n\end{bmatrix}=f$$

---

### 2

$$\frac{\partial (Au)}{\partial u}=A^T$$

where $u\in\mathbb{R}^n, A\in\mathbb{R}^{n\times n}$.

#### Proof:

$$Au=\begin{bmatrix}a_{11}&a_{12}&\cdots&a_{1n}\\a_{21}&a_{22}&\cdots&a_{2n}\\\vdots&\vdots&\ddots&\vdots\\a_{n1}&a_{n2}&\cdots&a_{nn}\end{bmatrix}\begin{bmatrix}u_1\\u_2\\\vdots\\u_n\end{bmatrix}=\begin{bmatrix}a_{11}u_1+a_{12}u_2+\cdots+a_{1n}u_n\\a_{21}u_1+a_{22}u_2+\cdots+a_{2n}u_n\\\vdots\\a_{n1}u_1+a_{n2}u_2+\cdots+a_{nn}u_n\end{bmatrix}$$

Define a vector $f=[f_1 \; f_2 \; \cdots \; f_n]^T\in\mathbb{R}^n$, satisfied the following equation

$$f=\begin{bmatrix}f_1\\f_2\\\vdots\\f_n\end{bmatrix}=\begin{bmatrix}a_{11}u_1+a_{12}u_2+\cdots+a_{1n}u_n\\a_{21}u_1+a_{22}u_2+\cdots+a_{2n}u_n\\\vdots\\a_{n1}u_1+a_{n2}u_2+\cdots+a_{nn}u_n\end{bmatrix}=Au$$
Then, We have the result.

$$\frac{\partial (Au)}{\partial u}=\begin{bmatrix}\frac{\partial Au}{\partial u_1}\\\frac{\partial Au}{\partial u_2}\\\vdots\\\frac{\partial Au}{\partial u_n}\end{bmatrix}=\begin{bmatrix}\frac{\partial f_1}{\partial u_1}&\frac{\partial f_2}{\partial u_1}&\cdots&\frac{\partial f_n}{\partial u_1}\\\frac{\partial f_1}{\partial u_2}&\frac{\partial f_2}{\partial u_2}&\cdots&\frac{\partial f_n}{\partial u_2}\\\vdots&\vdots&\ddots&\vdots\\\frac{\partial f_1}{\partial u_n}&\frac{\partial f_2}{\partial u_n}&\cdots&\frac{\partial f_n}{\partial u_n}\end{bmatrix}=\begin{bmatrix}a_{11}&a_{21}&\cdots&a_{n1}\\a_{12}&a_{22}&\cdots&a_{n2}\\\vdots&\vdots&\ddots&\vdots\\a_{1n}&a_{2n}&\cdots&a_{nn}\end{bmatrix}=A^T$$

---

### 3

$$\frac{\partial (u^TAu)}{\partial u}=Au+A^Tu$$

where $u\in\mathbb{R}^n, A\in\mathbb{R}^{n\times n}$. If $A=A^T$, we obtain $\frac{\partial (u^TAu)}{\partial u}=2Au$

#### Proof:

$$
\begin{aligned}
u^TAu& =\begin{bmatrix}u_{1}&u_{2}&\cdots&u_{n}\end{bmatrix}\begin{bmatrix}a_{11}&a_{12}&\cdots&a_{1n}\\\\a_{21}&a_{22}&\cdots&a_{2n}\\\vdots&\vdots&\ddots&\vdots\\a_{n1}&a_{n2}&\cdots&a_{nn}\end{bmatrix}\begin{bmatrix}u_{1}\\\\u_{2}\\\vdots\\u_{n}\end{bmatrix} \\
&=(a_{11}u_{1}+a_{12}u_{2}+\cdots+a_{1n}u_{n})u_{1}+\\ &\quad \quad (a_{21}u_{1}+a_{22}u_{2}+\cdots+a_{2n}u_{n})u_{2}+\\
&\quad \quad \cdots+(a_{n1}u_1+a_{n2}u_2+\cdots+a_{nn}u_n)u_n
\end{aligned}
$$

$$\frac{\partial(u^TAu)}{\partial u}=\begin{bmatrix}\frac{\partial(u^TAu)}{\partial u_{1}}\\\\\frac{\partial(u^TAu)}{\partial u_{2}}\\\\\vdots\\\frac{\partial(u^TAu)}{\partial u_{n}}\end{bmatrix}$$
$$\begin{aligned}&=\begin{bmatrix}a_{11}u_1+(a_{11}u_1+a_{12}u_2+\cdots+a_{1n}u_n)+a_{21}u_2+\cdots+a_{n1}u_n\\a_{12}u_1+a_{22}u_2+(a_{21}u_1+a_{22}u_2+\cdots+a_{2n}u_n)+\cdots+a_{n2}u_n\\\vdots\\a_{1n}u_1+a_{2n}u_2+\cdots+a_{nn}u_n+(a_{n1}u_1+a_{n2}u_2+\cdots+a_{nn}u_n)\end{bmatrix}\\&=\begin{bmatrix}(a_{11}u_1+a_{12}u_2+\cdots+a_{1n}u_n)+(a_{11}u_1+a_{21}u_2+\cdots+a_{n1}u_n)\\(a_{21}u_1+a_{22}u_2+\cdots+a_{2n}u_n)+(a_{12}u_1+a_{22}u_2+\cdots+a_{n2}u_n)\\\vdots\\(a_{n1}u_1+a_{n2}u_2+\cdots+a_{nn}u_n)+(a_{1n}u_1+a_{2n}u_2+\cdots+a_{nn}u_n)\end{bmatrix}\end{aligned}$$

$$
\begin{aligned}
&=\begin{bmatrix}a_{11}u_{1}+a_{12}u_{2}+\cdots+a_{1n}u_{n}\\a_{21}u_{1}+a_{22}u_{2}+\cdots+a_{2n}u_{n}\\\vdots\\a_{n1}u_{1}+a_{n2}u_{2}+\cdots+a_{nn}u_{n}\end{bmatrix}+\begin{bmatrix}a_{11}u_{1}+a_{21}u_{2}+\cdots+a_{n1}u_{n}\\a_{12}u_{1}+a_{22}u_{2}+\cdots+a_{n2}u_{n}\\\vdots\\a_{1n}u_{1}+a_{2n}u_{2}+\cdots+a_{nn}u_{n}\end{bmatrix} \\
&=\begin{bmatrix}a_{11}&a_{12}&\cdots&a_{1n}\\a_{21}&a_{22}&\cdots&a_{2n}\\\vdots&\vdots&\ddots&\vdots\\a_{n1}&a_{n2}&\cdots&a_{nn}\end{bmatrix}\begin{bmatrix}u_{1}\\\\u_{2}\\\vdots\\u_{n}\end{bmatrix}+\begin{bmatrix}a_{11}&a_{21}&\cdots&a_{n1}\\\\a_{12}&a_{22}&\cdots&a_{n2}\\\vdots&\vdots&\ddots&\vdots\\\\a_{1n}&a_{2n}&\cdots&a_{nn}\end{bmatrix}\begin{bmatrix}u_{1}\\\\u_{2}\\\vdots\\u_{n}\end{bmatrix} \\
&=Au+A^Tu
\end{aligned}
$$

---

### 4

$$\frac{\partial^2 (u^TAu)}{\partial^2 u}=A+A^T$$

where $u\in\mathbb{R}^n, A\in\mathbb{R}^{n\times n}$. If $A=A^T$, we obtain $\frac{\partial ^2(u^TAu)}{\partial^2 u}=2A$

---

## 4. The chain rule for matrix derivatives

Consider a scalar function $J=f(y(u))\in\mathbb{R}$, where $y(u)\in\mathbb{R}^m$, $u\in\mathbb{R}^n$.
According denominator layout, We can know $\frac{\partial J}{\partial u}\in\mathbb{R}^n$.

> The chain rule for scalar derivatives:
>
> $$\frac{\partial J}{\partial u}=\frac{\partial J}{\partial y}\frac{\partial y}{\partial u}$$
>
> where $J,y,u\in\mathbb{R}$.

If We directly use the chain rule for scalar derivatives to cope matrix, the result as
$$\underbrace{\frac{\partial J}{\partial y}}_{\mathbb{R}^m}\underbrace{\frac{\partial y}{\partial u}}_{\mathbb{R}^{n\times m}}$$

can't be calculated.

Therefor, the chain rule for matrix derivatives :
$$\frac{\partial J}{\partial u}=\frac{\partial y}{\partial u}\frac{\partial J}{\partial y}\in\mathbb{R}^n$$

> It's easy to proof by using the chain rule for scalar derivatives to each element in the matrix.

### 5

$$\frac{\partial J}{\partial u}=2A^TBy$$

where $u\in\mathbb{R}^n, A\in\mathbb{R}^{m\times n}$, $y(u)=Au\in\mathbb{R}^m$, $B\in\mathbb{R}^{m\times m}$, $J=y^TBy\in\mathbb{R}$.

---

## The derivatives of scalar function by matrix

Consider a scalar function $f(K)$ by matrix $K\in\mathbb{R}^{m\times n}$,

$$\frac{\partial f(K)}{\partial K}\triangleq\begin{bmatrix}\frac{\partial f(K)}{\partial k_{11}}&\frac{\partial f(K)}{\partial k_{12}}&\cdots&\frac{\partial f(K)}{\partial k_{1m}}\\\\\frac{\partial f(K)}{\partial k_{21}}&\frac{\partial f(K)}{\partial k_{22}}&\cdots&\frac{\partial f(K)}{\partial k_{2m}}\\\vdots&\vdots&\ddots&\vdots\\\frac{\partial f(K)}{\partial k_{n1}}&\frac{\partial f(K)}{\partial k_{n2}}&\cdots&\frac{\partial f(K)}{\partial k_{nm}}\end{bmatrix}$$

---
## Application: Linear regression
- Least squares method
- Gredient descent
