---
title: FIR Filter
date: 2023-03-13 10:00:00 +0900 #Tokyo
categories: [Control Theory, Digital Signal Processing]
tags: [Filter, FIR, JP]
author: Youkoutaku
math: true #Mathematical
mermaid: true
#pin:
img_path: /src/Signal-Processing/
#image:
#  path:
#  alt:
---

## 10. FIR フィルタ

定義： FIR フィルタ は， Transfer Function が分母をもたないディジタルフィルタ ．

> (finite impilse repose, 有限インパルス応答)伝達関数 $H(z)$ が分母がないと， 非巡回型システム である．

特徴:
(1) 安定性 が保証されたフィルタを実現できる．
(2)設計が比較的容易である．
(3)ひずみのない位相特性，つまり， **直線位相特性** を容易に実現できる.

比較：

- FIR フィルタ は（直線）位相特性を重視フィルタ.
- IIR Filter は振幅（急峻な減衰）特性を重視したフィルタ．

$$
\frac {Y(z)}{X(z)}=H(Z)=b_0+b_1z^{-1}+\dots+b_Jz^{-J}=\sum_{i=0}^{J} b_iz^{-i}
$$

---

### 10.1 理想的なフィルタ

$$
H(\omega)=\begin{cases}
1・e^{-j\omega T}, &(|\omega|\leq \omega_c)
\\0,&(|\omega|> \omega_c)
\end{cases}
$$

逆ラプラス変換 すると， インパルス応答 が得られる．

$$
h(t)=\frac {1}{2\pi} \int_{-\omega_c}^{\omega_c} 1・e^{-j\omega\tau }e^{j\omega t}d\omega
$$

$$
=\frac {1}{2\pi} [2\frac {\sin {\omega(t-\tau)} }{(t-\tau)}]_{0}^{\omega_c} =\frac {\sin {\omega_c(t-\tau)} }{\pi(t-\tau)}
$$

### 10.2 FIR フィルタと直線位相特性

デジタルフィルタの 差分方程式 ：

$$
y(n)=h(0)x(n)+h(1)x(n-1)+h(2)x(n-2)+\dots+h(N-1)x(n-(N-1))
$$

$$
=\sum_{k=0}^{N-1}h(k)x(n-k)
$$

Blog/subpages/Transfer Function ：

$$
H(z)=h(0)+h(1)z^{-1}+h(2)z^{-2}+\dots+h(N-1)z^{-(N-1)}=\sum_{n=0}^{N-1} h(n)z^{-n}
$$

$N$ が奇数の場合：

$$
H(z)=\sum_{n=0}^{\frac {N-1}{2}-1}h(n)z^{-n}+h(\frac {N-1}{2})z^{-\frac {N-1}{2}}+\sum_{n=\frac {N-1}{2}-+1}^{N-1}h(n)z^{-n}
$$

$$
\sum_{n=\frac {N-1}{2}-+1}^{N-1}h(n)z^{-n}=\sum_{n=0}^{\frac {N-1}2-1}h(N-1-n)z^{-(N-1-n)}
$$

**インパルス応答が偶対称**すれば，直線位相を持つ

$$
h(n)=h(N-1-n), (n=0,1,2,\dots,N-1)
$$

よって，伝達関数：

$$
H(z)=\sum_{n=0}^{\frac {N-1}{2}-1}h(n)z^{-n}+h(\frac {N-1}{2})z^{-\frac {N-1}{2}}+\sum_{n=0}^{\frac {N-1}2-1}h(n)z^{-(N-1-n)}
$$

$$
=z^{-\frac {N-1}{2}}
\{
\sum_{n=0}^{\frac {N-1}{2}-1}h(n)z^{-n+\frac {N-1}{2}}+
h(\frac {N-1}{2})+
\sum_{n=0}^{\frac {N-1}{2}-1}h(n)z^{-(N-1-n)+\frac {N-1}{2}}
\}
$$

$$
=z^{-\frac {N-1}{2}}\{
h(\frac {N-1}{2})+
\sum_{n=0}^{\frac {N-1}{2}-1}h(n)(z^{-n+\frac {N-1}{2}}+z^{n-\frac {N-1}{2}})\}
$$

周波数特性 $z=e^{j\omega T}$

$$
H(e^{j\omega T})=e^{-j\omega\frac {N-1}{2}T}[h(\frac {N-1}{2})+
\sum_{n=0}^{\frac {N-3}{2}}h(n)(e^{-j\omega(n-\frac {N-1}{2})T}+e^{j\omega(n-\frac {N-1}{2})T})]
$$

$$
=e^{-j\omega\frac {N-1}{2}T}[h(\frac {N-1}{2})+
2\sum_{n=0}^{\frac {N-3}{2}}h(n)\cos{\{\omega(n-\frac {N-1}{2}) T}\}]
$$

- 振幅特性

  $$
  \mid
  h(\frac {N-1}{2})+
  2\sum_{n=0}^{\frac {N-3}{2}}h(n)\cos{\{\omega(n-\frac {N-1}{2}) T}\}
  \mid
  $$

- 位相特性

  $$
  \theta(\omega)=-\omega\frac {N-1}{2}T\dots(直線位相)
  $$

- ★ Blog/Digital Signal Processing/subpages/群遅延

  $$
  \tau(\omega)=-\frac {\partial\theta}{\partial\omega}=\frac {N-1}{2}T\dots(群遅延)
  $$

  (意味：波形全体の遅れを表している)
