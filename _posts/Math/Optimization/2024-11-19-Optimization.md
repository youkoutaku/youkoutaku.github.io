---
title: 最適化問題
date: 2024-11-19 13:00 +0900
categories: [Math, Optimization]
tags: [Math, Optimization, JP]
author: Youkoutaku
math: true
#img_path:
image:
  path: https://preview.redd.it/optiminal-control-v0-7og4u28zm9ke1.png?width=640&crop=smart&auto=webp&s=18b85effdb8f56860d18ef279ffd5979725bfe08
  alt: Optimal Control
---

### 最適化問題
- Performance index / Performance function 評価関数, Cost function コスト関数 / objective function 目的関数:
	 工学的な問題の結果の望ましさが自由変数の関数として実数値で表す関数である．
-  Optimization problem  最適化問題:
	- 評価関数を最小化するや最大化する自由変数を求める．
- Constraint 拘束条件 / 制約条件:
	- 自由変数を選ぶ範囲である．

### 数理計画問題
-  Mathematical  programming problem  数理計画問題: 自由変数が有限個の変数である最適化問題
	-  Linear programming problem  線形計画問題: 評価関数と拘束条件が1次式
	-  Quadratic programming problem  2次計画問題: 評価関数と拘束条件が2次式
	-  Nonlinear programming problem  非線形計画問題: 線形計画以外の問題

### 汎関数と変分問題
評価関数は，関数に対応して結果の望ましさを実数値として与えるので，いわば関数の関数になる．
-  functional  汎関数: 関数の関数
-  variational problem  変分問題: 汎関数を最小化する問題
- variation 変分: 関数の微分を汎関数に拡張した演算
- argument function in a functional 変関数: 汎関数の変数に相当する関数

### 最適制御
さまざまな制御の問題も，制御目的を評価関数の最小化として表すことができれば最適化問題になる．1つはフィードバックゲインなどを自由変数とみなせば，制御系設計は数理計画問題に帰着される．一方で，制御対象のシステムに対する制御入力を変関数，評価関数を汎関数とみなせば，制御問題は変分問題に帰着され，これを**最適制御問題**(Optimal control problem)という．また，最適問題において制御入力を最適化する時刻の範囲を**評価区間**(horizon)といい，評価関数を最小にする制御入力を**最適制御**(optimal control)という．

最適制御は，フィードフォワード制御に用いられる．しかし，現実の世界ではフィードバック制御は基本であり，状態の関数としてフィードバック則を決める．線形システムを除いて最適制御問題はフィードバック制御にあまり適さない．

#### モデル予測制御
最適制御は，時刻の関数としてフィードバック制御を行うのは，モデル予測制御(Model Predictive Control )である．評価区間の長さを$T$として，常に現在の時刻 $t$ を最適制御問題の初期時刻として，現在から評価区間を経った未来時刻 $t+T$ までの最適制御を求め，最適制御の最初項を実際の制御入力として用いる．

評価関数:

$$
J=\phi(x(t+T))+\int_{t}^{t+T}L(x(\tau),u(\tau),\tau)d\tau
$$

- receding horizon control (RHC)
- moving horizon control

### 最適推定問題

## 制御と最適化
実際に制御の問題を最適化によって解くには，以下のステップが重要である．
1. 制御対象のモデルを作成する．(モデルベース)
   - 現実ではほとんど非線形モデルである．モデルが必要不可欠である．モデルが不正解だと，予測した最適解が無意識なものになる．
2. 目的にあった評価関数(アルゴリズム)と拘束条件(物理的制限)を設定する．
   - 良い性能を得るために，適切な評価関数を設計する．
   - 制御対象の物理的制限によって拘束条件を与える
3. 最適解が満たすべき条件を得る．
   - 解が簡単に求められなくて，条件を満たすように解の候補を繰り返し修正していく．その繰り返しは多くの計算が必要であり，計算時間が制御サンプル時間より多くなる場合がある．そのために実用化が難しい．
   - 近年，計算機の性能と数値解法の進歩により，実時間最適化(real-time optimization)が実現可能になる．
4. 数値計算によって最適解を見つける．
5. 2-4を繰り返して，望ましい応答が得られるように評価関数を調整する．

### オフライン最適制御と実時間最適制御

![](https://preview.redd.it/optiminal-control-v0-7og4u28zm9ke1.png?width=640&crop=smart&auto=webp&s=18b85effdb8f56860d18ef279ffd5979725bfe08)

---
## 参考資料
- [大塚 敏之](https://www.ids.sys.i.kyoto-u.ac.jp/index.html), 非線形最適制御入門(Introduction to Nonlinear Optimal Control)，コロナ社, 2011.