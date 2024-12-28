---
title: Discrete Fourier transform
date: 2023-02-29 13:00:00 +0900 #Tokyo
categories: [Control Theory, Digital Signal Processing]
tags: [Fourier transform, DFT, JP]
author: Youkoutaku
math: true #Mathematical
mermaid: true
#pin:
img_path: /src/Signal-Processing/
#image:
#  path:
#  alt:
---

### 5.1 離散時間フーリエ変換の導出

サンプル値の Fourier transform

- サンプル値:

  $$
  x_{sample}(t)=\sum_{n=0}^{\infty}x(nT)\delta(t-nT)
  $$

  $x(nT)→x(n)$

- Fourier transform:

  $$
  F\{x_{sample}(t)\}=X_{sample}(\omega)=\int_{-\infty}^{\infty} [\sum_{n=0}^{\infty}x(n)\delta(t-nT)]e^{-j\omega t}dt
  $$

  $$
  =\sum_{n=0}^{\infty}x(n)\int_{-\infty}^{\infty}\delta(t-nT)e^{-j\omega t}dt
  $$

#### 離散時間フーリエ変換：

$$
X(\omega)=\sum_{n=0}^{\infty}x(n)e^{-j\omega nT}
$$

$$
x(n)=\frac{1}{2\pi}\int_{-\pi}^{\pi}X(\omega)e^{jn\omega T}d\omega
$$

これは， **離散時間フーリエ変換** という．時間は離散だが $(t→nT)$,周波数 $-\pi/T<\omega<\pi/T$ は，連続的な実数である．

### 5.2 ★DFT(離散フーリエ変換)の導出

離散時間フーリエ変換に従い，周波数 ω を離散化する

$$
\omega=[-\pi/T, \pi/T)
$$

⇒

$$
\omega_k=\frac {2\pi}{T} \frac kN, k= -N/2,\dots,N/2-1
$$

\*\*周波数 $ω$ を $k$ で $N$ 個の点に離散化する．\*\*

$$
X(k)=\sum_{n=0}^{\infty}x(n)e^{-j \frac {2\pi}N kn}
$$

> 逆離散フーリエ変換：

$$
x(n)=\frac{1}{N}\sum_{k=0}^{N-1}X(k)e^{j\frac{2\pi}{N}kn}
$$

ここで，

$$
\omega_k=\frac {2\pi}{T} \frac kN \in [-\pi/T, (\pi/T-\frac {2\pi /N}T)]
$$

習慣的に**マイナスを避ける**ために，$k=0,\dots,N-1$

$$
\omega_k=\frac {2\pi}{T} \frac kN \in [0, (2\pi/T-\frac {2\pi /N}T)]
$$

$\color{red}{W=e^{-j\frac {2\pi}N}}$ とすると，

$$
F\{x_{sample}(t)\}=X_{sample}(\omega)=X(k)
$$

$$
X(k)=\sum_{n=0}^{N-1}x(n)W^{kn}
$$

これを離散フーリエ変換（DFT:discrete Fourier transform）という．k は周波数に関する変数，n は時間に関する変数．

### 5.3 ★ 逆離散フーリエ変換

$$
x(n)=F^{-1}\{X(k)\}
$$

$$
=\frac 1N\sum_{k=0}^{N-1}X(k)W^{-kn}
$$

### 5.4 離散フーリエ変換の性質

#### 重ね合わせ定理

#### ★ 周期性 (複素数 W の周期性による)

$$
X(k+rN)=X(k)
$$

#### ★ 対称性

$$
X(N-k)=X^*(k)
$$

$X^*(k)$ は, $X(k)$ の共役複素数である．

#### 線形性
