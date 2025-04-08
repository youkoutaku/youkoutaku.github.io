---
title: Sync Obsidian
date: 2024-08-31 20:00:00 +0900
categories:
  - Tech-Tips
tags:
  - Obsidian
author: Youkoutaku
math: true
mermaid: true
image: https://upload.wikimedia.org/wikipedia/commons/9/9c/Obsidian_desktop_demo_workflow.jpg
---

## Sync Obsidian on All Platforms (Windows, Mac, Android, iOS) and Multiple Devices

### 0. Requirements

- **Obsidian Plugin:**
  - [Remotely Sync](https://github.com/sboesen/remotely-sync)
- **Cloud Storage Service:**
  - WebDAV
  - Dropbox
  - OneDrive

### 1. Sync a New Vault

1. Install the Remotely Sync plugin to your vault.
2. Choose a remote service (WebDAV, Dropbox, OneDrive).

![Sync a New Vault](https://preview.redd.it/sycn-obsidian-v0-sbdpc4g4s1oe1.png?width=1021&format=png&auto=webp&s=1b47c91646201820db862d2063305aab5e3dd2c6)

### 2. Sync the Vault on Another Device

1. Create a new vault with the same name as the original vault.
2. Install the Remotely Sync plugin to the new vault.
3. Choose the same remote service used for the original vault.

---

## Additional Information

This method is easy for syncing Obsidian vaults across all platforms and multiple devices. However, it may not be stable for large vaults (over 300MB by experience).

- For Windows devices, OneDrive is the best way to sync.
- For Mac and iOS devices, iCloud is the best way to sync (also available on Windows).