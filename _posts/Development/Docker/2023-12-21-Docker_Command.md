---
title: Docker Command Reference
date: 2023-12-21 12:06:03 +0900 # Tokyo
categories:
  - Development
  - Docker
tags: [Docker, ROS, Ubuntu, WSL]
author: Youkoutaku
math: true # Mathematical
mermaid: true
#pin: 
#img_path: 
---

# Docker Command Reference

---

## Check Docker Version
To verify the installed Docker version:
```bash
docker version
```

---

## Create a Container
To create and run a container from an image:
```bash
docker run <IMAGE ID>
```
- Replace `<IMAGE ID>` with the ID of the image you want to use.

---

## Start a Container
To start an existing container interactively:
```bash
sudo docker start -ia <CONTAINER ID>
```
- Replace `<CONTAINER ID>` with the ID of the container you want to start.

---

## Check Containers
- **List running containers**:
  ```bash
  docker ps
  ```

- **List all containers (including stopped ones)**:
  ```bash
  docker ps -a
  ```

---

## Check Images
To list all available Docker images on your system:
```bash
docker images
```

---

## Pull an Image from Docker Hub
To download an image from Docker Hub:
```bash
docker pull <IMAGE NAME>
```
- Replace `<IMAGE NAME>` with the name of the image you want to pull.

---

## Push Images to Docker Hub

### Rename a Container
To rename an existing container:
```bash
docker rename <OLD_CONTAINER_NAME_OR_ID> <NEW_CONTAINER_NAME>
```
- Replace `<OLD_CONTAINER_NAME_OR_ID>` with the current name or ID of the container.
- Replace `<NEW_CONTAINER_NAME>` with the desired new name.

### Create an Image from a Container
To create a new image from an existing container:
```bash
docker commit -a="<AUTHOR>" -m="<MESSAGE>" <CONTAINER_ID_OR_NAME> <NEW_IMAGE_NAME>:<VERSION>
```
- Replace `<AUTHOR>` with your name or username.
- Replace `<MESSAGE>` with a description of the changes.
- Replace `<CONTAINER_ID_OR_NAME>` with the container's ID or name.
- Replace `<NEW_IMAGE_NAME>:<VERSION>` with the desired name and version for the new image.

### Rename an Image
To rename or tag the created image for Docker Hub:
```bash
docker tag <IMAGE ID> <USERNAME>/<REPOSITORY>:<TAG>
```
- Replace `<IMAGE ID>` with the ID of the image.
- Replace `<USERNAME>/<REPOSITORY>:<TAG>` with the desired naming convention for Docker Hub.

### Push an Image to Docker Hub
To upload the created image to Docker Hub:
```bash
docker push <USERNAME>/<REPOSITORY>:<TAG>
```
- Replace `<USERNAME>/<REPOSITORY>:<TAG>` with the name of the image you want to push.

---

## Build an Image by Dockerfile
To build a Docker image from a Dockerfile:
```bash
docker build -t <IMAGE NAME> <PATH>
```
- Replace `<IMAGE NAME>` with the desired name for your image.
- Replace `<PATH>` with the directory containing your Dockerfile.

---

## Stop a Container
To stop a running container:
```bash
docker stop <CONTAINER ID>
```
- Replace `<CONTAINER ID>` with the ID of the container you want to stop.

---

## Delete a Container
To remove a container:
```bash
docker rm <CONTAINER ID>
```
- Replace `<CONTAINER ID>` with the ID of the container you want to delete.

---

## Delete an Image
To remove an image:
```bash
docker rmi <IMAGE ID>
```
- Replace `<IMAGE ID>` with the ID of the image you want to delete.

---

## Additional Notes
- Replace `<IMAGE ID>` or `<CONTAINER ID>` with the respective IDs obtained from `docker images` or `docker ps`.
- Use `sudo` if required, depending on your system's permissions.
- Ensure you are logged in to Docker Hub before pushing images using `docker login`.

---

This guide is a concise reference for Docker commands. For more detailed usage, refer to the [Docker documentation](https://docs.docker.com/).
