---
title: Fourier-series
date: 2023-02-13 11:00:00 +0900 #Tokyo
categories: [Control Theory, Digital Signal Processing]
tags: [Fourier series, JP]
author: Youkoutaku
math: true #Mathematical
mermaid: true
#pin:
img_path: /src/Signal-Processing/
#image:
#  path:
#  alt:
---

## フーリエ級数

周期関数の周期信号を扱う．複数の三角関数で信号関数を表す．

> ベクトルの内積の視線から見ると，ベクトルを二つ基底ベクトルの線形結合で表される．よって，ある関数を正弦波と余弦波の線形結合で表される．

$$
x(t)=x(t+T),\;f_0=\frac{1}{P},\;\omega_0=\frac{2\pi}{P}
$$

Fourier series：

$$x(t)=\frac{a_0}{2}+\sum_{n=1}^{\infty}\left( a_n\cos{n\omega_0t}+b_n\sin{n\omega_0t}\right)$$

> 関数の内積：
> $a_n$は余弦波成分の係数である．関数と余弦関数の内積で求める．
> $b_n$は正弦波成分の係数である．関数と正弦関数の内積で求める．

$$a_n=\frac{2}{P}\int_{0}^{P}x(t)\cos{n\omega_0t}\;dt,\quad b_n=\frac{2}{P}\int_{0}^{P}x(t)\sin{n\omega_0t}\;dt$$

直交性：(関数の内積の特性：自分自身の写像が 1)

$$\frac{2}{P}\int_{0}^{P}\cos{m\omega_0t}\cos{n\omega_0t}\;dt=\delta_{mn}$$

$$\frac{2}{P}\int_{0}^{P}\sin{m\omega_0t}\sin{n\omega_0t}\;dt=\delta_{mn}$$

$$\frac{2}{P}\int_{0}^{P}\sin{m\omega_0t}\cos{n\omega_0t}\;dt=0$$

クロネッカのデルタ：

$$\delta_{mn}=\begin{cases}1\quad m=n\\0\quad m\ne n\end{cases}$$

パーセバルの等式：

$$\frac{2}{P}\int_{0}^{P}x^2(t)dt=\frac{a_0^2}{2}+\sum_{n=1}^{\infty}(a_n^2+b_n^2)$$

> 信号の平均パワー

複素数表現：

$$e^{j\omega_0t}=\cos{\omega_0t}+\sin{\omega_0t}$$

オイラーの公式により，

$$\cos{\omega_0t}=\frac{e^{j\omega_0t}+e^{-j\omega_0t}}{2},\quad \sin{\omega_0t}=\frac{e^{j\omega_0t}-e^{-j\omega_0t}}{2j}$$

$$\begin{align}a_n\cos{n\omega_0t}+b_n\sin{n\omega_0t}&=a_n\frac{e^{jn\omega_0t}+e^{-jn\omega_0t}}{2}+b_n\frac{e^{jn\omega_0t}-e^{-jn\omega_0t}}{2j}\\&=\frac{a_n-jb_n}{2}e^{jn\omega_0t}+ \frac{a_n+jb_n}{2}e^{-jn\omega_0t} \end{align}$$

$$c_n=\frac{a_n-jb_n}{2}$$

複素フーリエ級数：

$$x(t)=\sum_{n=-\infty}^{\infty}c_ne^{j\omega_0t}$$

$$c_n=\frac{1}{P}\int_{0}^{P}x(t)e^{-jn\omega_0t}dt$$

- 複素数の内積

  $$\frac{1}{P}\int_{0}^{P}e^{jm\omega_0t}e^{-jn\omega_0t}dt=\delta_{mn}$$
  パーセバルの等式

  $$\frac{2}{P}\int_{0}^{P}x^2(t)dt=\sum_{n=-\infty}^{\infty}|c_n|^2$$
