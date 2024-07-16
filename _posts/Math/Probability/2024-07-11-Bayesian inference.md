---
title: ベイズ推定(Bayesian inference)
date: 2024-07-11 14:03 +0900
categories: [Probability Theory, Linear regression]
tags: [Probability, Linear regression, Bayesian inference, JP]
author: Youkoutaku
math: true
img_path: /src/math/msB2/
image:
  path: output_9_0.png
  alt: 
---

Homepage: https://youkoutaku.github.io/

## 1.問題
観測データが ($x_1$, $y_1$), … , ($x_M$, $y_M$) が得られたとき，この観測結果を直線 $y=\theta_0+\theta_1 x$ に回帰したい．回帰直線のパラメータ $\theta_0$ と $\theta_1$ を逐次ベイズ学習により推定し，その結果を考
察しなさい．

手順１：観測データの生成
1.  $x_1$ から $x_M$ を一様乱数により生成する．
2.  $y_1$ から $y_M$ を の計算式に従って生成する．ここで， $𝜃_0$ と $𝜃_1$ は適当な値を与える．また $𝜀_j$ は平均が 0 で分散が $𝜎^2$ の正規乱数とする．

手順２：逐次ベイズ学習による  $\theta_0$ と $\theta_1$ の推定
1. 事前分布のパラメタを設定する
2.  ($x_j$, $y_j$)が得られる毎に更新式にしたがい，パラメタ更新を行う．𝛼 と 𝛽 は各自で決める．ただし，ノイズの標準偏差が𝜎のとき，

$$
𝛽=(\frac1\sigma)^2$$

で与えられることに注意する．

考察のポイント
- 𝛼 と 𝛽 の影響
- 雑音レベルの影響（S/N）

## 2.ベイズ線形回帰

### 2.1 観測モデル

$$
y=\theta_0+\theta_1\phi_1(x)+\dots+\theta_N\phi_N(x) + ϵ$$

⇒

$$
\boldsymbol y=\boldsymbol H \boldsymbol \theta + \boldsymbol ϵ$$

ただし，

$$y=\begin{bmatrix}y_1\\y_2\\\vdots\\y_M\end{bmatrix}\quad x=\begin{bmatrix}x_1\\x_2\\\vdots\\x_M\end{bmatrix} \boldsymbol{\theta}=\begin{bmatrix}\theta_0\\\theta_1\\\vdots\\\theta_N
\end{bmatrix}\boldsymbol{ϵ}=\begin{bmatrix}ϵ_0\\ϵ_1\\\vdots\\ϵ_N
\end{bmatrix}$$

$$
H=\begin{bmatrix}1&\phi_1(x_1)&\cdots&\phi_N(x_1)\\1&\phi_1(x_2)&\cdots&\phi_N(x_2)\\\vdots&\vdots&\vdots&\vdots\\1&\phi_1(x_M)&\cdots&\phi_N(x_M)\end{bmatrix}$$

### 2.2 正規分布の仮定
- 事前分布(事前データ):

$$
f(\boldsymbol\theta)=N(\boldsymbol\theta|\boldsymbol\mu,\boldsymbol v^{-1})$$

- 尤度(観測分布，観測データ)

$$
f(\boldsymbol y|\boldsymbol\theta)=N(\boldsymbol y|\boldsymbol H \boldsymbol\theta, \boldsymbol\Lambda^{-1})$$

ただし，

$$
\boldsymbol{\Lambda}=\beta\boldsymbol{I}\\\boldsymbol{\mu}=\boldsymbol{0}\\\boldsymbol{v}=\alpha\boldsymbol{I}
$$

ここで，事前分布の分散行列が $\boldsymbol{v}^{-1}$であるため，$\alpha$ が小さいほど，事前分布のバラツキが大きくなる．観測分布の分散行列が $\boldsymbol{Λ}^{-1}$であるため，$\beta$ が小さいほど，観測分布のバラツキが大きくなる．

- ノイズ

$$
\boldsymbol ϵ=N(\boldsymbol 0,\sigma^2\boldsymbol I)$$


$$
\text{gzai}:\frac{\alpha}{\beta}$$

$$
S/N: 観測値 \boldsymbol y の標準偏差/ノイズ \boldsymbol ϵ の標準偏差$$


