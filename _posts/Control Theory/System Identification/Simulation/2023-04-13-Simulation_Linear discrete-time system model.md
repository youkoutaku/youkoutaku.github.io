---
title: Simulation_Linear discrete-time system models
date: 2023-04-13 15:33:00 +0900
categories: [Control Theory, System Identification]
tags: [Matlab, Control, Discrete-time, Linear System, JP]
author: Youkoutaku
math: true
---

## 前頭:

```matlab
close all % 全てのグラフfigureを閉じる

clear %メモリから変数と関数を消去

set(0,'DefaultAxesFontName','Times New Roman')  %ローマ字変換

set(0,'DefaultAxesFontSize',12)　%グラフの座標の文字サイズ
```

$$
	x_t=-a_1x_{t-1}-a_2x_{t-2}+b_1u_{t-1}+b_2u_{t-2}
$$

$$
(1+a_1z^{-1}+a_2z^{-2})X(z)=(b_1z^{-1}+b_2z^{-2})U(z)
$$

$$
	H(z)=\frac{X(z)}{H(z)}=\frac{b_1z^{-1}+b_2z^{-2}}{1+a_1z^{-1}+a_2z^{-2}}=\frac{z^{-1}+0.5z^{-2}}{1-1.5z^{-1}+0.7z^{-2}}=\frac{z+0.5}{z^2-1.5z+0.7}
$$

$$
    H(z)=\frac{ z^{-1}(1+0.5z^{-1}) }{ (1-\frac{1.5+j\sqrt{0.55}}{2}z^{-1})(1-\frac{1.5-j\sqrt{0.55}}{2}z^{-1})}
$$

> MATLAB でディジタル信号を扱う時に，そのまま差分方程式を使える．z 変換などが要らない．

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',12)

%--------------------------------------------------------------------
%  同定ゼミ宿題pdf p.13 線形離散時間システムモデルのシミュレーション
%                                                       4/13 by YANG
%---------------------------------------------------------------------

%初期パラメータ
Tmax=10;
samp=0.01;
n=Tmax/samp;
u=rand(1,n+1); %入力

NSR = 0.0;
%NSR 0, 0.1 0.2

a1=-1.5;
a2=0.7;
b1=1.0;
b2=0.5;

t = zeros(1, n+1);
x = zeros(1, n+1); %真の出力
y = zeros(1, n+1); %観察値

for k=3:n+1
    t(k)=(k-1)*samp;
    x(k) = -a1*x(k-1)-a2*x(k-2)+b1*u(k-1)+b2*u(k-2);
end

%ゼロ平均のガウス分布乱数=正規分布
mu=0 ;%平均値
sigma=NSR*std(x) ;%標準偏差 NSR=sigma/std(x)
v = sigma * randn(1,n+1) + mu;

for k=3:n+1
    y(k) = x(k)+v(k);
end

subplot(2,2,1); plot(t,u); title('ut');
subplot(2,2,2); plot(t,x); title('xt');
subplot(2,2,3); plot(t,y); title('yt');
subplot(2,2,4); plot(t,v); title('vt');
```
