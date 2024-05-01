---
title: PID Simulation
date: 2023-05-02 11:00:00 +0900
categories:[System Identification, ]
tags:[MATLAB] [PID]
author: <author_id>
math: true
---

[[制御対象]]：
$$G(s)=\frac{12s+8}{20s^4+112s^3+147s^2+62s+8}$$
[[状態空間表現]]に変換する．
```MATLAB
num = [0 0 12 8];

den = [20 113 147 62 8];

sysg=tf(num, den); %伝達関数

[Ao,Bo,Co,Do] = tf2ss(num,den); %tf2ss伝達関数表現から状態空間表現への変換
```

---
[[PID]]:
>$k \to n$
$$e(n)=r(n)-y(n-1)$$
>偏差 = 目標値 - **一個前の制御量**


$$U(z)=E(z)C(z)$$
>z[操作量] = z[偏差]・z[制御器]

---

PID制御器伝達関数：
$$C(s)=c_p+c_i\frac{1}{s}+c_d\frac{s}{\gamma s+1}, \quad \gamma=4\times T$$
```MATLAB
gamma=4*T; %γ
cp=6; %比例
ci=1; %積分
cd=7; %微分
```
---

# [[前進差分]]

## 方法1
- すごく面倒くさい方法：（諦めよう！）
>前進差分によるPID制御器のz変換:
>$$C(z)=c_p+c_i\frac{Tz^{-1}}{1-z^{-1}}+c_d\frac{1-z^{-1}}{\gamma+(T-\gamma)z^{-1}}  $$
>$$U(z)=E(z)C(z)$$
>逆z変換(徐々に):
>$$u(n)=c_pe(n)+c_i\frac{Te(n-1)}{1-z^{(-1)}}+c_d\frac{e(n)-e(n-1)}{\gamma+(T-\gamma)z^{-1}}$$
>1. 両辺を$1-z^{-1}$を掛けると，
>$$\begin{align}u(n)-u(n-1)=&c_pe(n)-c_pe(n-1)\\ &+c_iTe(n-1)\\ &+\frac{c_d}{\gamma+(T-\gamma)z^{-1}}\left( (e(n)-e(n-1))-(e(n-1)-e(n-2))   \right)\end{align}$$
>2. 両辺を$\gamma+(T-\gamma)z^{-1}$を掛けると，
>$$\begin{align}\gamma u(n)+(T-\gamma)u(n-1)\\-\gamma u(n-1)-(T-\gamma)u(n-2)=&c_p\gamma \left(e(n)-e(n-1)\right)\\&+(T-\gamma)c_p(e(n-1)-e(n-2))\\&+ c_iT\gamma e(n-1)+c_iT(T-\gamma)e(n-2)\\ &+c_d(e(n)-e(n-1)-e(n-1)+e(n-2))\end{align}$$
>3. 操作量$u(n)$$$\begin{align}u(n)=&\frac{1}{\gamma}[c_p\gamma \left(e(n)-e(n-1)\right)\\&+(T-\gamma)c_p(e(n-1)-e(n-2))\\&+ c_iT\gamma e(n-1)+c_iT(T-\gamma)e(n-2)\\&+c_d(e(n)-2e(n-1)+e(n-2)) \\&+(2\gamma-T)u(n-1)+(T-\gamma)u(n-2) ]\\=&( (c_p\gamma + c_d )e(n) \\&+ ( c_p( T- 2\gamma) + c_iT\gamma - 2c_d )e(k-1) \\&+(c_p(\gamma-T)+c_iT(T-\gamma)+c_d )e(k-2) \\&+ (2\gamma-T)u(k-1) + (T-\gamma)u(k-2) ) / \gamma\end{align}$$
>
```MATLAB
u(k)= ( (cp*gamma + cd )*e(k) + ( cp*( T- 2*gamma) + ci*T*gamma - 2*cd )*e(k-1) + (cp*(gamma-T)+ci*T*(T-gamma)+cd )*e(k-2) + (2*gamma-T)*u(k-1) + (T-gamma)* u(k-2) ) / gamma;
```

>プログラミング化する場合に，eの各項の係数を揃ったら，PCでの計算がミスがない．係数が揃えないと，ミスが生じる

## 方法2
**P-I-D**を分解して，シミュレーションを行うと，
P:
$$u_p(n)=c_pe(n)$$
>偏差を比例したもので制御する

I:
$$u_i(n)=c_i\frac{Tz^{-1}}{1-z^{-1}}e(n)$$
$$\begin{align}u(n)-u_i(n-1)=c_iTe(n-1)\\\to u_i(n)=u(n-1)+c_iTe(n-1)\end{align}$$
>偏差の累積で制御する