### 2.3 推定解
- 事後分布

$$
f(\boldsymbol\theta|\boldsymbol y) = N(\boldsymbol\theta|\boldsymbol{\overline{\theta}},\boldsymbol Γ^{-1})
$$

- 精度行列

$$
\boldsymbol Γ=\alpha \boldsymbol I + \beta \boldsymbol H^T  \boldsymbol H 
$$

- MMSE推定解

$$
 \boldsymbol{\overline{\theta}} = \boldsymbol Γ^{-1}  \beta \boldsymbol H^T \boldsymbol y
$$


## 3.プログラム
### 3.1 ライブラリのインポート


```python
# 以下のライブラリを使うので、あらかじめ読み込んでおいてください
import numpy as np
import scipy as sp
import pandas as pd
from pandas import Series, DataFrame
from scipy.stats import multivariate_normal

# 可視化ライブラリ
import matplotlib.pyplot as plt
import matplotlib as mpl
import matplotlib.mlab as mlab
import seaborn as sns
%matplotlib inline

# 小数第3位まで表示
%precision 3
```




    '%.3f'




```python
#観測データと回帰直線の描画関数
def plot_data(ax,x_min, x_max, x, y, x_seq):
    ax.set_xlim(x_min, x_max), ax.set_ylim(x_min, x_max)
    ax.scatter(x, y, color = 'b')
    ax.plot(x_seq, mu[0]+mu[1]*x_seq, color='r')

#事後分布の表示関数（ヒートマップ）
def plot_heatmap(ax, x_min, x_max, rv, x_seq, y_seq, pos):
    ax.set_xlim(x_min, x_max), ax.set_ylim(x_min, x_max)
    ax.pcolor(x_seq, y_seq, rv.pdf(pos), cmap=plt.cm.jet)
```

### 3.2 観測データの生成


```python
#シミュレーション条件
np.random.seed(0);               #ランダムシードの固定
c = np.array([-0.3, 0.5]);       #パラメータの真値 theta

sigma = 0.2;                     #ノイズの標準偏差
alpha =0.1;                      #事前分布の精度パラメタ

beta = 1.0 / (sigma ** 2);       #ノイズの精度パラメタ
gzai = alpha/beta;               #正則化定数
mu = np.zeros(2);                #事前分布の平均値ベクトル mu
Ganma = alpha * np.identity(2);  #事前分布の精度行列
inv_Ganma=np.linalg.inv(Ganma);  #事前分布の共分散行列
M = 50;                          #サンプル数

# 描画設定
x_min, x_max = -1.0, 1.0         #xの描画範囲
y_min, y_max = -1.0, 1.0         #yの描画範囲

# ノイズ
# np.random.normal(平均、標準偏差、サンプル数)
e= np.random.normal(0, sigma, M)

#観測データxの生成:np.random.uniform(a, b, サンプル数) aからｂまでの一様乱数
x=np.random.uniform(x_min, x_max, M)

#観測データyの生成
y=c[0]+c[1]*x+e
```

### 3.3 逐次ベイズ学習


```python
# 描画設定
fig = plt.figure(figsize = (10, 6 * M))
x_seq, y_seq = np.mgrid[x_min:x_max:.01, y_min:y_max:.01]  #メッシュグリッドの作成
pos = np.empty(x_seq.shape + (2,))
pos[:, :, 0] = x_seq; pos[:, :, 1] = y_seq
rv = multivariate_normal(mu,inv_Ganma)  #2次元正規分布の設定
axes = [fig.add_subplot(M + 1, 2, k) for k in range(1, 3)]
plot_heatmap(axes[1], x_min, x_max, rv, x_seq, y_seq, pos)

# 逐次ベイズ学習
result = []
for j in range(M):

    H = np.array([1.0, x[j]])

    # estimate parameters
    pre_Ganma = Ganma
    Ganma = pre_Ganma + beta * H.reshape(2, 1) * H
    inv_Ganma = np.linalg.inv(Ganma)
    mu = inv_Ganma.dot(pre_Ganma.dot(mu) + beta *  H * y[j])

    # plot
    rv = multivariate_normal(mu,inv_Ganma)  #2次元正規分布の設定
    axes = [fig.add_subplot(M + 1, 2, (j + 1) * 2 + k) for k in range(1, 3)]
    plot_data(axes[0], x_min, x_max, x[:j+1], y[:j+1], x_seq)
    plot_heatmap(axes[1], x_min, x_max, rv, x_seq, y_seq, pos)
    result.append([j, mu[0], mu[1], c[0], c[1]])

plt.show()
```


    
![png](output_9_0.png)
    



