---
title: Delete Ubuntu System on Windows
date: 2023-12-04 13:0:00 +0900
categories:
  - Development
  - Linux
tags:
  - Linux
  - Ubuntu
author: Youkoutaku
---

## **How to Remove Ubuntu System Files and Partitions on Windows**

### **1. Open Diskpart**
- Press `Win + R`, type `diskpart`, and press `Enter`.

### **2. Run Commands in Diskpart**
- **Check available disks**:
```sh
  list disk
```

- **Select the disk where Ubuntu is installed** (e.g., Disk 1):
```sh
select disk <disk_number>
```

- **Check partitions on the selected disk**:
```sh
list partition
```

- **Select the partitio(System)n containing Ubuntu system files** (e.g., Partition 1):
```sh
select partition 1
```

- **Assign a drive letter (P:)**:
```sh
assign letter=P
```

### **3. Delete Ubuntu System Files**
- **Run Notepad as Administrator**.
- **Navigate to `P:` in File Explorer and delete the Ubuntu folder in EFI folder**.

### **4. Remove Assigned Drive Letter**
- **Return to Diskpart and execute the following command**:
```sh
remove letter=P
```

### **5. Delete Ubuntu Partition Using Disk Management**
- Open `Disk Management` (`Win + X` → `Disk Management`).
- Locate the partition containing Ubuntu.
- **Right-click and select "Delete Volume"**.
- (**Optional**) Extend another partition into the unallocated space.

---

This completes the removal of Ubuntu system files and partitions!
