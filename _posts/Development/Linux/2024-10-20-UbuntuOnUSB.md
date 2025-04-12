---
title: Install Ubuntu on a USB drive
date: 2024-10-20 11:06:03 +0900
categories:
  - Development
  - Linux
tags:
  - Linux
  - Ubuntu
  - GRUB
author: Youkoutaku
math: true
mermaid: true
#pin:
#img_path:
image:
  path: https://res.cloudinary.com/canonical/image/fetch/f_auto,q_auto,fl_sanitize,w_1920,h_1080/https://assets.ubuntu.com/v1/d4ea8bb8-ubuntu-budgie-2410.png
  alt: Ubuntu
---

[Ubuntu](https://ubuntu.com/) is a popular [Linux](https://www.linux.org/) operating system, often used for embedded development. It's possible to install both Ubuntu and Windows OS on the same machine, with Ubuntu files stored on a USB drive, managed by the [GRUB](https://www.gnu.org/software/grub/) bootloader.

However, in the current setup, the GRUB system and Ubuntu’s EFI files are stored on the PC’s internal disk.

> Even when the USB drive is not plugged in, the GRUB system runs every time the computer restarts, allowing you to select the operating system.
{: .prompt-warning }

To avoid this, I want to install the GRUB bootloader on the USB drive itself. This way, the GRUB system will only run when the USB drive is plugged in.

## Install Ubuntu on USD drive
- [Install Ubuntu desk](https://ubuntu.com/tutorials/install-ubuntu-desktop#1-overview)

>When installing Ubuntu, make sure to **select the USB drive** as the target installation location for Ubuntu. Additionally, ensure that your BIOS settings are configured to boot the `Ubuntu GRUB`, rather than the `Windows Boot Manager`.
{: .prompt-info }

Setting the BIOS to boot from the `Ubuntu GRUB`. Now, we can select either Ubuntu or Windows when the PC restarts. But the Ubuntu’s EFI files are stored on the disk of Windows.

## Delete Ubutnu EFI on disk
The follwing steps do on WIndows.

- Run Diskpart
	- win+R: `diskpart`
- Check disk
	- `list disk`
- Select the disk saved EFI files of ubuntu.
	- `select disk 0`
- Check partition
	- `list  partition`
- Select the system partition saved EFI files. In the windows, it is often in partition 1
	- `select partition 1`
- Assign the partition to `p`
	- `assign letter=p`
- Keep the running Diskpart
- Open `notepad` as administrator
	- Open folder on notepad
	- Go to the disk `p` and delete the ubuntu folder in EFI
- Back to the Diskpart
	- `romove letter=p`

Because we deleted `Ubuntu's EFI` files, there is no `Ubuntu GRUB` in the BIOS. When we restart the PC, it should boot to the `Windows Boot Manager` and run Windows.

## Run Linux on USB drive
There are `Windows Boot Manager` and USB drive in the BIOS setting.

### Check the linux boot files
- Set the BIOS to boot the USB drive
	- We get the `GRUB` system. Because there is no `Ubuntu EFI`, it cannot run Ubuntu automatically
- We need to set the Ubuntu in `GRUB` by command
- `grub> ls` can show the disk list
	- There are the disks and parts.
	- `(hd0), (hd0, gpt1), (hd0, gp2)`
- `grub> ls (hd0,gpt2)/boot`. There are `vmlinuz` and `initrd` in the `/boot`.

### Boot linux
Set the path of `vmlinuz` :
```bash
grub> set root=(hd0,gpt2)
grub> linux /boot/vmlinuz-4.15.0-20-generic root=/dev/sda2
```

> Notice the name of `vmlinuz`.
> `/dev/sda2` is the diskpart in the Linux Identifier. `(hd0,gpt2)` is the diskparts in the GRUB Identifier
{: .prompt-info }

Set the path of `initrd` :
```bash
grub> initrd /boot/initrd.img-4.15.0-20-generic
```

Boot the Linux :
```bash
grub> boot
```

Then, We run the Ubuntu by `GRUB`.

## Set GRUB on USB drive

Update `GRUB` :
```bash
sudo update-grub
```

Set `Ubuntu's EFI` in USB drive:
```bash
sudo mount /dev/sdX1 /mnt  # mount the EFI diskpart in USB drive
sudo mount /dev/sdXY /mnt/boot  # mount the Ubuntu root
sudo grub-install --boot-directory=/mnt/boot --efi-directory=/mnt --removable
sudo update-grub
```

Restart the PC and check if the `Ubuntu GRUB` exists in the BIOS. Now, we can select to boot the `Ubuntu GRUB` from the USB drive. Furthermore, when the USB drive is not plugged in, it will boot to the `Windows Boot Manager` and run Windows.