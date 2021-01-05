---
title: 调整具有 GPT 分区的 OS 磁盘的大小
description: 本文提供了有关如何调整 Linux 中具有 GUID 分区表 (GPT) 分区的 OS 磁盘大小的说明。
services: virtual-machines-linux
documentationcenter: ''
author: kailashmsft
manager: dcscontentpm
editor: ''
tags: ''
ms.service: security
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.devlang: azurecli
ms.date: 05/03/2020
ms.author: kaib
ms.custom: seodec18
ms.openlocfilehash: ab83a3b11aebdc9fed450410aa1f9bee2d25c4bb
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900665"
---
# <a name="resize-an-os-disk-that-has-a-gpt-partition"></a>调整具有 GPT 分区的 OS 磁盘的大小

> [!NOTE]
> 本文仅适用于具有 GUID 分区表 (GPT) 分区的 OS 磁盘。

本文介绍如何在 Linux 中增加具有 GPT 分区的 OS 磁盘的大小。 

## <a name="identify-whether-the-os-disk-has-an-mbr-or-gpt-partition"></a>确定 OS 磁盘是否具有 MBR 或 GPT 分区

使用 `parted` 命令确定创建的磁盘分区是否具有主启动记录 (MBR) 分区或 GPT 分区。

### <a name="mbr-partition"></a>MBR 分区

在下面的输出中，分区表显示了一个 msdos 值 。 此值标识 MBR 分区。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 107GB
Sector size (logical/physical): 512B/512B
Partition Table: msdos
Number  Start   End     Size    Type     File system  Flags
1       1049kB  525MB   524MB   primary  ext4         boot
2       525MB   34.4GB  33.8GB  primary  ext4
[user@myvm ~]#
```

### <a name="gpt-partition"></a>GPT 分区

在下面的输出中，分区表显示了一个 gpt 值 。 此值标识 GPT 分区。

```
[user@myvm ~]# parted -l /dev/sda
Model: Msft Virtual Disk (scsi)
Disk /dev/sda: 68.7GB
Sector size (logical/physical): 512B/512B
Partition Table: gpt
Disk Flags:

Number  Start   End     Size    File system  Name                  Flags
1       1049kB  525MB   524MB   fat16        EFI System Partition  boot
2       525MB   1050MB  524MB   xfs
3       1050MB  1052MB  2097kB                                     bios_grub
4       1052MB  68.7GB  67.7GB                                     lvm
```

如果虚拟机 (VM) 在 OS 磁盘上具有 GPT 分区，请增加 OS 磁盘的大小。

## <a name="increase-the-size-of-the-os-disk"></a>增加 OS 磁盘的大小

以下说明适用于 Linux 支持的分发版。

> [!NOTE]
> 在继续操作之前，为 VM 创建备份副本，或者拍摄 OS 磁盘的快照。

### <a name="ubuntu"></a>Ubuntu

在 Ubuntu 16.x 和 18.x 中增加 OS 磁盘的大小： 

1. 停止 VM。
1. 从门户增加 OS 磁盘的大小。
1. 重启 VM，然后以根用户身份登录到 VM。
1. 验证 OS 磁盘现在是否显示增加后的文件系统大小。

如以下示例中所示，已通过门户将 OS 磁盘的大小调整为 100 GB。 / 上装载的 /dev/sda1 文件系统现在显示为 97 GB 。

```
user@myvm:~# df -Th
Filesystem     Type      Size  Used Avail Use% Mounted on
udev           devtmpfs  314M     0  314M   0% /dev
tmpfs          tmpfs      65M  2.3M   63M   4% /run
/dev/sda1      ext4       97G  1.8G   95G   2% /
tmpfs          tmpfs     324M     0  324M   0% /dev/shm
tmpfs          tmpfs     5.0M     0  5.0M   0% /run/lock
tmpfs          tmpfs     324M     0  324M   0% /sys/fs/cgroup
/dev/sda15     vfat      105M  3.6M  101M   4% /boot/efi
/dev/sdb1      ext4       20G   44M   19G   1% /mnt
tmpfs          tmpfs      65M     0   65M   0% /run/user/1000
user@myvm:~#
```

### <a name="suse"></a>SUSE

增加 SUSE 12 SP4、SUSE SLES 12 for SAP、SUSE SLES 15 和 SUSE SLES 15 for SAP 中的 OS 磁盘大小：

1. 停止 VM。
1. 从门户增加 OS 磁盘的大小。
1. 重启 VM。

重启 VM 后，完成以下步骤：

1. 使用以下命令，以根用户身份访问 VM：

   ```
   # sudo -i
   ```

1. 使用以下命令来安装 growpart 包，该包将用于调整分区的大小：

   ```
   # zypper install growpart
   ```

1. 使用 `lsblk` 命令查找安装在文件系统根目录 ("/") 上的分区。 在这种情况下，我们看到设备 sda 的分区 4 安装在  / 上：

   ```
   # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 28.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. 通过使用上一步中确定的分区号，使用 `growpart` 命令调整所需分区的大小。

   ```
   # growpart /dev/sda 4
   CHANGED: partition=4 start=3151872 old: size=59762655 end=62914527 new: size=97511391 end=100663263
   ```

