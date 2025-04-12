---
title: How to Set Up VSCode for Remote Development on Ubuntu via SSH
date: 2025-04-11 16:08:00 +0900
categories:
  - Development
  - Linux
tags:
  - Linux
  - SSH
  - Ubuntu
  - VSCode
author: Youkoutaku
math: true
mermaid: true
---

This guide explains how to set up Visual Studio Code (VSCode) for remote development on an Ubuntu machine using SSH.

---
### 1. Install VSCode and Remote Development Extensions

- Ensure you have Visual Studio Code installed on your local machine. If not, you can download it from [here](https://code.visualstudio.com/).
- Install the **Remote - SSH** extension in VSCode. You can do this by:
  1. Opening VSCode.
  2. Clicking on the **Extensions** view icon on the sidebar.
  3. Searching for **Remote - SSH** and installing it.

---
### 2. Enable SSH on Ubuntu

1. Open a terminal on your Ubuntu machine.
2. Install the SSH server by running the following commands:
  ```bash
  sudo apt update
  sudo apt install openssh-server
  ```
3. Verify that the SSH service is running:
  ```bash
  sudo systemctl status ssh
  ```

---
### 3. Find Your Ubuntu machine's IP Address

1. On Ubuntu, run the following command to get the IP address: `ip a`
2. Note the IP address shown (e.g., `192.168.x.x`).

---
### 4. Configure SSH on Your Local Machine

#### A. Generate an SSH Key Pair (Local Machine)

1. Open a terminal or command prompt on your local machine.
2. Run the following command to generate an SSH key pair:
  ```bash
  ssh-keygen
  ```
3. Follow the prompts:
  - Press **Enter**:
    - `Enter file in which to save the key:` Enter(default)
    - `Enter passphrase (empty for no passphrase):` Enter
    - `Enter same passphrase again:` Enter
  - The key will be saved in:
    - **Linux**: `~/.ssh/<id_name>`
    - **Windows**: `C:\Users\<username>\.ssh\<id_name>`

#### B. Copy the SSH Public Key to Ubuntu

- Linux:
  1. Run the following command to copy your public key to the Ubuntu machine:
    ```bash
    ssh-copy-id <ubuntu_user_name>@<ubuntu_ip>
    ```
  2. Replace `<ubuntu_username>` and `<ubuntu_ip>` with your Ubuntu username and IP address.
  3. Enter the password when prompted.
- Windows:
	1. Display your public key:
    ```bash
    type C:\Users\<Username>\.ssh\<id_name>.pub
    ```
	2. Connect to the Ubuntu machine via SSH:
    ```bash
    ssh <ubuntu_username>@<ubuntu_ip>
    ```
	3. Create the `.ssh` directory and add the public key:
	```bash
  mkdir -p ~/.ssh
  nano ~/.ssh/authorized_keys
  ```
  Paste the public key into the `authorized_keys` file. Save and exit.

> `authorized_keys` is a fixed filename. If you have many keys, please save all of them in this file.
{: .prompt-warning }

#### C. Test SSH Access:

- Verify that you can SSH into the Ubuntu machine without a password:
  ```bash
  ssh <ubuntu_username>@<ubuntu_ip>
  ```

---
### 5. Configure VSCode to Connect via SSH

- Open VSCode.
- Press `Ctrl + Shift + P` to open the Command Palette.
- Type and select **Remote-SSH: Connect to Host...**.
- Enter the following:
  ```bash
  <ubuntu_username>@<ubuntu_ip>
  ```
- Replace `<ubuntu_username>` and `<ubuntu_ip>` with your Ubuntu username and IP address.

---
### Optional A: Save Connection

- You can save this SSH connection for easier access in the future:
  1. Go to **File** > **Preferences** > **Settings**.
  2. Search for **Remote.SSH: Config File**.
    1. Linux:`~/.ssh/config`
    2. Windows: `C:\Users\<username>\.ssh\config`
  ```
  # For Linux
  Host Ubuntu
	  HostName <ubuntu_ip>
	  User <ubuntu_username>
	  IdentityFile ~/.ssh/<id_name>`
  ```
  ```
  # For Windows
  Host Ubuntu
	  HostName <ubuntu_ip>
	  User  <ubuntu_username>
	  IdentityFile C:\Users\<username>\.ssh\config
  ```

This way, you can simply type `Ubuntu` in the **Remote-SSH: Connect to Host** command.

---
### Optional B: Fix "Connection Refused" Errors

The "Connection Refused" error maybe show up when trying to SSH into the server. When the connecting is cut down while, the SSH service will down automatically.

1. Edit the SHH service setting `/etc/ssh/sshd_config`:
  ```bash
  sudo nano /etc/ssh/sshd_config
  ```
2. Update the following settings:
  ```bash
  ClientAliveInterval 60
  ClientAliveCountMax 3
  ```
3. Restart the SSH service:
  ```bash
  sudo systemctl restart sshd
  ```

---
### 6. **Start Editing on Ubuntu**

You are now ready to use Visual Studio Code for remote development on your Ubuntu machine!