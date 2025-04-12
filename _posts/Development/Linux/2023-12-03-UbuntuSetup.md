---
title: Ubuntu Setup
date: 2023-12-03 15:06:03 +0900
categories:
  - Development
  - Linux
tags:
  - Linux
  - Pycharm
  - ROS
  - CUDA
  - Pytorch
  - DNN
  - Ubuntu
author: Youkoutaku
math: true
mermaid: true
---

## 0. Delete Ubuntu

- [Delete Ubuntu System on Windows](https://youkoutaku.github.io/posts/DeleteUbuntu/)
- [双系统删除ubuntu](https://blog.csdn.net/ZChen1996/article/details/115436436?ops_request_misc=%257B%2522request%255Fid%2522%253A%2522165694741616782184622934%2522%252C%2522scm%2522%253A%252220140713.130102334..%2522%257D&request_id=165694741616782184622934&biz_id=0&utm_medium=distribute.pc_search_result.none-task-blog-2~all~top_click~default-2-115436436-null-null.142%5Ev30%5Econtrol,185%5Ev2%5Econtrol&utm_term=%E5%8F%8C%E7%B3%BB%E7%BB%9F%E5%8D%B8%E8%BD%BDubuntu&spm=1018.2226.3001.4187)

### 0.1 Open Diskpart
- win+R: `diskpart`

### 0.2 Commands in Diskpart
- Check available disks:
```sh
  list disk
```

- Select the disk where Ubuntu is installed (e.g., Disk 1):
```sh
select disk <disk_number>
```

- Check partitions on the selected disk:
```sh
list partition
```

- Select the partitio(System)n containing Ubuntu system files (e.g., Partition 1):
```sh
select partition 1
```

- Assign a drive letter (P:):
```sh
assign letter=P
```

### 0.3  Delete Ubuntu System Files
- Run Notepad as Administrator.
- Navigate to `P:` in File Explorer and delete the Ubuntu folder in EFI folder.

### 0.4. Remove Assigned Drive Letter
- Return to Diskpart and run the following command:
```sh
remove letter=P
```

### 0.5 Delete Ubuntu Partition Using Disk Management
- Open `Disk Management` (`Win + X` → `Disk Management`).
- Locate the partition containing Ubuntu.
- Right-click and select "Delete Volume".
- (**Optional**) Extend another partition into the unallocated space.

## 1. Install Ubuntu
- [Rufus](https://github.com/pbatard/rufus)
- [Ubuntu Download](https://jp.ubuntu.com/download)

## 2. Install Anaconda3
### 2.1 Download
[Anaconda3](https://www.anaconda.com/download#download-section)

```bash
cd Downloads
```

```bash
bash Anaconda3-xxxx.xx-Linux-x86_64.sh
```

### 2.2 bashrc setting
```bash
nano ~/.bashrc
```

- export

```bash
export PATH="/home/username/anaconda3/bin:$PATH"
```

```shell
source ~/.bashrc
```

---
### 2.3 Command
```bash
conda create -n your_env_name python=X.X（3.6,3.7,etc.）

source activate your_env_name(env_name)

source deactivate your_env_name(env_name)

conda remove -n your_env_name(env_name) --all

conda list

conda install package_name(package_name)
conda install scrapy==1.3 
conda install -n env_name package_name

#Check env
conda env list 
#or
conda info -e
#or
conda info --envs

conda update conda

conda update anaconda

conda update --all

conda update python
```

### 2.4 put images source

## 3.Install Pycharm
[Pycharm](https://www.jetbrains.com/pycharm/download/other.html)

## 4.ROS
- ROS2 - humble - ubuntu 22.04
- ROS1 - ubuntu 20.04

## 5.Install Nvidia driver
```bash
ubuntu-drivers devices
```

```bash
sudo apt-get install nvidia-driver-535
```

- hardware key

```bash
Enroll MOK 
```

- `reboot` and check

```bash
nvidia-smi
```

---
## 6.CUDA
[CUDA](https://developer.nvidia.com/cuda-toolkit-archive)

- `nano ~/.bashrc`
```shell
export PATH=/usr/local/cuda-12.3/bin${PATH:+:${PATH}}
export LD_LIBRARY_PATH=/usr/local/cuda-12.3/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
```

---
## 7.cuDNN
[cudnn](https://developer.nvidia.com/rdp/cudnn-archive)

- Extract

```bash
cd ~/Downloads/cudnn-xxxxxxxxxxxxx`
```

- move

```bash
sudo cp include/cudnn*.h /usr/local/cuda/include 
sudo cp -P lib/libcudnn* /usr/local/cuda/lib64 
sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
```

- Check

```bash
cat /usr/local/cuda-12.3/include/cudnn_version.h | grep CUDNN_MAJOR -A 2
```

---
## 8.Pytorch
https://pytorch.org/get-started/previous-versions/

- Check

```python
>>import torch

>>print(torch.__version__)

>>print(torch.version.cuda)

>>print(torch.backends.cudnn.version())

>>torch.cuda.is_available()

>>torch.cuda.device_count()

>>torch.cuda.get_device_name(0)

>>torch.cuda.current_device()
```

---
## 9.Docker 
>Do not install docker-desktop!

-  [Install using the apt repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

---