```python
#推定値確認
df = DataFrame(data=result, columns = ['j','mu[0]','mu[1]','c[0]','c[1]'])
df
```





  <div id="df-771f17da-5be9-4f79-9137-d6fa88df79cd" class="colab-df-container">
    <div>
<style scoped>
    .dataframe tbody tr th:only-of-type {
        vertical-align: middle;
    }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>j</th>
      <th>mu[0]</th>
      <th>mu[1]</th>
      <th>c[0]</th>
      <th>c[1]</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>-0.196174</td>
      <td>0.133802</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>0.689139</td>
      <td>1.559455</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>-0.091903</td>
      <td>0.495938</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-0.067200</td>
      <td>0.422107</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>-0.054682</td>
      <td>0.409682</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>5</th>
      <td>5</td>
      <td>-0.178664</td>
      <td>0.307591</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>6</th>
      <td>6</td>
      <td>-0.122925</td>
      <td>0.401981</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>7</th>
      <td>7</td>
      <td>-0.137210</td>
      <td>0.461177</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>8</th>
      <td>8</td>
      <td>-0.166260</td>
      <td>0.414366</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>9</th>
      <td>9</td>
      <td>-0.172869</td>
      <td>0.432366</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>10</th>
      <td>10</td>
      <td>-0.177469</td>
      <td>0.424336</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>11</th>
      <td>11</td>
      <td>-0.163138</td>
      <td>0.428268</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>12</th>
      <td>12</td>
      <td>-0.155363</td>
      <td>0.442815</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>13</th>
      <td>13</td>
      <td>-0.163447</td>
      <td>0.437846</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>14</th>
      <td>14</td>
      <td>-0.164698</td>
      <td>0.436398</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>15</th>
      <td>15</td>
      <td>-0.171319</td>
      <td>0.451264</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>16</th>
      <td>16</td>
      <td>-0.163291</td>
      <td>0.444574</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>17</th>
      <td>17</td>
      <td>-0.172927</td>
      <td>0.462257</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>18</th>
      <td>18</td>
      <td>-0.176718</td>
      <td>0.464833</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>19</th>
      <td>19</td>
      <td>-0.188428</td>
      <td>0.487693</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>20</th>
      <td>20</td>
      <td>-0.215711</td>
      <td>0.500593</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>21</th>
      <td>21</td>
      <td>-0.213568</td>
      <td>0.500739</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>22</th>
      <td>22</td>
      <td>-0.211275</td>
      <td>0.494130</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>23</th>
      <td>23</td>
      <td>-0.224171</td>
      <td>0.478620</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>24</th>
      <td>24</td>
      <td>-0.206389</td>
      <td>0.492221</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>25</th>
      <td>25</td>
      <td>-0.219011</td>
      <td>0.503851</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>26</th>
      <td>26</td>
      <td>-0.222030</td>
      <td>0.501989</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>27</th>
      <td>27</td>
      <td>-0.224571</td>
      <td>0.509199</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>28</th>
      <td>28</td>
      <td>-0.214985</td>
      <td>0.517580</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>29</th>
      <td>29</td>
      <td>-0.205499</td>
      <td>0.536104</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>30</th>
      <td>30</td>
      <td>-0.206971</td>
      <td>0.537011</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>31</th>
      <td>31</td>
      <td>-0.208106</td>
      <td>0.535684</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>32</th>
      <td>32</td>
      <td>-0.213388</td>
      <td>0.547829</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>33</th>
      <td>33</td>
      <td>-0.232095</td>
      <td>0.522497</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>34</th>
      <td>34</td>
      <td>-0.235294</td>
      <td>0.525330</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>35</th>
      <td>35</td>
      <td>-0.235660</td>
      <td>0.525998</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>36</th>
      <td>36</td>
      <td>-0.230049</td>
      <td>0.530969</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>37</th>
      <td>37</td>
      <td>-0.227055</td>
      <td>0.518751</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>38</th>
      <td>38</td>
      <td>-0.232876</td>
      <td>0.508740</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>39</th>
      <td>39</td>
      <td>-0.234585</td>
      <td>0.515564</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>40</th>
      <td>40</td>
      <td>-0.243208</td>
      <td>0.505223</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>41</th>
      <td>41</td>
      <td>-0.250083</td>
      <td>0.511826</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>42</th>
      <td>42</td>
      <td>-0.262091</td>
      <td>0.494843</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>43</th>
      <td>43</td>
      <td>-0.250672</td>
      <td>0.518613</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>44</th>
      <td>44</td>
      <td>-0.253364</td>
      <td>0.521745</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>45</th>
      <td>45</td>
      <td>-0.256704</td>
      <td>0.519256</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>46</th>
      <td>46</td>
      <td>-0.263807</td>
      <td>0.513454</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>47</th>
      <td>47</td>
      <td>-0.261113</td>
      <td>0.515408</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>48</th>
      <td>48</td>
      <td>-0.267102</td>
      <td>0.524312</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
    <tr>
      <th>49</th>
      <td>49</td>
      <td>-0.269706</td>
      <td>0.518660</td>
      <td>-0.3</td>
      <td>0.5</td>
    </tr>
  </tbody>