1. 再次运行 `lsblk` 命令以检查是否已增加分区大小。

   下面的输出显示已将 /dev/sda4 分区的大小调整为 46.5 GB：
   
   ```
   linux:~ # lsblk
   NAME   MAJ:MIN RM  SIZE RO TYPE MOUNTPOINT
   sda      8:0    0   48G  0 disk
   ├─sda1   8:1    0    2M  0 part
   ├─sda2   8:2    0  512M  0 part /boot/efi
   ├─sda3   8:3    0    1G  0 part /boot
   └─sda4   8:4    0 46.5G  0 part /
   sdb      8:16   0    4G  0 disk
   └─sdb1   8:17   0    4G  0 part /mnt/resource
   ```

1. 使用具有 `-f` 标记的 `lsblk` 命令确定 OS 磁盘上的文件系统类型：

   ```
   linux:~ # lsblk -f
   NAME   FSTYPE LABEL UUID                                 MOUNTPOINT
   sda
   ├─sda1
   ├─sda2 vfat   EFI   AC67-D22D                            /boot/efi
   ├─sda3 xfs    BOOT  5731a128-db36-4899-b3d2-eb5ae8126188 /boot
   └─sda4 xfs    ROOT  70f83359-c7f2-4409-bba5-37b07534af96 /
   sdb
   └─sdb1 ext4         8c4ca904-cd93-4939-b240-fb45401e2ec6 /mnt/resource
   ```

1. 根据文件系统类型，使用相应的命令调整文件系统的大小。
   
   对于 xfs，请使用以下命令：
   
   ```
   #xfs_growfs /
   ```
   
   示例输出：
   
   ```
   linux:~ # xfs_growfs /
   meta-data=/dev/sda4              isize=512    agcount=4, agsize=1867583 blks
            =                       sectsz=512   attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0 rmapbt=0
            =                       reflink=0
   data     =                       bsize=4096   blocks=7470331, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=3647, version=2
            =                       sectsz=512   sunit=0 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 7470331 to 12188923
   ```
   
   对于 ext4，请使用以下命令：
   
   ```
   #resize2fs /dev/sda4
   ```
   
1. 使用以下命令验证增加后的用于 df -Th 的文件系统的大小：
   
   ```
   #df -Thl
   ```
   
   示例输出：
   
   ```
   linux:~ # df -Thl
   Filesystem     Type      Size  Used Avail Use% Mounted on
   devtmpfs       devtmpfs  445M  4.0K  445M   1% /dev
   tmpfs          tmpfs     458M     0  458M   0% /dev/shm
   tmpfs          tmpfs     458M   14M  445M   3% /run
   tmpfs          tmpfs     458M     0  458M   0% /sys/fs/cgroup
   /dev/sda4      xfs        47G  2.2G   45G   5% /
   /dev/sda3      xfs      1014M   86M  929M   9% /boot
   /dev/sda2      vfat      512M  1.1M  511M   1% /boot/efi
   /dev/sdb1      ext4      3.9G   16M  3.7G   1% /mnt/resource
   tmpfs          tmpfs      92M     0   92M   0% /run/user/1000
   tmpfs          tmpfs      92M     0   92M   0% /run/user/490
   ```
   
   在前面的示例中，可以看到 OS 磁盘的文件系统大小已增加。

