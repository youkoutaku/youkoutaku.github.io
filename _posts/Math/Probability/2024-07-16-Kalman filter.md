---
title: カルマンフィルタ(Kalman filter)
date: 2024-07-16 15:03 +0900
categories: [Math, Probability Theory]
tags: [Probability, Kalman filter, JP]
author: Youkoutaku
math: true
img_path: /src/math/msB3/
image:
  path: output_12_0.png
  alt: 
---

Home Page:[Youkoutaku](https://youkoutaku.github.io/)

## 1.問題

スカラー変数のカルマンフィルタについて，観測雑音と
システム雑音の分散を変化させ，これらと状態推定値
の精度およびカルマンゲインとの関係について，シミュ
レーション例を通して考察しなさい

1. 設定するパラメータ
  - システム雑音の分散: $σ_w^2$
  - 観測雑音の分散: $σ_2$
  - 係数: $F=1, H=1$
2. 初期値
  - 状態推定値の初期値: $\overline{x}_0=0$
  - 推定誤差分散の初期値: $v_0=σ^2$


## 2.カルマンフィルタ
状態遷移式：

$$
x_k=Fx_{k-1}+w
$$

- $w=N(w \| 0,σ_w^2)$ : システム雑音
- $x_k$: 時刻 $t_k$ におけるシステム状態 $x$

観測モデル:

$$
y_k=Hx_{k}+ϵ$$
- $ϵ=N(ϵ \|0, \sigma^2)$: 観測雑音
- $y_k$: 時刻 $t_k$ における観測値 $y$

1. 設定するパラメータ \\
システム雑音の分散: $σ_w^2$ \\
観測雑音の分散: $σ^2$ \\
係数: $F, H$ \\
2. 初期値 \\
状態変数の初期値：$x_0$ \\
状態推定値の初期値: $\overline{x}_0$ \\
事後分布の分散の初期値：$v_0$

時間更新式：

$$
\begin{aligned}&p_{k-1}=F^{2}v_{k-1}+\sigma_{W}^{2}\\&{v}_{k}=(1-\kappa H)p_{k-1}\\& {x}_{k}=F \bar{x}_{k-1}+\kappa(y_{k}-HF \bar{x}_{k-1})\end{aligned}$$

カルマンゲイン：

$$
\kappa=\frac{p_{k-1}H}{\sigma^2+H^2p_{k-1}}$$

## 3.プログラム


### 3.1 ライブラリのインポートと関数定義


```python
# 以下のライブラリを使うので、あらかじめ読み込んでおいてください
import numpy as np
import scipy as sp
import pandas as pd
from pandas import Series, DataFrame

# 可視化ライブラリ
import matplotlib.pyplot as plt
import matplotlib as mpl
#import seaborn as sns
%matplotlib inline

# 小数第3位まで表示
%precision 3

#カルマンフィルタ
def kf_scalar(F,H,Q,R,y,xhat,V):
    #カルマンフィルタの更新を行う
    P=F*F*V+Q;                      #①誤差分散p(k-1)
    G=P*H/(H*H*P+R);                #②カルマンゲインχ
    V=(1-G*H)*P;                   #③事後分布の分散行列(v(k))
    xhat=F*xhat+G*(y-H*F*xhat);    #③推定値(x(k)-)
    return(xhat, V, G);
```

### 3.2 パラメータ設定


```python
F=1; H=1;     #係数

sigma_w=2.0;  #システム雑音の標準偏差(σw)
sigma=2.0;    #観測雑音の標準偏差(σ)

Q=sigma_w**2; #システム雑音の分散(σw^2)
R=sigma**2;   #観測雑音の分散(σ^2)
K=300;        #データ数

#初期値の設定
x=0.0;        #状態量の初期値(x0)
xhat= 0.0;    #状態推定値の初期値(x0-)
V=0;          #事後分布の分散の初期値(v0)

# ランダムシードの固定
np.random.seed(0)
```

### 3.3 シミュレーション


```python
result=[]
for k in range(0,K):

    #雑音信号の生成:np.random.normal(平均、標準偏差)
    w = np.random.normal(0, sigma_w); #システム雑音(w)
    v = np.random.normal(0, sigma);   #観測雑音(v)

    #xの真値の計算
    x = F*x + w;

    #観測値yの計算
    y = H*x + v;

    #カルマンフィルタによる状態推定
    xhat, V ,G = kf_scalar(F,H,Q,R,y,xhat,V);

    result.append([k, x, y, xhat, V, G])

df=DataFrame(data=result, columns=['k','x','y','xhat','V','G'])

```


```python
df
```





  <div id="df-ab514e2f-81bc-4f04-954e-cb245f459f67" class="colab-df-container">
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
      <th>k</th>
      <th>x</th>
      <th>y</th>
      <th>xhat</th>
      <th>V</th>
      <th>G</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3.528105</td>
      <td>4.328419</td>
      <td>2.164210</td>
      <td>2.000000</td>
      <td>0.500000</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>5.485581</td>
      <td>9.967367</td>
      <td>6.846104</td>
      <td>2.400000</td>
      <td>0.600000</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>9.220697</td>
      <td>7.266141</td>
      <td>7.104588</td>
      <td>2.461538</td>
      <td>0.615385</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>11.120873</td>
      <td>10.818159</td>
      <td>9.398264</td>
      <td>2.470588</td>
      <td>0.617647</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>10.914436</td>
      <td>11.735633</td>
      <td>10.842706</td>
      <td>2.471910</td>
      <td>0.617978</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>295</th>
      <td>295</td>
      <td>-48.742807</td>
      <td>-47.864721</td>
      <td>-49.795159</td>
      <td>2.472136</td>
      <td>0.618034</td>
    </tr>
    <tr>
      <th>296</th>
      <td>296</td>
      <td>-49.181889</td>
      <td>-51.349962</td>
      <td>-50.756080</td>
      <td>2.472136</td>
      <td>0.618034</td>
    </tr>
    <tr>
      <th>297</th>
      <td>297</td>
      <td>-48.478329</td>
      <td>-47.719858</td>
      <td>-48.879592</td>
      <td>2.472136</td>
      <td>0.618034</td>
    </tr>
    <tr>
      <th>298</th>
      <td>298</td>
      <td>-49.418395</td>
      <td>-49.851858</td>
      <td>-49.480485</td>
      <td>2.472136</td>
      <td>0.618034</td>
    </tr>
    <tr>
      <th>299</th>
      <td>299</td>
      <td>-51.278708</td>
      <td>-51.635886</td>
      <td>-50.812596</td>
      <td>2.472136</td>
      <td>0.618034</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 6 columns</p>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-ab514e2f-81bc-4f04-954e-cb245f459f67')"
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
        document.querySelector('#df-ab514e2f-81bc-4f04-954e-cb245f459f67 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-ab514e2f-81bc-4f04-954e-cb245f459f67');
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


<div id="df-b6e3c42a-e582-45ac-9f55-316ab3b46aa2">
  <button class="colab-df-quickchart" onclick="quickchart('df-b6e3c42a-e582-45ac-9f55-316ab3b46aa2')"
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
        document.querySelector('#df-b6e3c42a-e582-45ac-9f55-316ab3b46aa2 button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_27bc95c3-195f-4c09-9a28-afd491d38884">
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
        document.querySelector('#id_27bc95c3-195f-4c09-9a28-afd491d38884 button.colab-df-generate');
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




### 3.4 描画


```python
#推定結果の描画
fig, axes = plt.subplots(nrows=4, ncols=1, figsize=(10, 15))
df.plot(ax=axes[0],x='k', y=['x','xhat','y'], color=["black","red","green"] )
axes[0].set_xlabel("k")
axes[0].set_ylabel("x, xhat, y")
axes[0].set_xlim(0, K)
axes[0].set_ylim(-60, 40)
axes[0].grid(True)

df.plot(ax=axes[1],x='k', y=['x','xhat'], color=["black","red"] )
axes[1].set_xlabel("k")
axes[1].set_ylabel("x, xhat")
axes[1].set_xlim(0, K)
axes[1].set_ylim(-60, 40)
axes[1].grid(True)

#カルマンゲインの描画
df.plot(ax=axes[2],x='k', y='G', color="red" )
axes[2].set_xlabel("k")
axes[2].set_ylabel("Kalman gain")
axes[2].set_xlim(0, K)
axes[2].set_ylim(0, 1)
axes[2].grid(True)

#推定誤差分散の描画
df.plot(ax=axes[3],x='k', y='V', color="blue" )
axes[3].set_xlabel("k")
axes[3].set_ylabel("estimate error variance")
axes[3].set_xlim(0, K)
axes[3].set_ylim(0, 3)
axes[3].grid(True)

```


    
![png](output_12_0.png)
    


## 4.考察

### 4.1 $σ_w$

システム雑音の標準偏差 $σ_w=15$ と観測雑音の標準偏差 $σ=2$ と設定する．



```python
#-----------------------------------------
#      パラメータの設定
#-----------------------------------------
F=1; H=1;     #係数

sigma_w=15.0;  #システム雑音の標準偏差(σw)
sigma=2.0;     #観測雑音の標準偏差(σ)

Q=sigma_w**2; #システム雑音の分散(σw^2)
R=sigma**2;   #観測雑音の分散(σ^2)
K=300;        #データ数

#初期値の設定
x=0.0;        #状態量の初期値(x0)
xhat= 0.0;    #状態推定値の初期値(x0-)
V=0;          #事後分布の分散の初期値(v0)

result=[]
for k in range(0,K):

    #雑音信号の生成:np.random.normal(平均、標準偏差)
    w = np.random.normal(0, sigma_w); #システム雑音(w)
    v = np.random.normal(0, sigma);   #観測雑音(v)

    #xの真値の計算
    x = F*x + w;

    #観測値yの計算
    y = H*x + v;

    #カルマンフィルタによる状態推定
    xhat, V ,G = kf_scalar(F,H,Q,R,y,xhat,V);
    result.append([k, x, y, xhat, V, G])

df_1=DataFrame(data=result, columns=['k','x','y','xhat','V','G'])
```


```python
df_1
```





  <div id="df-12020ee2-8410-4ca0-81e9-ee9ae8e72b2f" class="colab-df-container">
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
      <th>k</th>
      <th>x</th>
      <th>y</th>
      <th>xhat</th>
      <th>V</th>
      <th>G</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>-23.256440</td>
      <td>-22.421803</td>
      <td>-22.030155</td>
      <td>3.930131</td>
      <td>0.982533</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>-37.421968</td>
      <td>-36.945761</td>
      <td>-36.689623</td>
      <td>3.931310</td>
      <td>0.982827</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>-58.511411</td>
      <td>-59.691527</td>
      <td>-59.296528</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>-60.168752</td>
      <td>-63.490152</td>
      <td>-63.418137</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>-58.441534</td>
      <td>-59.199829</td>
      <td>-59.272268</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>295</th>
      <td>295</td>
      <td>290.495100</td>
      <td>292.742911</td>
      <td>292.757904</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>296</th>
      <td>296</td>
      <td>287.893142</td>
      <td>286.873083</td>
      <td>286.974139</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>297</th>
      <td>297</td>
      <td>308.780918</td>
      <td>310.856090</td>
      <td>310.445978</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>298</th>
      <td>298</td>
      <td>309.062795</td>
      <td>307.875240</td>
      <td>307.919386</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
    <tr>
      <th>299</th>
      <td>299</td>
      <td>278.884591</td>
      <td>280.063998</td>
      <td>280.542343</td>
      <td>3.931310</td>
      <td>0.982828</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 6 columns</p>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-12020ee2-8410-4ca0-81e9-ee9ae8e72b2f')"
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
        document.querySelector('#df-12020ee2-8410-4ca0-81e9-ee9ae8e72b2f button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-12020ee2-8410-4ca0-81e9-ee9ae8e72b2f');
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


<div id="df-d7d7906c-14e4-4669-9d8b-40e512684abd">
  <button class="colab-df-quickchart" onclick="quickchart('df-d7d7906c-14e4-4669-9d8b-40e512684abd')"
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
        document.querySelector('#df-d7d7906c-14e4-4669-9d8b-40e512684abd button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_e1d45ebe-d026-4af1-98e8-b0a324498211">
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
    <button class="colab-df-generate" onclick="generateWithVariable('df_1')"
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
        document.querySelector('#id_e1d45ebe-d026-4af1-98e8-b0a324498211 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df_1');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
#推定結果の描画
fig, axes = plt.subplots(nrows=4, ncols=1, figsize=(10, 15))
df_1.plot(ax=axes[0],x='k', y=['x','xhat','y'], color=["black","red","green"] )
axes[0].set_xlabel("k")
axes[0].set_ylabel("x, xhat, y")
axes[0].set_xlim(0, K)
axes[0].set_ylim(-300, 150)
axes[0].grid(True)

df_1.plot(ax=axes[1],x='k', y=['x','xhat'], color=["black","red"] )
axes[1].set_xlabel("k")
axes[1].set_ylabel("x, xhat")
axes[1].set_xlim(0, K)
axes[1].set_ylim(-300, 150)
axes[1].grid(True)

#カルマンゲインの描画
df_1.plot(ax=axes[2],x='k', y='G', color="red" )
axes[2].set_xlabel("k")
axes[2].set_ylabel("Kalman gain")
axes[2].set_xlim(0, K)
axes[2].set_ylim(0, 1.5)
axes[2].grid(True)

#推定誤差分散の描画
df_1.plot(ax=axes[3],x='k', y='V', color="blue" )
axes[3].set_xlabel("k")
axes[3].set_ylabel("estimate error variance")
axes[3].set_xlim(0, K)
axes[3].set_ylim(0, 5)
axes[3].grid(True)
```


    
![png](output_17_0.png)
    


上の結果により，システム雑音の標準偏差が観測雑音の標準偏差より大きい場合に，カルマンフィルタゲインが大きくなり，推定性能が良くなることがわかる．

1. カルマンフィルタゲインについて2のカルマンフィルタの説明によって次の式が得られる。
$$
\kappa=\frac{p_{k-1}H}{\sigma^2+H^2p_{k-1}}=\frac{(F^{2}v_{k-1}+\sigma_{W}^{2})H}{\sigma^2+(F^{2}v_{k-1}+\sigma_{W}^{2})H^2}=\frac{H}{\frac{\sigma^2}{(F^{2}v_{k-1}+\sigma_{W}^{2})}+H^2}$$

よって，$σ_w$が大きいほど，カルマンフィルタゲイン$\kappa$が大きいことが解析の通りである．
2. カルマンフィルタゲインが大きいため，時間更新式の推定値$x_k$ における観測値の重要さが高くなり，システムモデルによる事前予測値の重要さが低くなる．
3. システム雑音の標準偏差が高いため，システムモデルによる事前予測値のバラツキが大きい．観測雑音の標準偏差が小さいため，観測値のバラツキが小さい．そのために，事前予測値より，観測値の方は信頼度が高い．

### 4.2 $σ^2$

システム雑音の標準偏差 $σ_w=2$ と観測雑音の標準偏差 $σ=15$ と設定する．


```python
#-----------------------------------------
#      パラメータの設定
#-----------------------------------------
F=1; H=1;     #係数

sigma_w=2.0;  #システム雑音の標準偏差(σw)
sigma=15.0;    #観測雑音の標準偏差(σ)

Q=sigma_w**2; #システム雑音の分散(σw^2)
R=sigma**2;   #観測雑音の分散(σ^2)
K=300;        #データ数

#初期値の設定
x=0.0;        #状態量の初期値(x0)
xhat= 0.0;    #状態推定値の初期値(x0-)
V=0;          #事後分布の分散の初期値(v0)

# ランダムシードの固定
np.random.seed(0)

result=[]
for k in range(0,K):

    #雑音信号の生成:np.random.normal(平均、標準偏差)
    w = np.random.normal(0, sigma_w); #システム雑音(w)
    v = np.random.normal(0, sigma);   #観測雑音(v)

    #xの真値の計算
    x = F*x + w;

    #観測値yの計算
    y = H*x + v;

    #カルマンフィルタによる状態推定
    xhat, V ,G = kf_scalar(F,H,Q,R,y,xhat,V);

    result.append([k, x, y, xhat, V, G])

df_2=DataFrame(data=result, columns=['k','x','y','xhat','V','G'])
```


```python
df_2
```





  <div id="df-ef591dba-b9c3-4876-8204-c0e043d28fa6" class="colab-df-container">
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
      <th>k</th>
      <th>x</th>
      <th>y</th>
      <th>xhat</th>
      <th>V</th>
      <th>G</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>0</th>
      <td>0</td>
      <td>3.528105</td>
      <td>9.530463</td>
      <td>0.166471</td>
      <td>3.930131</td>
      <td>0.017467</td>
    </tr>
    <tr>
      <th>1</th>
      <td>1</td>
      <td>5.485581</td>
      <td>39.098979</td>
      <td>1.491932</td>
      <td>7.660149</td>
      <td>0.034045</td>
    </tr>
    <tr>
      <th>2</th>
      <td>2</td>
      <td>9.220697</td>
      <td>-5.438472</td>
      <td>1.150474</td>
      <td>11.085658</td>
      <td>0.049270</td>
    </tr>
    <tr>
      <th>3</th>
      <td>3</td>
      <td>11.120873</td>
      <td>8.850515</td>
      <td>1.634302</td>
      <td>14.137759</td>
      <td>0.062834</td>
    </tr>
    <tr>
      <th>4</th>
      <td>4</td>
      <td>10.914436</td>
      <td>17.073413</td>
      <td>2.786040</td>
      <td>16.784706</td>
      <td>0.074599</td>
    </tr>
    <tr>
      <th>...</th>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
      <td>...</td>
    </tr>
    <tr>
      <th>295</th>
      <td>295</td>
      <td>-48.742807</td>
      <td>-42.157163</td>
      <td>-49.681037</td>
      <td>28.066593</td>
      <td>0.124740</td>
    </tr>
    <tr>
      <th>296</th>
      <td>296</td>
      <td>-49.181889</td>
      <td>-65.442438</td>
      <td>-51.647121</td>
      <td>28.066593</td>
      <td>0.124740</td>
    </tr>
    <tr>
      <th>297</th>
      <td>297</td>
      <td>-48.478329</td>
      <td>-42.789796</td>
      <td>-50.542254</td>
      <td>28.066593</td>
      <td>0.124740</td>
    </tr>
    <tr>
      <th>298</th>
      <td>298</td>
      <td>-49.418395</td>
      <td>-52.669367</td>
      <td>-50.807591</td>
      <td>28.066593</td>
      <td>0.124740</td>
    </tr>
    <tr>
      <th>299</th>
      <td>299</td>
      <td>-51.278708</td>
      <td>-53.957544</td>
      <td>-51.200518</td>
      <td>28.066593</td>
      <td>0.124740</td>
    </tr>
  </tbody>
</table>
<p>300 rows × 6 columns</p>
</div>
    <div class="colab-df-buttons">

  <div class="colab-df-container">
    <button class="colab-df-convert" onclick="convertToInteractive('df-ef591dba-b9c3-4876-8204-c0e043d28fa6')"
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
        document.querySelector('#df-ef591dba-b9c3-4876-8204-c0e043d28fa6 button.colab-df-convert');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      async function convertToInteractive(key) {
        const element = document.querySelector('#df-ef591dba-b9c3-4876-8204-c0e043d28fa6');
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


<div id="df-7e91a7ad-6901-4715-85bf-356308efe4ba">
  <button class="colab-df-quickchart" onclick="quickchart('df-7e91a7ad-6901-4715-85bf-356308efe4ba')"
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
        document.querySelector('#df-7e91a7ad-6901-4715-85bf-356308efe4ba button');
      quickchartButtonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';
    })();
  </script>
</div>

  <div id="id_f7d546d0-8d52-4a92-8049-e7cfa1620458">
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
    <button class="colab-df-generate" onclick="generateWithVariable('df_2')"
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
        document.querySelector('#id_f7d546d0-8d52-4a92-8049-e7cfa1620458 button.colab-df-generate');
      buttonEl.style.display =
        google.colab.kernel.accessAllowed ? 'block' : 'none';

      buttonEl.onclick = () => {
        google.colab.notebook.generateWithVariable('df_2');
      }
      })();
    </script>
  </div>

    </div>
  </div>





```python
#推定結果の描画
fig, axes = plt.subplots(nrows=4, ncols=1, figsize=(10, 15))
df_2.plot(ax=axes[0],x='k', y=['x','xhat','y'], color=["black","red","green"] )
axes[0].set_xlabel("k")
axes[0].set_ylabel("x, xhat, y")
axes[0].set_xlim(0, K)
axes[0].set_ylim(-85, 40)
axes[0].grid(True)

df_2.plot(ax=axes[1],x='k', y=['x','xhat'], color=["black","red"] )
axes[1].set_xlabel("k")
axes[1].set_ylabel("x, xhat")
axes[1].set_xlim(0, K)
axes[1].set_ylim(-85, 40)
axes[1].grid(True)

#カルマンゲインの描画
df_2.plot(ax=axes[2],x='k', y='G', color="red" )
axes[2].set_xlabel("k")
axes[2].set_ylabel("Kalman gain")
axes[2].set_xlim(0, K)
axes[2].set_ylim(0, 0.3)
axes[2].grid(True)

#推定誤差分散の描画
df_2.plot(ax=axes[3],x='k', y='V', color="blue" )
axes[3].set_xlabel("k")
axes[3].set_ylabel("estimate error variance")
axes[3].set_xlim(0, K)
axes[3].set_ylim(0, 30)
axes[3].grid(True)
```


    
![png](output_22_0.png)
    


上の結果により，システム雑音の標準偏差が観測雑音の標準偏差より小さい場合に，カルマンフィルタゲインが小さくなり，推定性能が悪くなることがわかる．

1. カルマンフィルタゲインの式により，

$$
\kappa=\frac{p_{k-1}H}{\sigma^2+H^2p_{k-1}}=\frac{(F^{2}v_{k-1}+\sigma_{W}^{2})H}{\sigma^2+(F^{2}v_{k-1}+\sigma_{W}^{2})H^2}=\frac{H}{\frac{\sigma^2}{(F^{2}v_{k-1}+\sigma_{W}^{2})}+H^2}
$$

よって，$σ$が大きいほど，カルマンフィルタゲイン$\kappa$が小さいことが解析の通りである．
1. カルマンフィルタゲインが小さいため，時間更新式の推定値 $x_k$ における観測値の重要さが低くなり，システムモデルによる事前予測値の重要さが高くなる．
2. システム雑音の標準偏差が低いため，システムモデルによる事前予測値のバラツキが小さい．観測雑音の標準偏差が小さいため，観測値のバラツキが大きい．そのために，観測値より，事前予測値の方は信頼度が高い．
