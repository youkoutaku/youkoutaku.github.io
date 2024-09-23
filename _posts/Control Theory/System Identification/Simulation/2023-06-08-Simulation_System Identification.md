---
title: Simulation_System Identification
date: 2023-06-08 15:33:00 +0900
categories: [Control Theory, System Identification]
tags: [System Identification, Matlab, Control, Least squares method, RLS, JP]
author: Youkoutaku
math: true
---

## 3.5.1 同定対象

2 次のシステムを考える．

$$
\begin{aligned}
x_{t} &=-a_1x_{t-1}-a_2x_{t-2}+b_1u_{t-1}+b_2u_{t-2}  \\
y_t &=x_t+v_t
\end{aligned}
$$

ただし，

$$
a_1=-1.5,\quad a_2=0.7,\quad b_1=1.0, \quad b_2=0.5
$$

ここで，入力信号を $[0,1]$ の範囲で**一様分布する乱数**とする．観測雑音は**ゼロ平均**のガウス分布乱数で，**入力信号と無相関**である．
雑音のレベルは一般に雑音信号比（Noise Signal Ratio, $NSR$ ）で評価される．ここで, **$NSR$ は雑音 $v_t$ の標準偏差と真の出力信号 $x_t$ の標準偏差との比である.**

## 3.5.2 逐次最小二乗法によるパラメータ推定

同じ入力信号に対して，出力の雑音信号比 $NSR$ をそれぞれ **0%，10%，20%** とし，入出力信号の観測値 $\{u_t , y_t\}(t = 1, 2, · · · , N)$ を用いて，パラメータを逐次最小二乗法で推定せよ。ただし，$N$ は**十分大きな整数（2000 ∼ 3000**）とする。時刻を横軸として，各推定パラメータの挙動を，パラメータの真値とあわせて図示せよ。観測雑音によるパラメータ 推定値への影響を考察せよ。

$$
y_t=-a_1y_{t-1}-a_2y_{t-2}+b_1u_{t-1}+b_2u_{t-2}+r_t
$$

$$
z_t^T=[-y_{t-1},-y_{t-2},u_{t-1},u_{t-2}]
$$

$$
\theta^T=[a_1,a_2,b_1,b_2]
$$

3.線形システムのパラメータ推定#3.3 **逐次最小二乗法**]]

> 行列計算のところに注意すべき

考察：
出力の雑音信号比(雑音のレベル)が大きいほど，逐次最小二乗法によるパラメータの推定値は，パラメータの真値とずれが生じる．

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',22)

%--------------------------------------------------------------------
%  同定ゼミ宿題 逐次最小二乗法によるパラメータ推定
%                                                5/24 by YANG 20T1126N
%---------------------------------------------------------------------
N=3000;
xmax = N;
ymax = 3;
rand('seed',1);

NSR = 0; %NSR=0, 0.1 0.2

%パラメーター
a1=-1.5;
a2=0.7;
b1=1.0;
b2=0.5;

t = zeros(1, N+1);
u = rand(1, N+1);
x = zeros(1, N+1); %真の出力
theta1_hat = zeros(4,N+1); %θ 0
theta2_hat = zeros(4,N+1); %θ 10
theta3_hat = zeros(4,N+1); %θ 20
E1 = zeros(1, N+1);
E2 = zeros(1, N+1);
E3 = zeros(1, N+1);
alpha = 1000;
rho   = 0.95*ones(1, N+1); %忘却係数
y = zeros(1, N+1); %観測値
z     = zeros(4,N+1); %z
z_t     = zeros(N+1,4); %z_t
phi   = zeros(4,N+1); %Φ
psi   = zeros(4,N+1); %Ψ
P = zeros(4,4,N+1);
L = zeros(4, N+1);
m = zeros(4,N+1);

%初期値設定
P(:,:,1) = alpha*eye(4);
t(1) = 0;
t(2) = 1;
x(1) = 0;
x(2) = -a1*x(1)+b1*u(1);
z(:,2) = [-y(1); 0; u(1); 0];
z_t(2,:) = z(:,2).';
rho(1) = 0.95;
rho(2) = (1-0.01)*rho(1)+0.01;
P(:,:,2) = ( P(:,:,1)-( P(:,:,1)*z(:,2)*z_t(2,:)*P(:,:,1) )/( rho(2)-z_t(2,:)*P(:,:,1)*z(:,2) ) )/rho(2);
L(:,2) = ( P(:,:,1)*z(:,2) )/( rho(2)+z_t(2,:)*P(:,:,1)*z(:,2) );
E1(1) = y(1);
E2(1) = y(1);
E3(1) = y(1);
theta1_hat(:,1) = L(:,1)*E1(1);
theta2_hat(:,1) = L(:,1)*E2(1);
theta3_hat(:,1) = L(:,1)*E3(1);
E1(2) = y(2)-z_t(2,:)*theta1_hat(:,1);
E2(2) = y(2)-z_t(2,:)*theta2_hat(:,1);
E3(2) = y(2)-z_t(2,:)*theta3_hat(:,1);
theta1_hat(:,2) = theta1_hat(:,1)+L(:,2)*E1(2);
theta2_hat(:,2) = theta2_hat(:,1)+L(:,2)*E2(2);
theta3_hat(:,2) = theta3_hat(:,1)+L(:,2)*E3(2);

for k=3:N+1
    t(k) = k-1;
    x(k) = -a1*x(k-1)-a2*x(k-2)+b1*u(k-1)+b2*u(k-2);
end

