---
title: Linear Continuous-time Model
date: 2023-04-10 18:55:00 +0900 #Tokyo
categories: [Control Theory, System Identification]
tags:
  [
    Linear System,
    Control,
    Transfer function,
    Fourier transform,
    Impulse response,
    Convolution,
    JP,
  ]
author: Youkoutaku
math: true #Mathematical
---

[システム同定ゼミ]http://yoh.ise.ibaraki.ac.jp/

## 1.1 モデルの表現

### 1.1.1 線形定係数常微分方程式

$$
\dfrac{d^ny(t)}{dt^n}+a_{n-1}\dfrac{d^{n-1}y(t)}{dt^{n-1}}+\cdots+a_1\dfrac{dy(t)}{dt}+a_0y(t)=b_m\dfrac{d^m u(t)}{dt^m}+b_{m-1}\dfrac{d^m-1}a(t)\\ dt^{m-1}+\cdots
+b_1\dfrac{du(t)}{dt}+b_0u(t)
$$

### 1.1.2 ラプラス変換

- ラプラス変換

- 逆ラプラス変換（あまり使わない）

- 最終値の定理

$$
	\lim_{ t\to \infty} f(t) = \lim_{ s\to 0} sF(s)
$$

- 初期値の定理 (あまり使わない)

$$
	\lim_{ t\to 0} f(t) = \lim_{ s\to \infty} sF(s)
$$

### 1.1.3 フーリエ変換

- フーリエ変換
- パーセバルの等式

$$
	\int_{0}^{\infty}f^2(t) dt=\frac{1}{2\pi}\int_{-\infty}^{\infty}|F(j\omega)|^2d\omega
$$

信号のエネルギーが周波数領域と時間領域で等しいことを表す等式である．
左: 時間領域における信号の 2 乗=power.
右: 周波数領域における信号の 2 乗=power.

### 1.1.4 インパルス応答と伝達関数

#### 畳み込み表現

$$
	y(t) = \int_{0}^{t}g(t-r)u(r)dr = \int_{0}^{t}g(r)u(t-r)dr
$$

- インパルス応答

#### 離散インパルス信号

$$
\delta(n)=
	\begin{cases}
	1 &(n=0)\\
	0 &(n\neq0)
	\end{cases}
$$

#### 伝達関数の周波数表現

$s=j\omega$

## 1.2 モデルの実例

微分方程式：

$$
	L\frac{di}{dt}+Ri=u(t), \: i(0)=0
$$

伝達関数：

$$
	G(s)=\frac{1}{Ls+R}=\frac{1}{L}\frac{1}{(s+\frac{R}{L})}
$$

### (1)単位インパルス

$u(t)=\delta(t)$:

**インパルス応答**：

$$
	g(t)=\mathcal{L}^{-1}|G(s)|=\frac{1}{L}e^{-\frac{R}{L}t}
$$

#### 意味：

マイナスの実数根であるので，安定性である．

インパルス応答のフーリエ変換は，周波数応答になり，

$$
\begin{equation}
	\begin{aligned}
			\mathcal{F}[g(t)]&=\int_{0}^{\infty}\frac{1}{L}e^{-\frac{R}{L}t}e^{-j\omega t}dt \\
	&=\frac{1}{L}\frac{-1}{\frac{R}{L}+j\omega}
	\left[ e^{-(\frac{R}{L}+j\omega)t} \right]_{0}^{\infty} \\
	&=\frac{-1}{R+Lj\omega}(0-1) \\
	&=\frac{1}{Lj\omega+R}
	\end{aligned}
\end{equation}
$$

つまり，伝達関数に $s=j\omega$ に代入することと同じである．

### (2) **交流起電力**

$u(t)=E_0\sin{\omega t}$

$$
	U(s)=E_0\frac{\omega}{s^2+\omega^2}
$$

応答：

$$
\begin{equation}
	\begin{split}
	i(t)&=\mathcal{L}^{-1}\left[G(s)U(s)\right]\\
	&=E_0\omega\mathcal{L}^{-1}\left[ \frac{1}{(s^2+\omega^2)(Ls+R)}
	\right]\\
	\end{split}
\end{equation}
$$

部分分数展開：