### <a name="rhel-with-lvm"></a>带有 LVM 的 RHEL

1. 使用以下命令，以根用户身份访问 VM：

   ```bash
   [root@dd-rhel7vm ~]# sudo -i
   ```

1. 使用 `lsblk` 命令确定在文件系统的根目录(/) 上装载的逻辑卷 (LV)。 在本例中，我们会看到在  / 上装载了 rootvg-rootlv。 如果需要其他文件系统，请替换本文中的 LV 和装入点。

   ```shell
   [root@dd-rhel7vm ~]# lsblk -f
   NAME                  FSTYPE      LABEL   UUID                                   MOUNTPOINT
   fd0
   sda
   ├─sda1                vfat                C13D-C339                              /boot/efi
   ├─sda2                xfs                 8cc4c23c-fa7b-4a4d-bba8-4108b7ac0135   /boot
   ├─sda3
   └─sda4                LVM2_member         zx0Lio-2YsN-ukmz-BvAY-LCKb-kRU0-ReRBzh
      ├─rootvg-tmplv      xfs                 174c3c3a-9e65-409a-af59-5204a5c00550   /tmp
      ├─rootvg-usrlv      xfs                 a48dbaac-75d4-4cf6-a5e6-dcd3ffed9af1   /usr
      ├─rootvg-optlv      xfs                 85fe8660-9acb-48b8-98aa-bf16f14b9587   /opt
      ├─rootvg-homelv     xfs                 b22432b1-c905-492b-a27f-199c1a6497e7   /home
      ├─rootvg-varlv      xfs                 24ad0b4e-1b6b-45e7-9605-8aca02d20d22   /var
      └─rootvg-rootlv     xfs                 4f3e6f40-61bf-4866-a7ae-5c6a94675193   /
   ```

1. 检查 LVM 卷组 (VG) 中是否有包含根分区的可用空间。 如果有可用空间，请跳到步骤 12。

   ```bash
   [root@dd-rhel7vm ~]# vgdisplay rootvg
   --- Volume group ---
   VG Name               rootvg
   System ID
   Format                lvm2
   Metadata Areas        1
   Metadata Sequence No  7
   VG Access             read/write
   VG Status             resizable
   MAX LV                0
   Cur LV                6
   Open LV               6
   Max PV                0
   Cur PV                1
   Act PV                1
   VG Size               <63.02 GiB
   PE Size               4.00 MiB
   Total PE              16132
   Alloc PE / Size       6400 / 25.00 GiB
   Free  PE / Size       9732 / <38.02 GiB
   VG UUID               lPUfnV-3aYT-zDJJ-JaPX-L2d7-n8sL-A9AgJb
   ```

   在此示例中，Free  PE / Size 一行显示卷组中有 38.02 GB 的可用空间。 在将空间添加到卷组之前，无需调整磁盘大小。

1. 在带有 LVM 的 RHEL 7.x 中增加 OS 磁盘的大小：

   1. 停止 VM。
   1. 从门户增加 OS 磁盘的大小。
   1. 启动 VM。

1. VM 重新启动后，请完成以下步骤：

   - 安装 **utils-growpart** 包以提供 **growpart** 命令，该命令是增加 OS 磁盘大小和 GPT 磁盘布局的 gdisk 处理程序所必需的。 这些包预装在大多数 marketplace 映像上。

   ```bash
   [root@dd-rhel7vm ~]# yum install cloud-utils-growpart gdisk
   ```

1. 通过使用 `pvscan` 命令，确定名为 rootvg 的卷组中哪个磁盘和分区包含 LVM 物理卷 (PV)。 请注意括号（“[”和“]”）之间列出的大小和可用空间。 

   ```bash
   [root@dd-rhel7vm ~]# pvscan
     PV /dev/sda4   VG rootvg          lvm2 [<63.02 GiB / <38.02 GiB free]
   ```