</table>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-771f17da-5be9-4f79-9137-d6fa88df79cd')"
            title="Convert this dataframe to an interactive table."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px" viewBox="0 -960 960 960">
    <path d="M120-120v-720h720v720H120Zm60-500h600v-160H180v160Zm220 220h160v-160H400v160Zm0 220h160v-160H400v160ZM180-400h160v-160H180v160Zm440 0h160v-160H620v160ZM180-180h160v-160H180v160Zm440 0h160v-160H620v160Z"/>
  </svg>
    </button>

  <style>
    .colab-df-container {
      display:flex;
      gap: 12px;
    }

    .colab-df-convert {
      background-color: #E8F0FE;
      border: none;
      border-radius: 50%;
      cursor: pointer;
      display: none;
      fill: #1967D2;
      height: 32px;
      padding: 0 0 0 0;
      width: 32px;
    }

    .colab-df-convert:hover {
      background-color: #E2EBFA;
      box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
      fill: #174EA6;
    }

    .colab-df-buttons div {
      margin-bottom: 4px;
    }

    [theme=dark] .colab-df-convert {
      background-color: #3B4455;
      fill: #D2E3FC;
    }

    [theme=dark] .colab-df-convert:hover {
      background-color: #434B5C;
      box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
      filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
      fill: #FFFFFF;
    }
  </style>

    <script>
      const buttonEl =
        document.querySelector('#df-771f17da-5be9-4f79-9137-d6fa88df79cd button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-771f17da-5be9-4f79-9137-d6fa88df79cd');
        const dataTable =
          await google.colab.kernel.invokeFunction('convertToInteractive',
                                                    [key], {});
        if (!dataTable) return;

        const docLinkHtml = 'Like what you see? Visit the ' +
          '<a target="_blank" href=https://colab.research.google.com/notebooks/data_table.ipynb>data table notebook</a>'
          + ' to learn more about interactive tables.';
        element.innerHTML = '';
        dataTable['output_type'] = 'display_data';
        await google.colab.output.renderOutput(dataTable, element);
        const docLink = document.createElement('div');
        docLink.innerHTML = docLinkHtml;
        element.appendChild(docLink);
      }
    </script>
  </div>


<div id="df-220e15f9-f6b0-487d-8c44-4dc64955aaf6">
  <button class="colab-df-quickchart" onclick="quickchart('df-220e15f9-f6b0-487d-8c44-4dc64955aaf6')"
            title="Suggest charts"
            style="display:none;">

<svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
     width="24px">
    <g>
        <path d="M19 3H5c-1.1 0-2 .9-2 2v14c0 1.1.9 2 2 2h14c1.1 0 2-.9 2-2V5c0-1.1-.9-2-2-2zM9 17H7v-7h2v7zm4 0h-2V7h2v10zm4 0h-2v-4h2v4z"/>
    </g>
</svg>
  </button>

