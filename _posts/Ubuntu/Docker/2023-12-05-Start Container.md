---
title: Start Container by Docker
date: 2023-12-05 16:06:03 +0900
categories:
  - Ubuntu
  - Docker
tags:
  - Ubuntu
  - Docker
  - ROS
author: Youkoutaku
math: true
mermaid: true
---

## Initial setting
```
xhost +
```
>Setting to enable to  use vscode. rviz ... in docker

## Make Container 
```bash
sudo docker run -it -v <local path>:/data --device=/dev/dri --group-add video --volume=/tmp/.X11-unix:/tmp/.X11-unix  --env="DISPLAY=$DISPLAY" --env="QT_X11_NO_MITSHM=1" --name=<Container name> <Image name>:<Tag>  /bin/bash
```

### Check host-connect filer 
```
cd /data
```

---
## Get apt in container 
```
sudo apt update
sudo apt upgrade
```

### Tmux
```
apt install tmux & apt install git
```

### Git
```
apt install git
```

---
## Container 
### Start Container 
```
sudo docker start -ia <Docker ID>
```

### Check Running Container
```
docker ps
```

### Check all container  
```
docker ps -a
```

### Check images
```
docker images
```