---
title: Docker without sudo
date: 2023-12-10 18:00:00 +0900 # Tokyo
categories:
  - Development
  - Docker
tags: [Docker]
author: Youkoutaku
math: true # Mathematical
mermaid: true
---

## Steps to Enable Docker Without `sudo`

Follow these steps to configure Docker for non-root usage:

### 1. Create the `docker` Group
The `docker` group allows users to execute Docker commands without requiring root privileges. Run the following command to create the group:

```bash
sudo groupadd docker
```

If the group already exists, this command will have no effect.

### 2. Add Your User to the `docker` Group
Add your current user to the `docker` group to grant the necessary permissions:

```bash
sudo gpasswd -a $USER docker
```

Replace `$USER` with your username if you're not running this command as the logged-in user.

### 3. Apply the Group Changes
To apply the group changes without logging out and back in, use the `newgrp` command:

```bash
newgrp docker
```

This command switches your session to the updated group.

### 4. Test Docker Without `sudo`
Verify that Docker works without `sudo` by running the following command:

```bash
docker run hello-world
```

If everything is configured correctly, you should see the "Hello from Docker!" message.

---

For more details, refer to the official Docker documentation or the [Ask Ubuntu thread](https://askubuntu.com/questions/477551/how-can-i-use-docker-without-sudo).