1. 使用 `lsblk` 验证分区大小。 

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  63G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 使用 `growpart`、设备名称和分区号扩展包含此 PV 的分区。 这样做将扩展指定分区以使用设备上的所有可用连续空间。

   ```bash
   [root@dd-rhel7vm ~]# growpart /dev/sda 4
   CHANGED: partition=4 start=2054144 old: size=132161536 end=134215680 new: size=199272414 end=201326558
   ```

1. 再次使用 `lsblk` 命令验证分区是否已调整到预期大小。 请注意，在此示例中，sda4 已从 63 GB 更改为 95 GB。

   ```bash
   [root@dd-rhel7vm ~]# lsblk /dev/sda4
   NAME            MAJ:MIN RM SIZE RO TYPE MOUNTPOINT
   sda4              8:4    0  95G  0 part
   ├─rootvg-tmplv  253:1    0   2G  0 lvm  /tmp
   ├─rootvg-usrlv  253:2    0  10G  0 lvm  /usr
   ├─rootvg-optlv  253:3    0   2G  0 lvm  /opt
   ├─rootvg-homelv 253:4    0   1G  0 lvm  /home
   ├─rootvg-varlv  253:5    0   8G  0 lvm  /var
   └─rootvg-rootlv 253:6    0   2G  0 lvm  /
   ```

1. 扩展 PV 以使用新扩展的分区的其余部分：

   ```bash
   [root@dd-rhel7vm ~]# pvresize /dev/sda4
   Physical volume "/dev/sda4" changed
   1 physical volume(s) resized or updated / 0 physical volume(s) not resized
   ```

1. 验证 PV 的新大小是否为预期大小，并将其与原始 [size / free] 值进行比较：

   ```bash
   [root@dd-rhel7vm ~]# pvscan
   PV /dev/sda4   VG rootvg          lvm2 [<95.02 GiB / <70.02 GiB free]
   ```

1. 将所需逻辑卷 (LV) 扩展到所需量。 这个量不一定要是卷组中的所有可用空间。 在下面的示例中，将 /dev/mapper/rootvg-rootlv 的大小从 2 GB 调整到了 12 GB（增加了 10 GB）。 此命令还将调整文件系统的大小。

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   ```

   示例输出：

   ```bash
   [root@dd-rhel7vm ~]# lvresize -r -L +10G /dev/mapper/rootvg-rootlv
   Size of logical volume rootvg/rootlv changed from 2.00 GiB (512 extents) to 12.00 GiB (3072 extents).
   Logical volume rootvg/rootlv successfully resized.
   meta-data=/dev/mapper/rootvg-rootlv isize=512    agcount=4, agsize=131072 blks
            =                       sectsz=4096  attr=2, projid32bit=1
            =                       crc=1        finobt=0 spinodes=0
   data     =                       bsize=4096   blocks=524288, imaxpct=25
            =                       sunit=0      swidth=0 blks
   naming   =version 2              bsize=4096   ascii-ci=0 ftype=1
   log      =internal               bsize=4096   blocks=2560, version=2
            =                       sectsz=4096  sunit=1 blks, lazy-count=1
   realtime =none                   extsz=4096   blocks=0, rtextents=0
   data blocks changed from 524288 to 3145728
   ```

1. `lvresize` 命令将为 LV 中的文件系统自动调用相应的重设大小命令。 使用以下命令，检查装载在  / 上的 /dev/mapper/rootvg-rootlv 的文件系统大小是否已增加：

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   ```

   示例输出：

   ```shell
   [root@dd-rhel7vm ~]# df -Th /
   Filesystem                Type  Size  Used Avail Use% Mounted on
   /dev/mapper/rootvg-rootlv xfs    12G   71M   12G   1% /
   [root@dd-rhel7vm ~]#
   ```

> [!NOTE]
> 若要使用相同的过程来调整任何其他逻辑卷的大小，请更改步骤 12 中的 LV 名称。

### <a name="rhel-raw"></a>RHEL RAW
>[!NOTE] 
>增加 OS 磁盘大小前，请始终拍摄 VM 的快照。

在 RHEL RAW 中增加 OS 磁盘的大小：

1. 停止 VM。
1. 从门户增加 OS 磁盘的大小。
1. 启动 VM。

重新启动 VM 后，请执行以下步骤：

