---
title: IIR Filter
date: 2023-03-18 10:00:00 +0900 #Tokyo
categories: [Control Theory, Digital Signal Processing]
tags: [Filter, IIR, Bilinear transform, JP]
author: Youkoutaku
math: true #Mathematical
mermaid: true
#pin:
img_path: /src/Signal-Processing/
#image:
#  path:
#  alt:
---

## 9. IIR Filter

- 定義： **デジタル IIR フィルタ** は， 伝達関数 が分母を持つ離散時間フィルタ．
- 利点と欠点：FIR と比較して，低い次数で急峻な周波数特性を得ることができる．その反面，ひずみのない直線位相を満たすことは難しい．
- 方法： アナログフィルタ を離散時間へ変換（離散化）して デジタル IIR フィルタ を設計する．
  > - (infinite impiles response, 無限インパルス応答)伝達関数 $H(z)$ が分母があると， 巡回型システム である．

### 9.1 インパルス不変の方法

時間域で近似を行う方法.

すべての周波数を含む入力（ インパルス関数 ）について， 連続時間システム と デジタル時間システム の入出力関係が似ているのが望ましい．
インパルス不変の方法：すべての周波数成分を均一に含むインパルス信号を入力とし，その出力，すなわち（ 連続時間システム と デジタル時間システム ） インパルス応答 が等しくなるよう（サンプリング時刻において）に設定する方法である

- アナログフィルタの伝達関数 を逆ラプラス変換し， インパルス応答 を求める．

  $$
  H_A(s)=\frac {a_1}{s-s_1}+\frac {a_2}{s-s_2}+\dots+\frac {a_N}{s-s_N}
  $$

  $$
  h_A(t)=L^{-1}[H_A(s)]={a_1}e^{s_1t}+{a_2}e^{s_2t}+\dots+{a_N}e^{s_Nt}
  $$

- インパルス応答 を 離散化 し， デジタルフィルタ の インパルス応答 を求める．

  $$
  h_D(t)=h_A(nT)={a_1}e^{s_1nT}+{a_2}e^{s_2nT}+\dots+{a_N}e^{s_NnT}
  $$

- デジタルフィルタのインパルス応答を Z 変換すると， デジタルフィルタの伝達関数 が求められる．
  $$
  H_D(z)=Z[h_D(t)]=\frac {a_1}{1-e^{s_1T}z^{-1}}+\frac {a_2}{1-e^{s_2T}z^{-1}}+\dots+\frac {a_N}{1-e^{s_NT}z^{-1}}
  $$
  これは， デジタルフィルタである.

**注意)** 実際には，$H_D(z)T$ を ディジタルフィルタの伝達関数 とする．原因は， 畳み込み積分 の時間刻み T での近似である．

$$
H_D(z)T\approx H_A(s)
$$

- インパルス応答不変の方法の安定性 ：
  アナログフィルタ安定（伝達関数の極の実数部が負数）であれば，インパルス不変の方法によって変換されたデジタルフィルタも安定となる．

---

### 9.2★ 双 1 次変換法の方法

- 双线性变换法，Bilinear transform
- Tustin transform タスティン変換
- Trapezoidal method 台型差分法

微分を分差で近似する方法に基づく.
Bilinear transform は，積分操作 $s^{-1}$ を台形積分で近似して導出される．

- サンプリング周期 $T$ が十分小さいとき，アナログフィルタ $H_A(s)$ に双 1 次変換で代入すると，ディジタルフィルタ$H_D(z)$が得られる．
- サンプリング周期 T が十分小さくないとき，補正が必要である．

アナログフィルタの伝達関数の $s$ を双 1 次変換すでデジタルフィルタの伝達関数の $z$ に置き換える式:

$$
s=\frac 2T \frac {1-z^{-1}}{1+z^{-1}}
$$

$s=j\omega=j\omega_A, z=e^{j\omega_DT}$ を代入すると，

$$
j\omega_A=\frac 2T \frac {1-{e}^{-j\omega_DT}}{1+{e}^{-j\omega_DT}}=
 \frac 2T \frac {% raw %}{{e}^{j\omega_DT/2}-{e}^{-j\omega_DT/2}}{% endraw %}{% raw %}{{e}^{j\omega_DT/2}+{e}^{-j\omega_DT/2}}{% endraw %}=
 \frac 2T  \frac {j2\sin {\omega_DT/2}}{2\cos{\omega_DT/2}}
$$

$$
\omega_A=\frac 2T \tan {\frac {\omega_DT}{2}}
⇒ \frac {\omega_AT}2=\tan {(\frac {\omega_DT}{2})}
$$

よって，$\omega_AT$と$\omega_DT$が十分小さいと，

$$
\omega_A \approx \omega_D
$$

手順：

1. プロトタイプ(prototype,原型)となるアナログフィルタの伝達関数 を決定する．
2. $\omega_A=\frac 2T \tan {\frac {\omega_DT}{2}}$ を用いて，設計仕様をみたすアナログフィルタ特性を求める．
3. $s=\frac 2T \frac {1-z^{-1}}{1+z^{-1}}$により，$s$ の多項式を $z$ の多項式へ変換する.

- 双一次変換法の安定性 ：
  元のアナログフィルタが安定であれば，双一次変換法で離散化したディジタルフィルタも安定となる．
