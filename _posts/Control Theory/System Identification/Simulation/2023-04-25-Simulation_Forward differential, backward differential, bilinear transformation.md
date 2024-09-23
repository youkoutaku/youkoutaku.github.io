---
title: Simulation_Forward difference, Backward difference, bilinear transform
date: 2023-04-25 15:33:00 +0900
categories: [Control Theory, System Identification]
tags:
  [
    Matlab,
    Control,
    Bilinear transform,
    Forward difference,
    Backward difference,
    JP,
  ]
author: Youkoutaku
math: true
---

前進差分，後退差分，双一次変換をそれぞれ用いて，例題 1.2.1 の電気回路のステップ応答を数値計算で求め，数値計算の結果と真の応答をプロットし，比較せよ。

## 前進差分

$$
    sf(n)=\frac{f(n+1)-f(n)}{T}
$$

よって，

$$
    L\frac{i(n+1)-i(n)}{T}+Ri(n)=u(n) \to i(n+1)=\frac{1}{L}(Tu(n)+(L-RT)i(n))
$$

$$
    \to i(n)=\frac{1}{L}(Tu(n-1)+(L-RT)i(n-1))
$$

## 後退差分

$$
    sf(n)=\frac{f(n)-f(n-1)}{T}
$$

よって，

$$
    L\frac{i(n)-i(n-1)}{T}+Ri(n)=u(n) \to i(n)=\frac{1}{RT+L}(Tu(n)+Li(n-1))
$$

## 双一次変数

$$
    sf(n)=\frac{2}{T}\frac{1-z^{-1}}{1+z^{-1}}f(n)
$$

よって，

$$
L\frac{2}{T}(i(n)-i(n-1))+R(i(n)+i(n-1))=u(n)+u(n-1)
$$

$$
    \to i(n)=\frac{1}{2L+RT} \left( Tu(n)+Tu(n-1) + (2L-RT)i(n-1)  \right)
$$

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',12)

%--------------------------------------------------------------------
%  同定ゼミ宿題 電気回路応答のシミュレーション
%                                                4/21 by YANG
%---------------------------------------------------------------------

Tmax= 10;
samp=0.5; %サンプリング周期
T=samp;
n=Tmax/samp;
u=ones(1,n+1); %入力
%パラメータ
R=1;
L=1;

%数列
t  = zeros(1, n+1);
i  = zeros(1, n+1); %真の信号
i1 = zeros(1, n+1); %前進差分
i2 = zeros(1, n+1); %後退差分
i3 = zeros(1, n+1); %双一次変換

%真の信号
for k=2:n+1
    t(k)=(k-1)*samp;
    i(k) =(1 - exp((-t(k)*R)/L) )/R;
end

%前進差分
for k=2:n+1
    t(k)=(k-1)*samp;
    i1(k)=( samp*u(k-1) + (L-R*samp)*i1(k-1) )/L;
end

%後退差分
for k=2:n+1
    t(k)=(k-1)*samp;
    i2(k)=( T*u(k) + L*i(k-1) )/(R*samp+L);
end

%双一次変換
for k=2:n+1
    t(k)=(k-1)*samp;
    i3(k)=(T*u(k) + T*u(k-1) + (2*L-R*T)*i3(k-1) )/(2*L+R*samp);
end

%plot(t,i, t,i1, t,i2, t,i3);title('i(t)');legend('Real','Forward','Back','Bilinear'); xlabel('t');ylabel('i(t)');grid on
subplot(3,1,1); plot(t,i,t,i1); title('Forward');legend('real','Forward');  xlabel('t'); ylabel('i(t)');grid on
subplot(3,1,2); plot(t,i,t,i2); title('Back');legend('real','Back');      xlabel('t'); ylabel('i(t)');grid on
subplot(3,1,3); plot(t,i,t,i3); title('Bilinear');legend('real','Billinear');  xlabel('t'); ylabel('i(t)');grid on

```