<style>
  .colab-df-quickchart {
      --bg-color: #E8F0FE;
      --fill-color: #1967D2;
      --hover-bg-color: #E2EBFA;
      --hover-fill-color: #174EA6;
      --disabled-fill-color: #AAA;
      --disabled-bg-color: #DDD;
  }

  [theme=dark] .colab-df-quickchart {
      --bg-color: #3B4455;
      --fill-color: #D2E3FC;
      --hover-bg-color: #434B5C;
      --hover-fill-color: #FFFFFF;
      --disabled-bg-color: #3B4455;
      --disabled-fill-color: #666;
  }

  .colab-df-quickchart {
    background-color: var(--bg-color);
    border: none;
    border-radius: 50%;
    cursor: pointer;
    display: none;
    fill: var(--fill-color);
    height: 32px;
    padding: 0;
    width: 32px;
  }

  .colab-df-quickchart:hover {
    background-color: var(--hover-bg-color);
    box-shadow: 0 1px 2px rgba(60, 64, 67, 0.3), 0 1px 3px 1px rgba(60, 64, 67, 0.15);
    fill: var(--button-hover-fill-color);
  }

  .colab-df-quickchart-complete:disabled,
  .colab-df-quickchart-complete:disabled:hover {
    background-color: var(--disabled-bg-color);
    fill: var(--disabled-fill-color);
    box-shadow: none;
  }

  .colab-df-spinner {
    border: 2px solid var(--fill-color);
    border-color: transparent;
    border-bottom-color: var(--fill-color);
    animation:
      spin 1s steps(1) infinite;
  }

  @keyframes spin {
    0% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
      border-left-color: var(--fill-color);
    }
    20% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    30% {
      border-color: transparent;
      border-left-color: var(--fill-color);
      border-top-color: var(--fill-color);
      border-right-color: var(--fill-color);
    }
    40% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-top-color: var(--fill-color);
    }
    60% {
      border-color: transparent;
      border-right-color: var(--fill-color);
    }
    80% {
      border-color: transparent;
      border-right-color: var(--fill-color);
      border-bottom-color: var(--fill-color);
    }
    90% {
      border-color: transparent;
      border-bottom-color: var(--fill-color);
    }
  }