1. 通过使用以下命令，以根用户身份访问 VM：
 
   ```
   sudo su
   ```

1. 安装 gptfdisk 包，增加 OS 磁盘大小需要此包。

   ```
   yum install gdisk -y
   ```

1.  若要查看磁盘上的所有可用扇区，请运行以下命令：
    ```
    gdisk -l /dev/sda
    ```

1. 你将看到通知分区类型的详细信息。 确保它是 GPT。 标识根分区。 请勿更改或删除启动分区 (BIOS 启动分区) 和系统分区 ( "EFI 系统分区" ) 

1. 使用以下命令首次启动分区。 
    ```
    gdisk /dev/sda
    ```

1. 现在，你将看到一条消息，要求下一个命令 ( "命令：？ 获取帮助 ") 。 

   ```
   w
   ```

1. 您将收到一条警告，指出 "Warning！ 磁盘上的辅助标头太早放置了！ 是否要更正此问题？  (Y/N) ： "。 必须按 "Y"

   ```
   Y
   ```

1. 应该会看到一条消息，通知最终检查已完成并要求确认。 按 "Y"

   ```
   Y
   ```

1. 使用 partprobe 命令检查是否一切正常

   ```
   partprobe
   ```

1. 上述步骤确保将辅助 GPT 标头置于结尾。 下一步是再次使用 gdisk 工具开始调整大小的过程。 使用以下命令。

   ```
   gdisk /dev/sda
   ```
1. 在 "命令" 菜单中，按 "p" 查看分区列表。  (在步骤中标识根分区，将 sda2 视为根分区) 并且步骤中 (启动分区，sda3 被视为启动分区)  

   ```
   p
   ```
    ![根分区和启动分区](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw1.png)

1. 按 "d" 删除分区，并选择分配给 boot (的分区号，在本示例中为 "3" ) 
   ```
   d
   3
   ```
1. 按 "d" 删除分区，并选择分配给 boot (的分区号，在本示例中为 "2" ) 
   ```
   d
   2
   ```
    ![删除根分区和启动分区](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw2.png)

1. 若要重新创建大小增加的根分区，请按 "n"，输入以前为此) 示例的根 ( "2" 删除的分区号，并选择第一个扇区为 "默认值"，最后一个扇区作为 "最后一个扇区值-启动大小扇区" ( "4096"，这种情况下，对应于 2MB boot) ，16个8300代码
   ```
   n
   2
   (Enter default)
   (Calculateed value of Last sector value - 4096)
   8300
   ```
1. 若要重新创建启动分区，请按 "n"，为此示例输入先前为启动 ( "3" 删除的分区号) 并选择第一个扇区作为 "默认值"，将最后一个扇区作为 "默认值"，将十六进制代码选为 "EF02"
   ```
   n
   3
   (Enter default)
   (Enter default)
   EF02
   ```

1. 用 "w" 命令编写更改，然后按 "Y" 确认
   ```
   w
   Y
   ```
1. 运行命令 ' partprobe ' 检查磁盘稳定性
   ```
   partprobe
   ```
1. 重新启动 VM，根分区大小会增加
   ```
   reboot
   ```

   ![新建根分区和启动分区](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw3.png)

1. 在分区上运行 xfs_growfs 命令以调整其大小
   ```
   xfs_growfs /dev/sda2
   ```

   ![XFS 增长 FS](./media/resize-os-disk-rhelraw/resize-os-disk-rhelraw4.png)


1. 验证是否使用 **df** 命令反映了新大小：

   ```bash
   [root@vm-dd-cent7 ~]# df -hl
   Filesystem      Size  Used Avail Use% Mounted on
   devtmpfs        452M     0  452M   0% /dev
   tmpfs           464M     0  464M   0% /dev/shm
   tmpfs           464M  6.8M  457M   2% /run
   tmpfs           464M     0  464M   0% /sys/fs/cgroup
   /dev/sda2        48G  2.1G   46G   5% /
   /dev/sda1       494M   65M  430M  13% /boot
   /dev/sda15      495M   12M  484M   3% /boot/efi
   /dev/sdb1       3.9G   16M  3.7G   1% /mnt/resource
   tmpfs            93M     0   93M   0% /run/user/1000
   ```
