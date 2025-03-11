---
title: WindowsでUbuntuを削除する
date: 2023-12-04 13:00:00 +0900
categories:
  - Development
  - Linux
tags:
  - Linux
  - Ubuntu
  - JP
author: Youkoutaku
---

## **WindowsでUbuntuのシステムファイルとパーティションを削除する方法**

### **1. Diskpartを開く**
- `Win + R` を押し、`diskpart` と入力して `Enter` を押す。

### **2. Diskpartでコマンドを実行**
- **利用可能なディスクを確認**:

```sh
  list disk
```

- **Ubuntuがインストールされているディスクを選択** (例: Disk 1):

```sh
select disk <disk_number>
```

- **選択したディスクのパーティションを確認**:
```sh
list partition
```

- **Ubuntuのシステムファイルが含まれるパーティション(システム)を選択** (例: Partition 1):
```sh
select partition 1
```

- **ドライブレター (P:) を割り当てる**:
```sh
assign letter=P
```

### **3. Ubuntuのシステムファイルを削除**
- **メモ帳を管理者として実行**。
- **エクスプローラーで `P:` に移動し、EFIフォルダにあるUbuntuフォルダを削除**。

### **4. 割り当てたドライブレターを削除**
- **Diskpartに戻り、以下のコマンドを実行**:
```sh
remove letter=P
```

### **5. ディスク管理を使用してUbuntuのパーティションを削除**
- `Win + X` → `ディスクの管理` を開く。
- Ubuntuのパーティションを探す。
- **右クリックして「ボリュームの削除」を選択**。
- (**オプション**) 未割り当て領域を既存のパーティションに統合。

---

これでUbuntuのシステムファイルとパーティションの削除が完了です！
