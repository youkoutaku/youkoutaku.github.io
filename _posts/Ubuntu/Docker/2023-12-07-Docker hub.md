---
title: Docker hub
date: 2023-12-07 16:06:03 +0900
categories:
  - Ubuntu
  - Docker
tags:
  - Ubuntu
  - Docker
author: Youkoutaku
math: true
mermaid: true
image:
  path: https://th.bing.com/th?id=OSK.HEROemvCqZG4t6CTfwiuQrQSn1CMVPnxWezRLUVR0MFHKZc&w=472&h=280&c=13&rs=2&o=6&pid=SANGAM
  alt:
---

## Docker login
```bash
docker login
```

### Error Notes
```
>> Error saving credentials: error storing credentials - err: exec: "docker-credential-desktop": executable file not found in $PATH, out: `` 
```

```
sudo nano ~/.docker/config.json
```
rename `credsStore` to `credStore`

### Use Access Tokens
https://hub.docker.com/
My Accont >> Security >> New Avvess Token

---
## Push images to Docker Hub
### Rename Container 
```
docker rename old_container_name_or_id new_container_name
```

### Make image from container 
```
docker commit -a="<usrname>" -m="<info>" <docker ID or name> <new image name>:<ver.>
```

### Rename image name
The naming of images needs to comply with `<username>/<repository>:<TAG>` in Dockerhub

```bash
docker tag <image ID> <username>/<repository>:<TAG>
```

### Push to dockerhub
```
docker push <username>/<repository>:<TAG>
```
