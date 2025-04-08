---
title: VSCodeによるMATLABの使用
date: 2025-01-08 20:00:00 +0900
categories: [Programming, MATLAB]
tags: [MATLAB, VSCode, JP]
author: Youkoutaku
math: true
image: https://preview.redd.it/matlab-via-vscode-v0-me0ex76f02oe1.png?width=1064&format=png&auto=webp&s=9b4d93a1b862f0f3762c4b5c0a140301b31eddf4
---

MATLABは、科学技術計算やデータ分析、アルゴリズム開発などに利用される強力なツールですが、専用のMATLABエディタ以外で作業したい場合もあります。VSCodeは、とても人気なエディタであり、たくさんの開発環境に適用できます。例え、MATLAB、LaTex, Python, Cなどがあります。VSCode (Visual Studio Code) を使用してMATLABコードを編集、実行する方法について解説します。

---

## 1. 必要な準備
VSCodeでMATLABを使用するためには、以下の手順を行う必要があります。
### 必要なソフトウェア

1. [MATLAB](https://matlab.mathworks.com): MATLABがインストールされている必要があります。
2. [VSCode](https://code.visualstudio.com): 最新バージョンのVisual Studio Codeをインストールしてください。
3. [MATLAB Extension](https://marketplace.visualstudio.com/items?itemName=MathWorks.language-matlab): VSCodeでMATLABコードをサポートするための拡張機能。
4. 推奨拡張機能:
	- **Code Runner**: ワンクリックでコードを実行。
	- **Bracket Pair Colorizer**: 括弧の対応を色分け。

---

## 2. VSCodeのセットアップ
### 2.1 MATLAB拡張機能のインストール

1. VSCodeを起動します。
2. サイドバーの「Extensions(拡張機能)」アイコンをクリックします（ショートカットキー: `Ctrl+Shift+X`）。
3. 検索バーに「MATLAB」と入力し、MathWorksが提供する公式のMATLAB拡張機能をインストールします。

![](https://preview.redd.it/matlab-via-vscode-v0-me0ex76f02oe1.png?width=1064&format=png&auto=webp&s=9b4d93a1b862f0f3762c4b5c0a140301b31eddf4)

### 2.2 MATLAB CLIパスの設定
MATLABコードを実行するには、MATLABのコマンドラインインターフェイス (CLI) へのパスを指定する必要があります。

1. MATLABがインストールされているディレクトリを確認します。
    - Windowsの場合: `C:\Program Files\MATLAB\R202Xx\bin\matlab.exe`
    - macOSの場合: `/Applications/MATLAB_R202Xx.app/bin/matlab`
    - Linuxの場合: `/usr/local/MATLAB/R202Xx/bin/matlab`
2. VSCodeの設定を開きます（`Ctrl+,`）。
3. 検索バーに`MATLAB Path`と入力し、MATLAB CLIのフルパスを設定します。

![](https://preview.redd.it/matlab-via-vscode-v0-71htg76f02oe1.png?width=1058&format=png&auto=webp&s=b4c47eebaa87892c389699792fd242fffbd7c64c)

---

## 3. MATLABコードの編集と実行
### 3.1 新しいMATLABスクリプトの作成
1. VSCodeで新しいファイルを作成します（`Ctrl+N`）。
2. ファイルを`example.m`として保存します。
3. MATLABコードを書きます。以下は簡単な例です：

```
   x = 0:0.1:10;
   y = sin(x);
   plot(x, y);
   title('Sine Wave');
   xlabel('x');
   ylabel('sin(x)');
```

### 3.2 MATLABコードの実行
1. ターミナルを開きます（`Ctrl+``）。
2. MATLAB CLIを使用してスクリプトを実行します：

```
matlab -r "example"
```

MATLABのデスクトップ環境が起動し、プロットが表示されます。

### 3.3 拡張機能 **Code Runner** による実行
1. VSCodeで「Code Runner」拡張機能をインストールします。
    - 拡張機能の検索バーに「Code Runner」と入力し、インストールをクリック。
2. MATLABスクリプトを開き、右上の「Run Code」ボタンをクリックするだけでコードが実行されます。
    - または、`Ctrl+Alt+N`で実行可能。
3. 実行結果はVSCodeの出力ターミナルに表示されます。

![](https://preview.redd.it/matlab-via-vscode-v0-2pgle46f02oe1.png?width=640&crop=smart&auto=webp&s=d8e76100b548aaa2546a1c67ccbfa82285126087)

## 4. 補足情報
### MATLAB Live Scriptsの注意点
VSCodeではMATLAB Live Scripts (`.mlx`ファイル) の編集は直接サポートされていません。これらを編集する場合は、MATLAB専用のエディタを使用してください。

## まとめ
VSCodeでMATLABを使用することで、柔軟な作業環境を構築できます。特に、他のプログラミング言語と組み合わせたプロジェクトを扱う際には便利です。ぜひ試してみてください！