</style>

  <script>
    async function quickchart(key) {
      const quickchartButtonEl =
        document.querySelector('#' + key + ' button');
      quickchartButtonEl.disabled = true;  // To prevent multiple clicks.
      quickchartButtonEl.classList.add('colab-df-spinner');
      try {
        const charts = await google.colab.kernel.invokeFunction(
            'suggestCharts', [key], {});
      } catch (error) {
        console.error('Error during call to suggestCharts:', error);
      }
      quickchartButtonEl.classList.remove('colab-df-spinner');
      quickchartButtonEl.classList.add('colab-df-quickchart-complete');
    }
    (() => {
      let quickchartButtonEl =
        document.querySelector('#df-220e15f9-f6b0-487d-8c44-4dc64955aaf6 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_6eb68ce7-2d81-44b6-986c-a50a1783e042">
    <style>
      .colab-df-generate {
        background-color: #E8F0FE;
        border: none;
        border-radius: 50%;
        cursor: pointer;
        display: none;
        fill: #1967D2;
        height: 32px;
        padding: 0 0 0 0;
        width: 32px;
      }

      .colab-df-generate:hover {
        background-color: #E2EBFA;
        box-shadow: 0px 1px 2px rgba(60, 64, 67, 0.3), 0px 1px 3px 1px rgba(60, 64, 67, 0.15);
        fill: #174EA6;
      }

      [theme=dark] .colab-df-generate {
        background-color: #3B4455;
        fill: #D2E3FC;
      }

      [theme=dark] .colab-df-generate:hover {
        background-color: #434B5C;
        box-shadow: 0px 1px 3px 1px rgba(0, 0, 0, 0.15);
        filter: drop-shadow(0px 1px 2px rgba(0, 0, 0, 0.3));
        fill: #FFFFFF;
      }
    </style>
    <button class="colab-df-generate" onclick="generateWithVariable('df')"
            title="Generate code using this dataframe."
            style="display:none;">

  <svg xmlns="http://www.w3.org/2000/svg" height="24px"viewBox="0 0 24 24"
       width="24px">
    <path d="M7,19H8.4L18.45,9,17,7.55,7,17.6ZM5,21V16.75L18.45,3.32a2,2,0,0,1,2.83,0l1.4,1.43a1.91,1.91,0,0,1,.58,1.4,1.91,1.91,0,0,1-.58,1.4L9.25,21ZM18.45,9,17,7.55Zm-12,3A5.31,5.31,0,0,0,4.9,8.1,5.31,5.31,0,0,0,1,6.5,5.31,5.31,0,0,0,4.9,4.9,5.31,5.31,0,0,0,6.5,1,5.31,5.31,0,0,0,8.1,4.9,5.31,5.31,0,0,0,12,6.5,5.46,5.46,0,0,0,6.5,12Z"/>
  </svg>
    </button>
    <script>
      (() => {
      const buttonEl =
        document.querySelector('#id_6eb68ce7-2d81-44b6-986c-a50a1783e042 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
#推定値描画
# plot
df.plot(x='j', y=['mu[0]','mu[1]','c[0]','c[1]'] )
plt.title('sequential Bayesian estimation method(sigma = %.3f, S/N=%.3f, gzai=%.3f)'%(sigma, np.std(c[0]+c[1]*x)/sigma, gzai ))
plt.xlabel('j')
plt.ylabel('Estimate')
plt.xticks(np.arange(0, M+1, M/10)) #メモリの値を設定
plt.xlim(0,M+1)
plt.grid(True)
plt.show()

```


    
![png](output_11_0.png)
    


## 4. 考察

### 4.1 $\beta$


```python
#シミュレーション条件
np.random.seed(0);               #ランダムシードの固定
c = np.array([-0.3, 0.5]);       #パラメータの真値 theta

sigma = 0.8;                     #ノイズの標準偏差
alpha =0.1;                      #事前分布の精度パラメタ

beta = 1.0 / (sigma ** 2);       #ノイズの精度パラメタ
gzai = alpha/beta;               #正則化定数
mu = np.zeros(2);                #事前分布の平均値ベクトル mu
Ganma = alpha * np.identity(2);  #事前分布の精度行列
inv_Ganma=np.linalg.inv(Ganma);  #事前分布の共分散行列
M = 50;                          #サンプル数

# 描画設定
x_min, x_max = -1.0, 1.0         #xの描画範囲
y_min, y_max = -1.0, 1.0         #yの描画範囲

# ノイズ
# np.random.normal(平均、標準偏差、サンプル数)
e= np.random.normal(0, sigma, M)

#観測データxの生成:np.random.uniform(a, b, サンプル数) aからｂまでの一様乱数
x=np.random.uniform(x_min, x_max, M)

#観測データyの生成
y=c[0]+c[1]*x+e

# 逐次ベイズ学習
result = []
for j in range(M):

    H = np.array([1.0, x[j]])

    # estimate parameters
    pre_Ganma = Ganma
    Ganma = pre_Ganma + beta * H.reshape(2, 1) * H
    inv_Ganma = np.linalg.inv(Ganma)
    mu = inv_Ganma.dot(pre_Ganma.dot(mu) + beta *  H * y[j])
    # plot
    rv = multivariate_normal(mu,inv_Ganma)  #2次元正規分布の設定
    axes = [fig.add_subplot(M + 1, 2, (j + 1) * 2 + k) for k in range(1, 3)]
    plot_data(axes[0], x_min, x_max, x[:j+1], y[:j+1], x_seq)
    plot_heatmap(axes[1], x_min, x_max, rv, x_seq, y_seq, pos)
    result.append([j, mu[0], mu[1], c[0], c[1]])

#推定値確認
df = DataFrame(data=result, columns = ['j','mu[0]','mu[1]','c[0]','c[1]'])
#推定値描画
# plot
df.plot(x='j', y=['mu[0]','mu[1]','c[0]','c[1]'] )
plt.title('sequential Bayesian estimation method(sigma = %.3f, S/N=%.3f, gzai=%.3f)'%(sigma, np.std(c[0]+c[1]*x)/sigma, gzai ))
plt.xlabel('j')
plt.ylabel('Estimate')
plt.xticks(np.arange(0, M+1, M/10)) #メモリの値を設定
plt.xlim(0,M+1)
plt.grid(True)
plt.show()
```


    
![png](output_14_0.png)
    


ここで，ノイズの標準偏差を大きくして $\sigma=0.8$ とおく．

$$
\beta=\frac1\sigma^2
$$

$\beta$ が小さくなることがわかる．

上の描画結果が示すように，ノイズの標準偏差 $\sigma$ を大きく，$\beta$ が小さく，推定性能が悪くなることがわかる．それで，$\beta$ が小さくするとノイズのバラツキが大きくなり，観測分布の偏差行列 $Λ^{-1}$ が大きくなるため，推定の性質が悪くなることは一般的に考えられる．予想通りである．

### 4.2 $\alpha$



```python
#シミュレーション条件
np.random.seed(0);               #ランダムシードの固定
c = np.array([-0.3, 0.5]);       #パラメータの真値 theta

sigma = 0.2;                     #ノイズの標準偏差
alpha =0.8;                      #事前分布の精度パラメタ

beta = 1.0 / (sigma ** 2);       #ノイズの精度パラメタ
gzai = alpha/beta;               #正則化定数
mu = np.zeros(2);                #事前分布の平均値ベクトル mu
Ganma = alpha * np.identity(2);  #事前分布の精度行列
inv_Ganma=np.linalg.inv(Ganma);  #事前分布の共分散行列
M = 50;                          #サンプル数

# 描画設定
x_min, x_max = -1.0, 1.0         #xの描画範囲
y_min, y_max = -1.0, 1.0         #yの描画範囲

# ノイズ
# np.random.normal(平均、標準偏差、サンプル数)
e= np.random.normal(0, sigma, M)

#観測データxの生成:np.random.uniform(a, b, サンプル数) aからｂまでの一様乱数
x=np.random.uniform(x_min, x_max, M)

#観測データyの生成
y=c[0]+c[1]*x+e

# 逐次ベイズ学習
result = []
for j in range(M):

    H = np.array([1.0, x[j]])

    # estimate parameters
    pre_Ganma = Ganma
    Ganma = pre_Ganma + beta * H.reshape(2, 1) * H
    inv_Ganma = np.linalg.inv(Ganma)
    mu = inv_Ganma.dot(pre_Ganma.dot(mu) + beta *  H * y[j])
    # plot
    rv = multivariate_normal(mu,inv_Ganma)  #2次元正規分布の設定
    axes = [fig.add_subplot(M + 1, 2, (j + 1) * 2 + k) for k in range(1, 3)]
    plot_data(axes[0], x_min, x_max, x[:j+1], y[:j+1], x_seq)
    plot_heatmap(axes[1], x_min, x_max, rv, x_seq, y_seq, pos)
    result.append([j, mu[0], mu[1], c[0], c[1]])

#推定値確認
df = DataFrame(data=result, columns = ['j','mu[0]','mu[1]','c[0]','c[1]'])
#推定値描画
# plot
df.plot(x='j', y=['mu[0]','mu[1]','c[0]','c[1]'] )
plt.title('sequential Bayesian estimation method(sigma = %.3f, S/N=%.3f, gzai=%.3f)'%(sigma, np.std(c[0]+c[1]*x)/sigma, gzai ))
plt.xlabel('j')
plt.ylabel('Estimate')
plt.xticks(np.arange(0, M+1, M/10)) #メモリの値を設定
plt.xlim(0,M+1)
plt.grid(True)
plt.show()
```


    
![png](output_17_0.png)
    


ここで，$\alpha=0.8$ が大きくすると，事前分布の偏差行列
$v^{-1}$ が小さくなることがわかる．

上の描画結果が示すように，$\alpha=0.8$ が大きく，事前分布の偏差行列が小さく，推定性能が良くなることがわかる．それで，$\alpha=0.8$ が大きくすると事前分布のバラツキが小さい．すなわち，事前情報が正しさが高い．したがって，推定の性質が良いことだと考えられる．予想通りである．

### 4.3 S/N
2.2で述べたように,$$ S/N:観測値 \boldsymbol y の標準偏差/ノイズ \boldsymbol ϵ の標準偏差$$

したがって，$S/N$ が大きいほど，観測分布の標準偏差がノイズの標準偏差より大きい．$S/N$ が小さいほど，観測分布の標準偏差がノイズの標準偏差より小さい．

上記の結果から見ると，$S/N$ が大きいほど，推定性能がよいことがわかる．解析の予想通りである．


