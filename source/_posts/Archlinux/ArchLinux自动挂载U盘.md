---
title: ArchLinux自动挂载U盘
date: 2023-03-23 10:34:47
id: 83
tags: 
  - Archlinux
  - U盘
categories: Archlinux
copyright: true
---

# ArchLinux自动挂载U盘

在Arch Linux中，您可以通过以下步骤来自动挂载USB设备：

1. 确保您的系统上安装了`udisks2`软件包。您可以使用以下命令来安装该软件包：

   ```
   sudo pacman -S udisks2
   ```

2. 为了能够自动挂载USB设备，您需要启用`udisks2`服务。您可以使用以下命令启用该服务：

   ```bash
   sudo systemctl enable udisks2.service
   ```

3. 然后，您需要将您的用户添加到`storage`组中，以便您可以访问挂载点。您可以使用以下命令将您的用户添加到`storage`组中：

   ```css
   sudo gpasswd -a yourusername storage
   ```

4. 最后，您可以将以下行添加到`/etc/fstab`文件中，以便自动挂载USB设备：

   ```ini
   UUID=XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX /mnt/usb auto nosuid,nodev,nofail,x-gvfs-show 0 0
   ```

   您需要将`XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX`替换为您的USB设备的UUID，并将`/mnt/usb`替换为您希望将USB设备挂载到的挂载点。

   您可以使用以下命令查找USB设备的UUID：

   ```
   lsblk -f
   ```

   找到您的USB设备，然后在`UUID`列中查找UUID。

这样，每次插入USB设备时，它都会自动挂载到指定的挂载点。请注意，在第一次插入USB设备时，系统可能需要一些时间来创建挂载点。