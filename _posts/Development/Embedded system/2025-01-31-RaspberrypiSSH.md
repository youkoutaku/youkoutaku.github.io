---
title: How to Set Up VSCode for Remote Development on Raspberry Pi via SSH
date: 2025-01-31 17:03:00 +0900
categories: [Development]
tags: [VSCode, SSH, Raspberry Pi]
author: Youkoutaku
math: true
image:
  path: https://preview.redd.it/i-just-set-up-visual-studio-code-for-remote-development-on-v0-ieh7oacw9bge1.png?width=640&crop=smart&auto=webp&s=93192dc0f9df66059316a00358c91303cde7c64a
  alt: VSCode on Raspberry
---


### 1. **Install VSCode and Remote Development Extensions**

- Ensure you have Visual Studio Code installed on your local machine. If not, you can download it from [here](https://code.visualstudio.com/).
- Install the **Remote - SSH** extension in VSCode. You can do this by:
    1. Opening VSCode.
    2. Clicking on the **Extensions** view icon on the sidebar.
    3. Searching for **Remote - SSH** and installing it.

---
### 2. **Enable SSH on Your Raspberry Pi**
- SSH should be enabled on your Raspberry Pi by default. If it's not, enable SSH using the following steps:
    1. Open a terminal on your Raspberry Pi.
    2. Run the command:
        `sudo raspi-config`
    3. Navigate to **Interfacing Options** > **SSH** > **Enable**.

---
### 3. **Find Your Raspberry Pi's IP Address**
- On your Raspberry Pi, run the following command to get the IP address:
    `hostname -I`
- Take note of the IP address shown (e.g., `192.168.x.x`).

---
### 4. **Configure SSH on Your Local Machine**
- If you haven’t already, generate an SSH key pair on your local machine:
    1. Open a terminal/command prompt on your local machine.
    2. Run on Local Machine(PC) :
        `ssh-keygen`
	3. Terminal:
		1. `Enter file in which to save the key:` Enter(default)
	    2. `Enter passphrase (empty for no passphrase):` Enter
	    3. `Enter same passphrase again:` Enter
    4. Follow the prompts to generate the key. The default location for storing the key is fine
	    1. Linux: usually `~/.ssh/<id_name>`
	    2. Windows: usually `C:\Users\<username>\.ssh\<ir_name>`
- (Linux): Copy your SSH public key to the Raspberry Pi.
    1. Run:
        `ssh-copy-id <raspberry_pi_username>@<raspberry_pi_ip>`
    2. Replace `<raspberry_pi_ip>` with the IP address of your Raspberry Pi.
    3. Enter the password when prompted.
- (Windows): There is no command to copy the public key directly.
	1. Open the public key and copy it on PC terminal
	2. Run: Show the public key
		`type C:\Users\<Username>\.ssh\<id_name>.pub`
	3. Access Raspberry Pi's SSH :
		`ssh <raspberry_pi_username>@<raspberry_pi_ip>`
	4. Create `.ssh` Folder on Raspberry Pi and paste the public key

```shell
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

> `authorized_keys` is a fixed filename. If you have many keys, please save all of them in this file.
{: .prompt-warning }

- Test SSH Access:
	- Able to SSH into your Raspberry Pi from your machine without a password:
		`ssh <raspberry_pi_username>@<raspberry_pi_ip>

---
### 5. **Configure VSCode to Connect via SSH**

- Open VSCode.
- Press `Ctrl + Shift + P` to open the Command Palette.
- Type and select **Remote-SSH: Connect to Host...**.
- Enter the following: `<raspberry_pi_username>@<raspberry_pi_ip>`
- Replace `<raspberry_pi_ip>` with the IP address of your Raspberry Pi.

---
### Optional: **Save Connection**

- You can save this SSH connection for easier access in the future:
    1. Go to **File** > **Preferences** > **Settings**.
    2. Search for **Remote.SSH: Config File**.
    3. Edit the (Linux):`~/.ssh/config` or (Windows): `C:\Users\<username>\.ssh\config` file to add config:

```
# For Linux
Host raspberry_pi
	HostName <raspberry_pi_ip>
	User <raspberry_pi_username>
	IdentityFile ~/.ssh/id_rsa`
```

```
# For Windows
Host raspberry_pi
	HostName <raspberry_pi_ip>
	User <raspberry_pi_username>
	IdentityFile C:\Users\<username>\.ssh\config
```

- This way, you can simply type `raspberry_pi` in the **Remote-SSH: Connect to Host** command.

---
### 6. **Start Editing on Raspberry Pi**

![](https://preview.redd.it/i-just-set-up-visual-studio-code-for-remote-development-on-v0-ieh7oacw9bge1.png?width=640&crop=smart&auto=webp&s=93192dc0f9df66059316a00358c91303cde7c64a)
