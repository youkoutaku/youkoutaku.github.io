---
title: Start a Docker Container
date: 2023-12-05 16:06:03 +0900
categories:
  - Development
  - Docker
tags:
  - Ubuntu
  - Docker
  - ROS
  - WSL
author: Youkoutaku
math: true
mermaid: true
---

## Initial setting
```
xhost +
```
>Setting to enable to  use vscode. rviz ... in docker

## Create a Container
```bash
sudo docker run -it -v <local path>:/data \
	--device=/dev/dri \
	--group-add video \
	--volume=/tmp/.X11-unix:/tmp/.X11-unix \
	--env="DISPLAY=$DISPLAY" \
	--env="QT_X11_NO_MITSHM=1" \
	--name=<Container name> \
	<Image name>:<Tag>
	/bin/bash
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
apt install tmux
```

### Git
```
apt install git
```

---
## Run a Container
### Start Container
```
sudo docker start -ia <Container ID>
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