$$
	P(s)=\frac{1}{(s^2+\omega^2)(Ls+R)}=\frac{a}{Ls+R}+\frac{bs}{s^2+\omega^2}+\frac{c}{s^2+\omega^2}
$$

$$
\begin{equation}
	=\frac{as^2+a\omega^2+bLs^2+bRs+cLs+cR}{(s^2+\omega^2)(Ls+R)}
\end{equation}
$$

$s^2:$

$$
a+bL=0 \to b=-\frac{a}{L}
$$

$s:$ $
$bR+cL=0 \to -\frac{a}{L}R+cL=0 \to c=a\frac{R}{L^2}$$

$s^0:$

$$
a\omega^2+cR=1 \to a\omega^2+a\frac{R^2}{L^2}=1 \to a=\frac{L^2}{R^2+\omega^2L^2}
$$

そして，

$$
	b=-\frac{L}{R^2+\omega^2L^2},\: c=\frac{R}{R^2+\omega^2L^2}
$$

よって，

$$
\frac{1}{(s^2+\omega^2)(Ls+R)}=\frac{L^2}{R^2+\omega^2L^2}(\frac{1}{Ls+R})-\frac{L}{R^2+\omega^2L^2}(\frac{s}{s^2+\omega^2})+\frac{R}{R^2+\omega^2L^2}(\frac{1}{s^2+\omega^2})
$$

返して，応答は

$$
\begin{equation}
	\begin{split}	i(t)&=\frac{E_0\omega}{R^2+\omega^2L^2} \mathcal{L}^{-1}
	\left[
		\frac{L^2}{Ls+R}-\frac{Ls}{s^2+\omega^2}+\frac{R}{s^2+\omega^2}
	\right] \\
	&=\frac{E_0\omega}{R^2+\omega^2L^2}\left(
		Le^{-\frac{R}{L}t}-L\cos{\omega t+\frac{R}{\omega}\sin{\omega t}}
	\right)
	\end{split}

\end{equation}
$$

#### 意味:

過渡解: $s=-\frac{R}{L}$
(共役複素根)強制解: $s=(j\omega) \:or (-j\omega)$

**低周波と高周波に対しての評価:**
$\omega \to \infty:$ $i(t)\to0$ ので，lowpass フィルタである．

### (3)ステップ入力

$u(t)=1\:(t\ge0)$

$$
U(s)=\frac{1}{s}
$$

**ステップ応答**：

$$
\begin{equation}
	\begin{split}	i(t)&=\mathcal{L}^{-1}[G(s)U(s)] \\
	&=\mathcal{L}^{-1}\left[ \frac{1}{s(Ls+R)}
	\right]
	\end{split}

\end{equation}
$$

ヘビサイドの公式で部分分数展開：

$$
	P(s)=\frac{1}{s(Ls+R)}=c_1\frac{1}{s}+c_2{\frac{1}{Ls+R}}
$$

$$
\begin{equation}
	\begin{split}	&c_1=sP(s)|_{s=0}=\frac{1}{L\times0+R}=\frac{1}{R}\\
	&c_2=(Ls+R)P(s)|_{s=-\frac{R}{L}}=-\frac{L}{R}
	\end{split}

\end{equation}
$$

よって，ステップ応答は(**一次遅れ系のステップ応答**)

$$
\begin{equation}
	\begin{split}	i(t)&=\mathcal{L}^{-1}\left[ \frac{1}{s(Ls+R)}
	\right] \\
	&=\mathcal{L}^{-1}\left[ \frac{1}{R}\frac{1}{s}-\frac{L}{R}\frac{1}{Ls+R}
	\right] \\
	&=\frac{1}{R}\mathcal{L}^{-1}\left[\frac{1}{s}-\frac{1}{s+\frac{R}{L}}
	\right] \\
	&=\frac{1}{R}(1-e^{-\frac{R}{L}t})
	\end{split}

\end{equation}
$$

#### 意味:

$t=\frac{L}{R}$で$i(t)=\frac{1}{R}(1-e^{-1})\thickapprox 0.632\times \frac{1}{R}$ 定常値 63.3%

この時，$\frac{L}{R}$ が応答の速さを支配するパラメータで，時定数とよばれる．$\frac{L}{R}$ が小さければ，応答が早く，逆に大きければ遅くなる．