D:
$$u_d(n)=c_d\frac{1-z^{-1}}{\gamma+(T-\gamma)z^{-1}}e(n)$$
$$\begin{align}\gamma u_d(n)+(T-\gamma)u_d(n-1)=c_d(e(n)-e(n-1)\\\to u_d(n)=\frac{c_d}{\gamma}\left(e(n)-e(n-1)-\frac{T-\gamma}{\gamma}u_d(n-1)\right)\end{align}$$
>偏差の変化で制御するつもりだが，初期状態には変化がない．
>微分のs×ローパスフィルタ1/(γs+1)

前進差分による状態空間表現：
$$x(n+1)=x(n)+T(Ax(n)+bu(n))
$$
$$y(n)=Cx(n)+Du(n)$$
>状態空間表現において，行列計算を行う．そのためにプログラミングにおける行列のIndexを考えなければいけない．

```MATLAB
x = zeros(4, n+1); %状態変数 4次元 
```

```MATLAB
	x(:,k)= x(:,k-1) + T*(Ao*x(:,k-1) + Bo*u(k-1));
	y(k)= Co*x(:,k)+Do*u(k);
```
>`x(;,k)`というように，xの第k列のすべて要素で計算を行う

---
# [[後退差分]]
後退差分によるPID制御器の[[z変換]]:
$$C(z)=c_p+c_i\frac{T}{1-z^{-1}}+c_d\frac{1-z^{-1}}{T(\frac{\gamma}{T}(1-z^{-1})+1)}=c_p+c_i\frac{T}{1-z^{-1}}+c_d\frac{1-z^{-1}}{\gamma(1-z^{-1})+T}$$

PID分解して，シミュレーションを行うと，
P:
$$u_p(n)=c_pe(n)$$
I:
$$u_i(n)=c_i\frac{T}{1-z^{-1}}e(n)$$
$$\begin{align}u(n)-u_i(n-1)=c_iTe(n)\\\to u_i(n)=c_iTe(n)+u_i(n-1)\end{align}$$
D:
$$u_d(n)=c_d\frac{1-z^{-1}}{\gamma(1-z^{-1})+T}e(n)$$
$$\begin{align} (\gamma+T)u_d(n)-\gamma u_d(n-1))=c_d(e(n)-e(n-1)) \\\to u_d(n)=\left( c_d(e(n)-e(n-1)) +\gamma u_d(n-1) \right)/(\gamma+T)\end{align}$$
後退差分による[[状態空間表現]]：
$$x(n)-x(n-1)=T(Ax(n)+bu(n)) \to x(n)=(I-TA)^{-1}\:(x(n-1)+Tbu(n))
$$
$$y(n)=Cx(n)+Du(n)$$

---
# [[双1次変換法]]
双一次変換法によるPID制御器のz変換:
$$\begin{align}C(z)&=c_p+c_i\frac{T}{2}\frac{1+z^{-1}}{1-z^{-1}}+c_d\frac{1}{\gamma+\frac{T(1+z^{-1})}{2(1-z^{-1})}}\\&=c_p+\frac{Tc_i}{2}\frac{1+z^{-1}}{1-z^{-1}}+2c_d\frac{1-z^{-1}}{2\gamma+T+(T-2\gamma)z^{-1}}\end{align}$$

PID分解して，シミュレーションを行うと，
P:
$$u_p(n)=c_pe(n)$$
I:
$$\begin{align}u_i(n)-u_i(n-1)=\frac{c_iT}{2}(e(n)+e(n-1))\\\to u_i(n)=\frac{c_iT}{2}(e(n)+e(n-1))+u_i(n-1)\end{align}$$
D:
$$\begin{align}(2\gamma+T)u_d(n)+(T-2\gamma)u_d(n-1)=2c_d(e(n)-e(n-1)) \\\to u_d(n)= \frac{2c_d(e(n)-e(n-1))+(2\gamma-T)u_d(n-1)}{2\gamma+T}
\end{align}$$

双一次変換法による状態空間表現：
$$\begin{align}\frac{2}{T}(x(n)-x(n-1))=A(x(n)+x(n-1))+b(u(n)+u(n-1)) \\\to (2I-AT)x(n)=(2I+AT)x(n-1)+bT(u(n)+u(n-1))\\
\to x(n)=(2I-TA)^{-1}[(2I+AT)x(n-1)+bT(u(n)+u(n-1))]
\end{align}$$
$$y(n)=Cx(n)+Du(n)$$

---
[[../../../Math/Liner Algebra/線形代数入門/行列/行列|行列]]計算する時，行列の次元注意
```MATLBA
inv() %逆行列
eye() %単位行列I

./ 
.* %行列個別計算
```

>最も注意すべきなのは，括弧と正負の符号である．
>理由は，コードの数式が読みずらい．
>解決策：
>1. コードをLaTex公式に入れて，確認する[在线LaTeX公式编辑器-编辑器 (latexlive.com)](https://www.latexlive.com/)
>2. 目と手でもう一回計算して確認する

>- font 字のサイズ
>- x軸，y軸 ラベルと単位
>- zero-orderHoldeにより，因果関係である．

``` C
for k=1:N+1
	y(k) = Cx(k) + Du(k)
	e(k) = r(k) - y(k)
	
	%離散方法
	up(k)= 
	ui(k)=
	ud(k)=
	u(k) =
	
	%前進差分で出力 
	x(k+1)= x(k) + T*( A*x(k)+B*u(K) )
end 
```

>1. 実際の場合，センサからの観測値をもらうので，y(t)が先の方である．
>2. しかし，y(t)を先にすると，$y=Cx+Du$における$u$がずっと0で計算している．普通に$D=0$ので影響がない．
>3. また，現在の入力 $u(t)$ を求めたい．$u(t)$を求めるために，現在の出力（観察値）$y(t)$が必要である．出力を得るために，現在まだ求めていない入力を使うべきではない．