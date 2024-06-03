---
title: Docker for ROS
date: 2023-12-20 15:06:03 +0900 #Tokyo
categories: [Ubuntu, Docker]
tags: [Docker, ROS, Ubuntu]
author: Youkoutaku
math: true #Mathematical
mermaid: true
#pin: 
#img_path: 
image:
  path: https://roboticseabass.com/wp-content/uploads/2021/04/docker_ros_banner-1.png
  alt: Docker + ROS (https://roboticseabass.com/)
---

## 1.Install Docker
[Install using the apt repository](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)

- Get apt

```bash
udo docker run -it -v /home/youkoutaku/noetic_data:/data --device=/dev/dri --group-add video --volume=/tmp/.X11-unix:/tmp/.X11-unix  --env="DISPLAY=$DISPLAY" --env="QT_X11_NO_MITSHM=1" --name=noetic_ros osrf/ros:noetic-desktop-full  /bin/bash
```

```bash
# Add Docker's official GPG key:
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg
```

```bash
# Add the repository to Apt sources:
echo \
  "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
  $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | \
  sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
sudo apt-get update
```

- Install

```bash
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin
```

- Test

```bash
sudo docker run hello-world
```

>Don't install docker-desktop!

##  2.ROS docker images
[ros - Official Image | Docker Hub](https://hub.docker.com/_/ros/)

`docker pull osrf/ros_legacy:<tag_name>`

- ROS1 - noetic

```bash
sudo docker pull osrf/ros:noetic-desktop-full
```
- ROS2 - humble

```bash
sudo docker pull osrf/ros:humble-desktop-full
```

---
## 3.Make Container for Docker
### 3.1 host open
Setting to enable to  use vscode. rviz ... in docker

```bash
xhost +
```
> run the code when restated the container 

### 3.2 make data folder
- make a folder to connect container.

`<data name>`

### 3.3 make container and connect to data folder
```bash
sudo docker run -it -v <host path>:<docker paht> --device=/dev/dri --group-add video --volume=/tmp/.X11-unix:/tmp/.X11-unix  --env="DISPLAY=$DISPLAY" --env="QT_X11_NO_MITSHM=1" --name=<container name> osrf/ros:noetic-desktop-full  /bin/bash
```
- `docker run` : make a container
- `-it`: use terminal
- `-v <host path>:<docker paht>`: make a **volume(data)** to connect between host to container
- `--device=/dev/dri`: connect device of host to container
- `--group-add video`: Add the container user to the "video" group to gain access to the graphics device.
- `--volume=/tmp/.X11-unix:/tmp/.X11-unix`: Maps the X11 Unix socket directory `/tmp/.X11-unix` on the host to the container, allowing the container to communicate with the X11 server, typically used for the display of graphical applications.
- `--env="DISPLAY=$DISPLAY"` : set display path of container to X11 path
- `--env="QT_X11_NO_MITSHM=1"`: set up the path of `QT_X11_NO_MITSHM=1` in container
- `--name=<container name>`: set up the name of container
- `osrf/ros:noetic-desktop-full`: using dokcer image of ROS
- `/bin/bash` : run bash in container

For ROS2 - humble
```bash
sudo docker run -it -v /home/youkoutaku/noetic_data:/data --device=/dev/dri --group-add video --volume=/tmp/.X11-unix:/tmp/.X11-unix  --env="DISPLAY=$DISPLAY" --env="QT_X11_NO_MITSHM=1" --name=humble_ros osrf/ros:humble-desktop-full  /bin/bash
```

## 4.ROS in Docker
### 4.2 Get apt in container 
```bash
sudo apt update
sudo apt upgrade
```

#### Tmux
```bash
apt install tmux & apt install git
```
[Tmux Getting-Started](https://github.com/tmux/tmux/wiki/Getting-Started)

#### Git
```bash
apt install git
```

### 4.3 ROS test
[ROS: Getting Started](https://www.ros.org/blog/getting-started/)

```bash
roscore
```

```bash
rviz
```

```bash
gazebo
```

---
## Reference
1. [Install Docker Engine on Ubuntu](https://docs.docker.com/engine/install/ubuntu/#install-using-the-repository)
2. [ros - Official Image](https://hub.docker.com/_/ros/)
3. [Loam：Ubuntu20.04下的编译与运行_for frame camera init to-CSDN博客](https://blog.csdn.net/weixin_44156680/article/details/117705830)
4. [Ubuntu20.04+Docker+ROS Noetic+LOAM环境配置 - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/552273212)
5. [Ubuntu 22.04 docker下运行ROS Melodic rviz - 知乎 (zhihu.com)](https://zhuanlan.zhihu.com/p/586391583)