for NSR = 0:+0.1:0.2
    %ゼロ平均のガウス分布乱数=正規分布
    mu = 0 ;%平均値
    sigma = NSR*std(x) ;%標準偏差 NSR=sigma/std(x)
    v = sigma*randn(1,N+1) + mu;
    %観察雑音の平均値確認
    avg=mean(v);
    disp('v(t) Avg:')
    disp(avg)
    %入力信号と観察雑音の相関係数確認
    C=corrcoef(u,v);
    disp('v(t)-u(t) Corrcoef')
    disp(C)

    y = x + v;
    for k=3:N+1
        z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
    end

    if NSR == 0
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E1(k) = y(k)- (phi(:,k).')*theta1_hat(:,k-1);
            %thetaN
            theta1_hat(:,k) = theta1_hat(:,k-1) + L(:,k)*E1(k);
        end
    end
    if NSR == 0.1
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = 0.98;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E2(k) = y(k)- (phi(:,k).')*theta2_hat(:,k-1);
            %thetaN
            theta2_hat(:,k) = theta2_hat(:,k-1) + L(:,k)*E2(k);
        end
    end
    if NSR == 0.2
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E3(k) = y(k)- (phi(:,k).')*theta3_hat(:,k-1);
            %thetaN
            theta3_hat(:,k) = theta3_hat(:,k-1) + L(:,k)*E3(k);
        end
    end
end

%推定パラメータ
%a1
figure;
subplot(2,1,1);
plot( t,theta1_hat(1,:),'-',t,theta2_hat(1,:),'-', t,theta3_hat(1,:),'-');
title('-1.5');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a1')
hold on;
plot(t, -1.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%a2
subplot(2,1,2);
plot( t,theta1_hat(2,:),'-',t,theta2_hat(2,:),'-', t,theta3_hat(2,:),'-');
title('0.7');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a2')
hold on;
plot(t,  0.7*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%b1
figure;
subplot(2,1,1);
plot( t,theta1_hat(3,:),'-',t,theta2_hat(3,:),'-', t,theta3_hat(3,:),'-');
title('1.0');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b1')
hold on;
plot(t,  1.0*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%b2
subplot(2,1,2);
plot( t,theta1_hat(4,:),'-',t,theta2_hat(4,:),'-', t,theta3_hat(4,:),'-');
title('0.5');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b2')
hold on;
plot(t,  0.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
```

## 3.5.3 逐次最小二乗法による時変系のパラメータ推定

2 次の時変システム：

$$
\begin{aligned}
&x_t {=}{-a_{1,t}x_{t-1}-a_{2,t}x_{t-2}+b_{1,t}u_{t-1}+b_{2,t}u_{t-2}}  \\
&y_t =x_t+v_t
\end{aligned}
$$

ただし，係数：

$$
\begin{aligned}
a_{1,t}&= -1.0+0.5\sin(2\pi t/2000)  \\
a_{2,t}& =0.7  \\
b_{1,t}& =1.0  \\
b_{2,t}& =0.5+0.4\cos(2\pi t/2000)
\end{aligned}
$$

同じ入力信号に対して，出力の雑音信号比 $NSR$ をそれぞれ **0%，5%，10%** とした場合， 逐次最小二乗法で上記のシステムの時変パラメータを推定せよ。時刻を横軸として，各推定パラメータの挙動を，パラメータの真値とあわせて図示せよ。忘却係数及び観測雑音によるパラメータ推定値への影響を考察せよ。

考察：雑音のレベルが逐次最小二乗法による時変系のパラメータ推定への影響

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',22)

%--------------------------------------------------------------------
%  同定ゼミ宿題 逐次最小二乗法による時変系のパラメータ推定
%                                                5/25 by YANG 20T1126N
%---------------------------------------------------------------------
N=3000;
xmax = N;
ymax = 3;
rand('seed',1);
randn('seed',1);

NSR = 0;%NSR=0, 0.1 0.2

%パラメーター
a1 = zeros(1, N+1);
a2 = zeros(1, N+1);
b1 = zeros(1, N+1);
b2 = zeros(1, N+1);
for k=1:N+1
    %パラメータの時変
    a1(k) = -1.0 + 0.5*sin(2*pi*k/2000);
    a2(k) = 0.7;
    b1(k) = 1.0;
    b2(k) = 0.5 + 0.4*cos(2*pi*k/2000);
end

t = zeros(1, N+1);
u = rand(1, N+1);
x = zeros(1, N+1); %真の出力
theta1_hat = zeros(4,N+1); %θ 0
theta2_hat = zeros(4,N+1); %θ 10
theta3_hat = zeros(4,N+1); %θ 20
E1 = zeros(1, N+1);
E2 = zeros(1, N+1);
E3 = zeros(1, N+1);
alpha = 1000;
d = 2; %d>=n (n=2)
rho   = 0.95*ones(1, N+1); %忘却係数
y = zeros(1, N+1); %観測値
z     = zeros(4,N+1); %z
z_t     = zeros(N+1,4); %z_t
phi   = zeros(4,N+1); %Φ
psi   = zeros(4,N+1); %Ψ
P = zeros(4,4,N+1);
L = zeros(4, N+1);
m = zeros(4,N+1);

%初期値設定
P(:,:,1) = alpha*eye(4);
t(1) = 0;
t(2) = 1;
x(1) = 0;
x(2) = -a1(1) *x(1)+ b1(1)*u(1);
z(:,2)=[-y(1); 0; u(1); 0];
z_t(2,:) = z(:,2).';
rho(1) = 0.95;
rho(2) = (1-0.01)*rho(1)+0.01;
P(:,:,2) = ( P(:,:,1)-( P(:,:,1)*z(:,2)*z_t(2,:)*P(:,:,1) )/( rho(2)-z_t(2,:)*P(:,:,1)*z(:,2) ) )/rho(2);
L(:,2) = ( P(:,:,1)*z(:,2) )/( rho(2)+z_t(2,:)*P(:,:,1)*z(:,2) );
E1(1)=y(1);
E2(1)=y(1);
E3(1)=y(1);
theta1_hat(:,1) = L(:,1)*E1(1);
theta2_hat(:,1) = L(:,1)*E2(1);
theta3_hat(:,1) = L(:,1)*E3(1);
E1(2) = y(2)-z_t(2,:)*theta1_hat(:,1);
E2(2) = y(2)-z_t(2,:)*theta2_hat(:,1);
E3(2) = y(2)-z_t(2,:)*theta3_hat(:,1);
theta1_hat(:,2) = theta1_hat(:,1)+L(:,2)*E1(2);
theta2_hat(:,2) = theta2_hat(:,1)+L(:,2)*E2(2);
theta3_hat(:,2) = theta3_hat(:,1)+L(:,2)*E3(2);

for k=3:N+1
    t(k) = k-1;
    x(k) = -a1(k-1)*x(k-1)-a2(k-2)*x(k-2)+b1(k-1)*u(k-1)+b2(k-2)*u(k-2);
end

for NSR = 0:+0.1:0.2
    %ゼロ平均のガウス分布乱数=正規分布
    mu = 0 ;%平均値
    sigma = NSR*std(x) ;%標準偏差 NSR=sigma/std(x)
    v = sigma*randn(1,N+1) + mu;
    %観察雑音の平均値確認
    avg=mean(v);
    disp('v(t) Avg:')
    disp(avg)
    %入力信号と観察雑音の相関係数確認
    C=corrcoef(u,v);
    disp('v(t)-u(t) Corrcoef')
    disp(C)

    y = x + v;

    for k=3:N+1
        z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
    end

    if NSR == 0
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = 0.98;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E1(k) = y(k)- (phi(:,k).')*theta1_hat(:,k-1);
            %thetaN
            theta1_hat(:,k) = theta1_hat(:,k-1) + L(:,k)*E1(k);
        end
    end
    if NSR == 0.1
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = 0.98;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E2(k) = y(k)- (phi(:,k).')*theta2_hat(:,k-1);
            %thetaN
            theta2_hat(:,k) = theta2_hat(:,k-1) + L(:,k)*E2(k);
        end
    end
    if NSR == 0.2
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = 0.98;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E3(k) = y(k)- (phi(:,k).')*theta3_hat(:,k-1);
            %thetaN
            theta3_hat(:,k) = theta3_hat(:,k-1) + L(:,k)*E3(k);
        end
    end
end

%推定パラメータ
%a1
figure;
subplot(2,1,1);
plot( t,theta1_hat(1,:),'-',t,theta2_hat(1,:),'-', t,theta3_hat(1,:),'-');
title('A1');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a1')
hold on;
plot(t, a1, 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%a2
subplot(2,1,2);
plot( t,theta1_hat(2,:),'-',t,theta2_hat(2,:),'-', t,theta3_hat(2,:),'-');
title('A2');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a2')
hold on;
plot(t,  a2, 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%b1
figure;
subplot(2,1,1);
plot( t,theta1_hat(3,:),'-',t,theta2_hat(3,:),'-', t,theta3_hat(3,:),'-');
title('B1');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b1')
hold on;
plot(t,  b1, 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%b2
subplot(2,1,2);
plot( t,theta1_hat(4,:),'-',t,theta2_hat(4,:),'-', t,theta3_hat(4,:),'-');
title('B2');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b2')
hold on;
plot(t,  b2, 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
```

## 3.5.4 逐次補助変数法によるパラメータ推定

### 遅延された入力

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',22)

%--------------------------------------------------------------------
%  同定ゼミ宿題 逐次補助変数法によるパラメータ推定 遅延された入力
%                                                6/05 by YANG 20T1126N
%---------------------------------------------------------------------
N=3000;
xmax = N;
ymax = 5;
rand('seed',1);
randn('seed',1);

NSR = 0;%NSR=0, 0.1 0.2

%パラメーター
a1=-1.5;
a2=0.7;
b1=1.0;
b2=0.5;

t = zeros(1, N+1);
u = rand(1, N+1);
x = zeros(1, N+1); %真の出力
theta1_hat = zeros(4,N+1); %θ 0
theta2_hat = zeros(4,N+1); %θ 10
theta3_hat = zeros(4,N+1); %θ 20
E1 = zeros(1, N+1);
E2 = zeros(1, N+1);
E3 = zeros(1, N+1);
alpha = 1000;
rho   = 0.95*ones(1, N+1); %忘却係数
y = zeros(1, N+1); %観測値
z     = zeros(4,N+1); %z
z_t     = zeros(N+1,4); %z_t
phi   = zeros(4,N+1); %Φ
psi   = zeros(4,N+1); %Ψ
P = zeros(4,4,N+1);
L = zeros(4, N+1);
m = zeros(4,N+1);
d = 2; %d>=n (n=2)

%初期値設定
P(:,:,1) = alpha*eye(4);
t(1) = 0;
t(2) = 1;
x(1) = 0;
x(2) = -a1(1) *x(1)+ b1(1)*u(1);
z(:,2)=[-y(1); 0; u(1); 0];
z_t(2,:) = z(:,2).';
rho(1) = 0.95;
rho(2) = (1-0.01)*rho(1)+0.01;
P(:,:,2) = ( P(:,:,1)-( P(:,:,1)*z(:,2)*z_t(2,:)*P(:,:,1) )/( rho(2)-z_t(2,:)*P(:,:,1)*z(:,2) ) )/rho(2);
L(:,2) = ( P(:,:,1)*z(:,2) )/( rho(2)+z_t(2,:)*P(:,:,1)*z(:,2) );
E1(1)=y(1);
E2(1)=y(1);
E3(1)=y(1);
theta1_hat(:,1) = L(:,1)*E1(1);
theta2_hat(:,1) = L(:,1)*E2(1);
theta3_hat(:,1) = L(:,1)*E3(1);
E1(2) = y(2)-z_t(2,:)*theta1_hat(:,1);
E2(2) = y(2)-z_t(2,:)*theta2_hat(:,1);
E3(2) = y(2)-z_t(2,:)*theta3_hat(:,1);
theta1_hat(:,2) = theta1_hat(:,1)+L(:,2)*E1(2);
theta2_hat(:,2) = theta2_hat(:,1)+L(:,2)*E2(2);
theta3_hat(:,2) = theta3_hat(:,1)+L(:,2)*E3(2);

for k=3:N+1
    t(k) = k-1;
    x(k) = -a1*x(k-1)-a2*x(k-2)+b1*u(k-1)+b2*u(k-2);
end

for NSR = 0:+0.1:0.2
    %ゼロ平均のガウス分布乱数=正規分布
    mu = 0 ;%平均値
    sigma = NSR*std(x) ;%標準偏差 NSR=sigma/std(x)
    v = sigma*randn(1,N+1) + mu;
    %観察雑音の平均値確認
    avg=mean(v);
    disp('v(t) Avg:')
    disp(avg)
    %入力信号と観察雑音の相関係数確認
    C=corrcoef(u,v);
    disp('v(t)-u(t) Corrcoef')
    disp(C)

    y = x + v;

    %遅延された入力の初期設定
    m(:,2) = [0, 0, u(1) ,0 ].';
    m(:,3) = [0, 0, u(2) ,u(1) ].';
    m(:,4) = [u(1), 0, u(3) ,u(2) ].';

    for k=3+d:N+1
        m(:,k) = [u(k-1-d), u(k-2-d), u(k-1), u(k-2)].'; %m-遅延された入力
    end

    if NSR == 0
        for k=3:N+1
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = m(:,k);
            %rho
            rho(k) = 0.98;%(1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E1(k) = y(k)- (phi(:,k).')*theta1_hat(:,k-1);
            %thetaN
            theta1_hat(:,k) = theta1_hat(:,k-1) + L(:,k)*E1(k);
        end
    end
    if NSR == 0.1
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = m(:,k);
            %rho
            rho(k) =  0.98;%(1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E2(k) = y(k)- (phi(:,k).')*theta2_hat(:,k-1);
            %thetaN
            theta2_hat(:,k) = theta2_hat(:,k-1) + L(:,k)*E2(k);
        end
    end
    if NSR == 0.2
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = m(:,k);
            %rho
            rho(k) = 0.98;%(1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E3(k) = y(k)- (phi(:,k).')*theta3_hat(:,k-1);
            %thetaN
            theta3_hat(:,k) = theta3_hat(:,k-1) + L(:,k)*E3(k);
        end
    end
end

%推定パラメータ
%a1
figure;
subplot(2,1,1);
plot( t,theta1_hat(1,:),'-',t,theta2_hat(1,:),'-', t,theta3_hat(1,:),'-');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a1')
hold on;
plot(t, -1.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%a2
subplot(2,1,2);
plot( t,theta1_hat(2,:),'-',t,theta2_hat(2,:),'-', t,theta3_hat(2,:),'-');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a2')
hold on;
plot(t,  0.7*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%b1
figure;
subplot(2,1,1);
plot( t,theta1_hat(3,:),'-',t,theta2_hat(3,:),'-', t,theta3_hat(3,:),'-');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b1')
hold on;
plot(t,  1.0*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%b2
subplot(2,1,2);
plot( t,theta1_hat(4,:),'-',t,theta2_hat(4,:),'-', t,theta3_hat(4,:),'-');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b2')
hold on;
plot(t,  0.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
```

### 遅延された出力

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',22)

%--------------------------------------------------------------------
%  同定ゼミ宿題 逐次補助変数法によるパラメータ推定 遅延された出力
%                                                6/05 by YANG 20T1126N
%---------------------------------------------------------------------
N=3000;
xmax = N;
ymax = 10;
rand('seed',1);
randn('seed',1);

NSR = 0;%NSR=0, 0.1 0.2

%パラメーター
a1=-1.5;
a2=0.7;
b1=1.0;
b2=0.5;

t = zeros(1, N+1);
u = rand(1, N+1);
x = zeros(1, N+1); %真の出力
theta1_hat = zeros(4,N+1); %θ 0
theta2_hat = zeros(4,N+1); %θ 10
theta3_hat = zeros(4,N+1); %θ 20
E1 = zeros(1, N+1);
E2 = zeros(1, N+1);
E3 = zeros(1, N+1);
alpha = 1000;
rho   = 0.95*ones(1, N+1); %忘却係数
y = zeros(1, N+1); %観測値
z     = zeros(4,N+1); %z
z_t     = zeros(N+1,4); %z_t
phi_t   = zeros(N+1,4); %Φ
psi   = zeros(4,N+1); %Ψ
P = zeros(4,4,N+1);
L = zeros(4, N+1);
m = zeros(4,N+1);
d = 2; %d>=n (n=2)

%初期値設定
P(:,:,1) = alpha*eye(4);
t(1) = 0;
t(2) = 1;
x(1) = 0;
x(2) = -a1(1) *x(1)+ b1(1)*u(1);
z(:,2)=[-y(1); 0; u(1); 0];
z_t(2,:) = z(:,2).';
rho(1) = 0.95;
rho(2) = (1-0.01)*rho(1)+0.01;
P(:,:,2) = ( P(:,:,1)-( P(:,:,1)*z(:,2)*z_t(2,:)*P(:,:,1) )/( rho(2)-z_t(2,:)*P(:,:,1)*z(:,2) ) )/rho(2);
L(:,2) = ( P(:,:,1)*z(:,2) )/( rho(2)+z_t(2,:)*P(:,:,1)*z(:,2) );
E1(1)=y(1);
E2(1)=y(1);
E3(1)=y(1);
theta1_hat(:,1) = L(:,1)*E1(1);
theta2_hat(:,1) = L(:,1)*E2(1);
theta3_hat(:,1) = L(:,1)*E3(1);
E1(2) = y(2)-z_t(2,:)*theta1_hat(:,1);
E2(2) = y(2)-z_t(2,:)*theta2_hat(:,1);
E3(2) = y(2)-z_t(2,:)*theta3_hat(:,1);
theta1_hat(:,2) = theta1_hat(:,1)+L(:,2)*E1(2);
theta2_hat(:,2) = theta2_hat(:,1)+L(:,2)*E2(2);
theta3_hat(:,2) = theta3_hat(:,1)+L(:,2)*E3(2);

for k=3:N+1
    t(k) = k-1;
    x(k) = -a1*x(k-1)-a2*x(k-2)+b1*u(k-1)+b2*u(k-2);
end

for NSR = 0:+0.1:0.2
    %ゼロ平均のガウス分布乱数=正規分布
    mu = 0 ;%平均値
    sigma = NSR*std(x) ;%標準偏差 NSR=sigma/std(x)
    v = sigma*randn(1,N+1) + mu;
    %観察雑音の平均値確認
    avg=mean(v);
    disp('v(t) Avg:')
    disp(avg)
    %入力信号と観察雑音の相関係数確認
    C=corrcoef(u,v);
    disp('v(t)-u(t) Corrcoef')
    disp(C)

    y = x + v;

    if NSR == 0
        %遅延された出力の初期設定
        m(:,2) = [0, 0, u(1) ,0 ].';
        m(:,3) = [0, 0, u(2) ,u(1) ].';
        m(:,4) = [y(1), 0, u(3) ,u(2) ].';
        for k=3+d:N+1
            %m-遅延された出力
            m(:,k) = [ y(k-1-d), y(k-2-d), u(k-1), u(k-2)].';
        end
        for k=3:N+1
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            %phi (^T)
            phi_t(k,:) = z_t(k,:);
            %psi
            psi(:,k) = m(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * phi_t(k,:) *P(:,:,k-1 ) / (rho(k) + phi_t(k,:) *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + phi_t(k,:) *P(:,:,k-1)*psi(:,k));
            %e
            E1(k) = y(k)- phi_t(k,:)*theta1_hat(:,k-1);
            %thetaN
            theta1_hat(:,k) = theta1_hat(:,k-1) + L(:,k)*E1(k);
        end
    end
    if NSR == 0.1
        %遅延された出力の初期設定
        m(:,2) = [0, 0, u(1) ,0 ].';
        m(:,3) = [0, 0, u(2) ,u(1) ].';
        m(:,4) = [y(1), 0, u(3) ,u(2) ].';
        for k=3+d:N+1
            %m-遅延された出力
            m(:,k) = [ y(k-1-d), y(k-2-d), u(k-1), u(k-2)].';
        end
        for k=3:N+1
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            %phi (^T)
            phi_t(k,:) = z_t(k,:);
            %psi
            psi(:,k) = m(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * phi_t(k,:) *P(:,:,k-1 ) / (rho(k) + phi_t(k,:) *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + phi_t(k,:) *P(:,:,k-1)*psi(:,k));
            %e
            E2(k) = y(k)- phi_t(k,:)*theta2_hat(:,k-1);
            %thetaN
            theta2_hat(:,k) = theta2_hat(:,k-1) + L(:,k)*E2(k);
        end
    end
    if NSR == 0.2
        %遅延された出力の初期設定
        m(:,2) = [0, 0, u(1) ,0 ].';
        m(:,3) = [0, 0, u(2) ,u(1) ].';
        m(:,4) = [y(1), 0, u(3) ,u(2) ].';
        for k=3+d:N+1
            %m-遅延された出力
            m(:,k) = [ y(k-1-d), y(k-2-d), u(k-1), u(k-2)].';
        end
        for k=3:N+1
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            %phi (^T)
            phi_t(k,:) = z_t(k,:);
            %psi
            psi(:,k) = m(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * phi_t(k,:) *P(:,:,k-1 ) / (rho(k) + phi_t(k,:) *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + phi_t(k,:) *P(:,:,k-1)*psi(:,k));
            %e
            E3(k) = y(k)- phi_t(k,:)*theta3_hat(:,k-1);
            %thetaN
            theta3_hat(:,k) = theta3_hat(:,k-1) + L(:,k)*E3(k);
        end
    end
end

%推定パラメータ
%a1
figure;
subplot(2,1,1);
plot( t,theta1_hat(1,:),'-',t,theta2_hat(1,:),'-', t,theta3_hat(1,:),'-');
title('theta');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a1')
hold on;
plot(t, -1.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%a2
subplot(2,1,2);
plot( t,theta1_hat(2,:),'-',t,theta2_hat(2,:),'-', t,theta3_hat(2,:),'-');
title('theta');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('a2')
hold on;
plot(t,  0.7*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%b1
figure;
subplot(2,1,1);
plot( t,theta1_hat(3,:),'-',t,theta2_hat(3,:),'-', t,theta3_hat(3,:),'-');
title('theta');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b1')
hold on;
plot(t,  1.0*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
%b2
subplot(2,1,2);
plot( t,theta1_hat(4,:),'-',t,theta2_hat(4,:),'-', t,theta3_hat(4,:),'-');
title('theta');
legend('NSR=0','NSR=0.1','NSR=0.2');
xlabel('Time[sec]');
ylabel('b2')
hold on;
plot(t,  0.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
```

### 定された出力

```matlab
clear
close all
set(0,'DefaultAxesFontName','Times New Roman')
set(0,'DefaultAxesFontSize',22)

%--------------------------------------------------------------------
%  同定ゼミ宿題 逐次補助変数法によるパラメータ推定 推定された出力
%                                                6/08 by YANG 20T1126N
%---------------------------------------------------------------------
N=3000;
xmax = 1000;
ymax = 3;
rand('seed',1);
randn('seed',1);

NSR = 0.1;%NSR=0, 0.1 0.2

%パラメーター
a1=-1.5;
a2=0.7;
b1=1.0;
b2=0.5;

t = zeros(1, N+1);
u = rand(1, N+1);
x = zeros(1, N+1); %真の出力
theta1_hat = zeros(4,N+1); %θ 0
theta2_hat = zeros(4,N+1); %θ 10
theta3_hat = zeros(4,N+1); %θ 20
E1 = zeros(1, N+1);%逐次最小二乗法
E2 = zeros(1, N+1);
E3 = zeros(1, N+1);

theta4_hat = zeros(4,N+1); %θ 0
theta5_hat = zeros(4,N+1); %θ 10
theta6_hat = zeros(4,N+1); %θ 20
E4 = zeros(1, N+1);%補助変数法
E5 = zeros(1, N+1);
E6 = zeros(1, N+1);

alpha = 1000;
rho   = 0.95*ones(1, N+1); %忘却係数
y = zeros(1, N+1); %観測値
z     = zeros(4,N+1); %z
z_t     = zeros(N+1,4); %z_t
phi   = zeros(4,N+1); %Φ
psi   = zeros(4,N+1); %Ψ
P = zeros(4,4,N+1);
L = zeros(4, N+1);

%補助変数法
m = zeros(4,N+1);
hat_x = zeros(1, N+1);
bar_a1 = zeros(1, N+1);
bar_a2 = zeros(1, N+1);

%初期値設定
P(:,:,1) = alpha*eye(4);
t(1) = 0;
t(2) = 1;
x(1) = 0;
x(2) = -a1*x(1)+b1*u(1);
z(:,2) = [-y(1); 0; u(1); 0];
z_t(2,:) = z(:,2).';
rho(1) = 0.95;
rho(2) = (1-0.01)*rho(1)+0.01;
P(:,:,2) = ( P(:,:,1)-( P(:,:,1)*z(:,2)*z_t(2,:)*P(:,:,1) )/( rho(2)-z_t(2,:)*P(:,:,1)*z(:,2) ) )/rho(2);
L(:,2) = ( P(:,:,1)*z(:,2) )/( rho(2)+z_t(2,:)*P(:,:,1)*z(:,2) );
E1(1) = y(1);
E2(1) = y(1);
E3(1) = y(1);
E4(1) = y(1);
E5(1) = y(1);
E6(1) = y(1);
theta1_hat(:,1) = L(:,1)*E1(1);
theta2_hat(:,1) = L(:,1)*E2(1);
theta3_hat(:,1) = L(:,1)*E3(1);
theta4_hat(:,1) = L(:,1)*E4(1);
theta5_hat(:,1) = L(:,1)*E5(1);
theta6_hat(:,1) = L(:,1)*E6(1);
E1(2) = y(2)-z_t(2,:)*theta1_hat(:,1);
E2(2) = y(2)-z_t(2,:)*theta2_hat(:,1);
E3(2) = y(2)-z_t(2,:)*theta3_hat(:,1);
E4(2) = y(2)-z_t(2,:)*theta4_hat(:,1);
E5(2) = y(2)-z_t(2,:)*theta5_hat(:,1);
E6(2) = y(2)-z_t(2,:)*theta6_hat(:,1);
theta1_hat(:,2) = theta1_hat(:,1)+L(:,2)*E1(2);
theta2_hat(:,2) = theta2_hat(:,1)+L(:,2)*E2(2);
theta3_hat(:,2) = theta3_hat(:,1)+L(:,2)*E3(2);
theta4_hat(:,2) = theta4_hat(:,1)+L(:,2)*E4(2);
theta5_hat(:,2) = theta5_hat(:,1)+L(:,2)*E5(2);
theta6_hat(:,2) = theta6_hat(:,1)+L(:,2)*E6(2);

for k=3:N+1
    t(k) = k-1;
    x(k) = -a1*x(k-1)-a2*x(k-2)+b1*u(k-1)+b2*u(k-2);
end

%逐次最小二乗法
for NSR = 0:+0.1:0.2
    %ゼロ平均のガウス分布乱数=正規分布
    mu = 0 ;%平均値
    sigma = NSR*std(x) ;%標準偏差 NSR=sigma/std(x)
    v = sigma*randn(1,N+1) + mu;
    %観察雑音の平均値確認
    avg=mean(v);
    disp('v(t) Avg:')
    disp(avg)
    %入力信号と観察雑音の相関係数確認
    C=corrcoef(u,v);
    disp('v(t)-u(t) Corrcoef')
    disp(C)

    y = x + v;
    for k=3:N+1
        z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
        z_t(k,:) = z(:,k).';
    end
    if NSR == 0
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E1(k) = y(k)- (phi(:,k).')*theta1_hat(:,k-1);
            %thetaN
            theta1_hat(:,k) = theta1_hat(:,k-1) + L(:,k)*E1(k);
        end
    end
    if NSR == 0.1
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E2(k) = y(k)- (phi(:,k).')*theta2_hat(:,k-1);
            %thetaN
            theta2_hat(:,k) = theta2_hat(:,k-1) + L(:,k)*E2(k);
        end
    end
    if NSR == 0.2
        for k=3:N+1
            %phi (^T)
            phi(:,k) = z(:,k);
            %psi
            psi(:,k) = z(:,k);
            %rho
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %P
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E3(k) = y(k)- (phi(:,k).')*theta3_hat(:,k-1);
            %thetaN
            theta3_hat(:,k) = theta3_hat(:,k-1) + L(:,k)*E3(k);
        end
    end
end


%50まで逐次最小二乗法
for NSR = 0:+0.1:0.2
    v0 = NSR*std(x);
    v = v0*randn(1,N+1);
    y = x + v;
    for k=3:50
        z(:,k) = [-y(k-1); -y(k-2); u(k-1); u(k-2)];
        z_t(k,:) = z(:,k).';
    end
    if NSR == 0
        for k = 3:50
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %Ps
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E4(k) = y(k)- (phi(:,k).')*theta4_hat(:,k-1);
            %thetaN
            theta4_hat(:,k) = theta4_hat(:,k-1) + L(:,k)*E4(k);
            hat_x(k+1) = -theta4_hat(1,k)*hat_x(k) - theta4_hat(2,k)*hat_x(k-1) + theta4_hat(3,k)*u(k) + theta4_hat(4,k)*u(k-1);
        end
    end
    if NSR == 0.1
        for k = 3:50
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %Ps
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E5(k) = y(k)- (phi(:,k).')*theta5_hat(:,k-1);
            %thetaN
            theta5_hat(:,k) = theta5_hat(:,k-1) + L(:,k)*E5(k);
            hat_x(k+1) = -theta5_hat(1,k)*hat_x(k) - theta5_hat(2,k)*hat_x(k-1) + theta5_hat(3,k)*u(k) + theta5_hat(4,k)*u(k-1);
        end
    end
    if NSR == 0.2
        for k = 3:50
            z(:,k) = [-y(k-1),-y(k-2),u(k-1),u(k-2)].';
            z_t(k,:) = z(:,k).';
            rho(k) = (1-0.01)*rho(k-1)+0.01;
            %Ps
            P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
            %L
            L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
            %e
            E6(k) = y(k)- (phi(:,k).')*theta6_hat(:,k-1);
            %thetaN
            theta6_hat(:,k) = theta6_hat(:,k-1) + L(:,k)*E6(k);
            hat_x(k+1) = -theta6_hat(1,k)*hat_x(k)-theta6_hat(2,k)*hat_x(k-1)+theta6_hat(3,k)*u(k)+theta6_hat(4,k)*u(k-1);
        end
    end
end

for NSR = 0:+0.1:0.2
    v0 = NSR*std(x);
    v = v0*randn(1,N+1);
    y = x + v;
    for k=51:N+1
        z(:,k) = [-y(k-1); -y(k-2); u(k-1); u(k-2)];
        z_t(k,:) = z(:,k).';
    end
    if NSR == 0
        for k=51:N+1
            p(:,:,k) = [1 theta4_hat(1,k-1) theta4_hat(2,k-1)];
            s(:,k) = roots(p(:,:,k));
            if abs(s(1,k)) < 1 &&  abs(s(2,k)) < 1
                bar_a1(k-1) = theta4_hat(1,k-1);
                bar_a2(k-1) = theta4_hat(2,k-1);
            else
                bar_a1(k-1) = bar_a1(k-2) + 0.5*( theta4_hat(1,k-1) - bar_a1(k-2) );
                bar_a2(k-1) = bar_a2(k-2) + 0.5*( theta4_hat(1,k-1) - bar_a2(k-2) );
            end
            while 1
                p(:,:,k) = [1 bar_a1(1,k-1) bar_a2(1,k-1)];
                s(:,k) = roots(p(:,:,k));
                if abs(s(1,k)) < 1 &&  abs(s(2,k)) < 1
                    break;
                else
                    bar_a1(k-1) = bar_a1(k-2) + 0.5*( bar_a1(k-1) - bar_a1(k-2) );
                    bar_a2(k-1) = bar_a2(k-2) + 0.5*( bar_a2(k-1) - bar_a2(k-2) );
                end
            end
        hat_x(k) = -bar_a1(k-1)*hat_x(k-1) - bar_a2(k-1)*hat_x(k-2) + theta4_hat(3,k-1)*u(k-1) + theta4_hat(4,k-1)*u(k-2) ;
        %m-推定された出力
        m(:,k) = [-hat_x(k-1), -hat_x(k-2), u(k-1), u(k-2)].';
        %phi (^T)
        phi(:,k) = z(:,k);
        %psi
        psi(:,k) = m(:,k);
        %rho
        rho(k) = (1-0.01)*rho(k-1)+0.01;
        %P
        P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
        %L
        L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
        %e
        E4(k) = y(k)- (phi(:,k).')*theta4_hat(:,k-1);
        %thetaN
        theta4_hat(:,k) = theta4_hat(:,k-1) + L(:,k)*E4(k);
        end
    end
    if NSR == 0.1
        for k=51:N+1
            p(:,:,k) = [1 theta5_hat(1,k-1) theta5_hat(2,k-1)];
            s(:,k) = roots(p(:,:,k));
            if abs(s(1,k)) < 1 &&  abs(s(2,k)) < 1
                bar_a1(k-1) = theta5_hat(1,k-1);
                bar_a2(k-1) = theta5_hat(2,k-1);
            else
                bar_a1(k-1) = bar_a1(k-2) + 0.5*( theta5_hat(1,k-1) - bar_a1(k-2) );
                bar_a2(k-1) = bar_a2(k-2) + 0.5*( theta5_hat(1,k-1) - bar_a2(k-2) );
            end
            while 1
                p(:,:,k) = [1 bar_a1(1,k-1) bar_a2(1,k-1)];
                s(:,k) = roots(p(:,:,k));
                if abs(s(1,k)) < 1 &&  abs(s(2,k)) < 1
                    break;
                else
                    bar_a1(k-1) = bar_a1(k-2) + 0.5*( bar_a1(k-1) - bar_a1(k-2) );
                    bar_a2(k-1) = bar_a2(k-2) + 0.5*( bar_a2(k-1) - bar_a2(k-2) );
                end
            end
        hat_x(k) = -bar_a1(k-1)*hat_x(k-1) - bar_a2(k-1)*hat_x(k-2) + theta5_hat(3,k-1)*u(k-1) + theta5_hat(4,k-1)*u(k-2) ;
        %m-推定された出力
        m(:,k) = [-hat_x(k-1), -hat_x(k-2), u(k-1), u(k-2)].';
        %phi (^T)
        phi(:,k) = z(:,k);
        %psi
        psi(:,k) = m(:,k);
        %rho
        rho(k) = (1-0.01)*rho(k-1)+0.01;
        %P
        P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
        %L
        L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
        %e
        E5(k) = y(k)- (phi(:,k).')*theta5_hat(:,k-1);
        %thetaN
        theta5_hat(:,k) = theta5_hat(:,k-1) + L(:,k)*E5(k);
        end
    end
    if NSR == 0.2
        for k=51:N+1
            p(:,:,k) = [1 theta6_hat(1,k-1) theta6_hat(2,k-1)];
            s(:,k) = roots(p(:,:,k));
            if abs(s(1,k)) < 1 &&  abs(s(2,k)) < 1
                bar_a1(k-1) = theta6_hat(1,k-1);
                bar_a2(k-1) = theta6_hat(2,k-1);
            else
                bar_a1(k-1) = bar_a1(k-2) + 0.5*( theta6_hat(1,k-1) - bar_a1(k-2) );
                bar_a2(k-1) = bar_a2(k-2) + 0.5*( theta6_hat(1,k-1) - bar_a2(k-2) );
            end
            while 1
                p(:,:,k) = [1 bar_a1(1,k-1) bar_a2(1,k-1)];
                s(:,k) = roots(p(:,:,k));
                if abs(s(1,k)) < 1 &&  abs(s(2,k)) < 1
                    break;
                else
                    bar_a1(k-1) = bar_a1(k-2) + 0.5*( bar_a1(k-1) - bar_a1(k-2) );
                    bar_a2(k-1) = bar_a2(k-2) + 0.5*( bar_a2(k-1) - bar_a2(k-2) );
                end
            end
        hat_x(k) = -bar_a1(k-1)*hat_x(k-1) - bar_a2(k-1)*hat_x(k-2) + theta6_hat(3,k-1)*u(k-1) + theta6_hat(4,k-1)*u(k-2) ;
        %m-推定された出力
        m(:,k) = [-hat_x(k-1), -hat_x(k-2), u(k-1), u(k-2)].';
        %phi (^T)
        phi(:,k) = z(:,k);
        %psi
        psi(:,k) = m(:,k);
        %rho
        rho(k) = (1-0.01)*rho(k-1)+0.01;
        %P
        P(:,:,k) = ( P(:,:,k-1)) - ( P(:,:,k-1)* psi(:,k) * (phi(:,k).') *P(:,:,k-1 ) / (rho(k) + (phi(:,k).') *P(:,:,k-1)* psi(:,k) ) ) / rho(k);
        %L
        L(:,k) = ( P(:,:,k-1) * psi(:,k) ) / (rho(k) + (phi(:,k).')*P(:,:,k-1)*psi(:,k));
        %e
        E6(k) = y(k)- (phi(:,k).')*theta6_hat(:,k-1);
        %thetaN
        theta6_hat(:,k) = theta6_hat(:,k-1) + L(:,k)*E6(k);
        end
    end
end

%推定パラメータ
%a1
figure;
subplot(3,1,1);
plot( t,theta1_hat(1,:),'-',t,theta4_hat(1,:),'-');
title('NSR=0');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$a_1$', 'Interpreter', 'latex');
hold on;
plot(t, -1.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,2);
plot( t,theta2_hat(1,:),'-',t,theta5_hat(1,:),'-');
title('NSR=0.1');
legend('Least square','Bootstrap');
xlabel('Time[');
ylabel('$a_1$', 'Interpreter', 'latex');
hold on;
plot(t, -1.5*ones(N+1), 'y--');
hold off;
xlim([-1 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,3);
plot( t,theta3_hat(1,:),'-',t,theta6_hat(1,:),'-');
title('NSR=0.2');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$a_1$', 'Interpreter', 'latex');
hold on;
plot(t, -1.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%a2
figure;
subplot(3,1,1);
plot( t,theta1_hat(2,:),'-',t,theta4_hat(2,:),'-');
title('NSR=0');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$a_2$', 'Interpreter', 'latex');
hold on;
plot(t,  0.7*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,2);
plot( t,theta2_hat(2,:),'-',t,theta5_hat(2,:),'-');
title('NSR=0.1');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$a_2$', 'Interpreter', 'latex');
hold on;
plot(t,  0.7*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,3);
plot( t,theta3_hat(2,:),'-',t,theta6_hat(2,:),'-');
title('NSR=0.2');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$a_2$', 'Interpreter', 'latex');
hold on;
plot(t,  0.7*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%b1
figure;
subplot(3,1,1);
plot( t,theta1_hat(3,:),'-',t,theta4_hat(3,:),'-');
title('NSR=0');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$b_1$', 'Interpreter', 'latex');
hold on;
plot(t,  1.0*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,2);
plot( t,theta2_hat(3,:),'-',t,theta5_hat(3,:),'-');
title('NSR=0.1');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$b_1$', 'Interpreter', 'latex');
hold on;
plot(t,  1.0*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,3);
plot( t,theta3_hat(3,:),'-',t,theta6_hat(3,:),'-');
title('NSR=0.2');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$b_1$', 'Interpreter', 'latex');
hold on;
plot(t,  1.0*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on

%b2
figure;
subplot(3,1,1);
plot( t,theta1_hat(4,:),'-',t,theta4_hat(4,:),'-');
title('NSR=0');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$b_2$', 'Interpreter', 'latex');
hold on;
plot(t,  0.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,2);
plot( t,theta2_hat(4,:),'-',t,theta5_hat(4,:),'-');
title('NSR=0.1');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$b_2$', 'Interpreter', 'latex');
hold on;
plot(t,  0.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
subplot(3,1,3);
plot( t,theta3_hat(4,:),'-',t,theta6_hat(4,:),'-');
title('NSR=0.2');
legend('Least square','Bootstrap');
xlabel('Time');
ylabel('$b_2$', 'Interpreter', 'latex');
hold on;
plot(t,  0.5*ones(N+1), 'y--');
hold off;
xlim([-10 xmax]);
ylim([-ymax ymax]);
grid on
```
