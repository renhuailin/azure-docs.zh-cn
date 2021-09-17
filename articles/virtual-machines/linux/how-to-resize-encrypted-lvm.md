---
title: 如何重设使用 Azure 磁盘加密进行加密的磁盘大小
description: 本文提供了使用逻辑卷管理重设 ADE 加密的磁盘大小的说明。
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.topic: article
ms.author: jofrance
ms.date: 09/21/2020
ms.openlocfilehash: bd23b2381fb042d80297c7b29fad382787ef1281
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697175"
---
# <a name="how-to-resize-logical-volume-management-devices-that-use-azure-disk-encryption"></a>如何重设使用 Azure 磁盘加密的逻辑卷管理设备的大小

适用于：:heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

本文介绍如何重设使用 Azure 磁盘加密的数据磁盘的大小。 若要重设磁盘大小，在 Linux 上使用逻辑卷管理 (LVM)。 这些步骤适用于多种场景。

你可以在以下环境中使用此重设大小过程：

- Linux 发行版：
    - Red Hat Enterprise Linux (RHEL) 7 或更高版本
    - Ubuntu 16 或更高版本
    - SUSE 12 或更高版本
- Azure 磁盘加密版本： 
    - 单通道扩展
    - 双通道扩展

## <a name="prerequisites"></a>先决条件

本文假设用户具备以下条件：

- 现有的 LVM 配置。 有关详细信息，请参阅[在 Linux VM 上配置 LVM](/previous-versions/azure/virtual-machines/linux/configure-lvm)。

- 已由 Azure 磁盘加密进行加密的磁盘。 有关详细信息，请参阅[在加密设备上配置 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md)。

- 使用 Linux 和 LVM 的经验。

- 在 Azure 上对数据磁盘使用 /dev/disk/scsi1/ 路径的经验。 有关详细信息，请参阅[排查 Linux VM 设备名称问题](/troubleshoot/azure/virtual-machines/troubleshoot-device-names-problems)。 

## <a name="scenarios"></a>方案

本文中的过程适用于以下场景：

- 传统的 LVM 和 LVM-on-crypt 配置
- 传统的 LVM 加密 
- LVM-on-crypt 

### <a name="traditional-lvm-and-lvm-on-crypt-configurations"></a>传统的 LVM 和 LVM-on-crypt 配置

当卷组 (VG) 有可用空间时，传统的 LVM 和 LVM-on-crypt 配置扩展逻辑卷 (LV)。

### <a name="traditional-lvm-encryption"></a>传统的 LVM 加密 

在传统的 LVM 加密中，LV 已加密。 未加密整个磁盘。

通过使用传统的 LVM 加密，你可以：

- 在添加新的物理卷 (PV) 时扩展 LV。
- 在重设现有 PV 的大小时，扩展 LV。

### <a name="lvm-on-crypt"></a>LVM-on-crypt 

推荐的磁盘加密方法是 LVM-on-encrypt。 该方法加密整个磁盘，而不仅仅是 LV。

通过使用 LVM-on-crypt，你可以： 

- 在添加新的 PV 时扩展 LV。
- 在重设现有 PV 的大小时，扩展 LV。

> [!NOTE]
> 建议不要在同一个 VM 上混合使用传统的 LVM 加密和 LVM-on-crypt。

以下部分提供了如何使用 LVM 和 LVM-on-crypt 的示例。 这些示例使用磁盘、PV、VG、LV、文件系统、全局唯一标识符 (UUID) 和装入点的预先存在的值。 将这些值替换为自己的值，使其适合你的环境。

#### <a name="extend-an-lv-when-the-vg-has-available-space"></a>当 VG 有可用空间时扩展 LV

重设 LV 大小的传统方法是在 VG 有可用空间时扩展 LV。 可以将此方法用于非加密磁盘、传统的 LVM 加密卷和 LVM-on-crypt 配置。

1. 验证要增大的文件系统的当前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![屏幕截图显示了检查文件系统大小的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/001-resize-lvm-scenarioa-check-fs.png)

2. 验证 VG 是否有足够的空间来增大 LV：

    ``` bash
    vgs
    ```

    ![屏幕截图显示了检查 VG 上的空间的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgs.png)

    也可使用 `vgdisplay`：

    ``` bash
    vgdisplay vgname
    ```

    ![屏幕截图显示了检查 VG 上的空间的 VG 显示代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-vgdisplay.png)

3. 确定需要重设大小的 LV：

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了 lsblk 命令的结果。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk1.png)

    对于 LVM-on-crypt，不同之处在于此输出显示加密层处于磁盘级别。

    ![屏幕截图显示了 lsblk 命令的结果。 突出显示了输出。 它显示了加密层。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lsblk2.png)

4. 检查 LV 大小：

    ``` bash
    lvdisplay lvname
    ```

    ![屏幕截图显示检查逻辑卷大小的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/002-resize-lvm-scenarioa-check-lvdisplay01.png)

5. 通过使用 `-r` 重设在线文件系统的大小来增加 LV 大小：

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![屏幕截图显示增加逻辑卷大小的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/003-resize-lvm-scenarioa-resize-lv.png)

6. 验证 LV 和文件系统的新大小：

    ``` bash
    df -h /mountpoint
    ```

    ![屏幕截图显示验证 LV 和文件系统的大小的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-fs.png)

    大小输出指示已成功重设 LV 和文件系统的大小。

可以再次检查 LV 信息以确认 LV 级别的更改：

``` bash
lvdisplay lvname
```

![屏幕截图显示确认新大小的代码。 突出显示了大小。](./media/disk-encryption/resize-lvm/004-resize-lvm-scenarioa-check-lvdisplay2.png)

#### <a name="extend-a-traditional-lvm-volume-by-adding-a-new-pv"></a>通过添加新的 PV 来扩展传统的 LVM 卷

当你需要添加一个新的磁盘来增加 VG 的大小时，可以通过添加一个新的 PV 来扩展传统的 LVM 卷。

1. 验证要增大的文件系统的当前大小：

    ``` bash
    df -h /mountpoint
    ```

    ![屏幕截图显示检查文件系统当前大小的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/005-resize-lvm-scenariob-check-fs.png)

2. 验证当前 PV 配置：

    ``` bash
    pvs
    ```

    ![屏幕截图显示检查当前 PV 配置的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/006-resize-lvm-scenariob-check-pvs.png)

3. 查看当前的 VG 信息：

    ``` bash
    vgs
    ```

    ![屏幕截图显示检查当前卷组信息的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/007-resize-lvm-scenariob-check-vgs.png)

4. 检查当前磁盘列表。 通过检查 /dev/disk/azure/scsi1/ 中的设备来标识数据磁盘。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![屏幕截图显示检查当前磁盘列表的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-scs1.png)

5. 查看 `lsblk` 的输出： 

    ``` bash
    lsbk
    ```

    ![屏幕截图显示检查 lsblk 输出的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/008-resize-lvm-scenariob-check-lsblk.png)

6. 按照[将数据磁盘附加到 Linux VM](attach-disk-portal.md) 中的说明，将新磁盘附加到 VM。

7. 检查磁盘列表，并注意新磁盘。

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![屏幕截图显示检查磁盘列表的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-scsi12.png)

    ``` bash
    lsbk
    ```

    ![屏幕截图显示通过使用 lsblk 检查磁盘列表的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/009-resize-lvm-scenariob-check-lsblk1.png)

8. 在新的数据磁盘上创建一个新 PV：

    ``` bash
    pvcreate /dev/newdisk
    ```

    ![屏幕截图显示创建新 PV 的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/010-resize-lvm-scenariob-pvcreate.png)

    此方法使用整个磁盘作为没有分区的 PV。 或者，你可以使用 `fdisk` 创建一个分区，然后将该分区用于 `pvcreate`。

9. 验证 PV 是否已添加到 PV 列表中：

    ``` bash
    pvs
    ```

    ![屏幕截图显示了显示物理卷列表的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/011-resize-lvm-scenariob-check-pvs1.png)

10. 通过向 VG 添加新的 PV 来扩展该 VG：

    ``` bash
    vgextend vgname /dev/newdisk
    ```

    ![屏幕截图显示扩展卷组的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/012-resize-lvm-scenariob-vgextend.png)

11. 检查新的 VG 大小：

    ``` bash
    vgs
    ```

    ![屏幕截图显示了检查卷组大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-vgs1.png)

12. 使用 `lsblk` 标识需要重设大小的 LV：

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了标识需要重设大小的本地卷的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-check-lsblk1.png)

13. 通过使用 `-r` 增大在线文件系统来扩展 LV 大小：

    ``` bash
    lvextend -r -L +2G /dev/vgname/lvname
    ```

    ![屏幕截图显示了增加在线文件系统大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/013-resize-lvm-scenariob-lvextend.png) 

14. 验证 LV 和文件系统的新大小：

    ``` bash
    df -h /mountpoint
    ```

    ![屏幕截图显示了检查本地卷和文件系统大小的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-fs1.png)

    >[!IMPORTANT]
    >对传统的 LVM 配置使用 Azure 数据加密时，加密层在 LV 级别（而不是磁盘级别）进行创建。
    >
    >此时，加密层已扩展到新磁盘。 实际的数据磁盘在平台级别没有加密设置，因此其加密状态不会更新。
    >
    >这些是推荐使用 LVM-on-crypt 方法的部分原因。 

15. 检查门户中的加密信息：

    ![屏幕截图显示了门户中加密信息。 突出显示了磁盘名称和加密。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal1.png)

    若要更新磁盘上的加密设置，请添加一个新的 LV 并在 VM 上启用扩展。
    
16. 添加新的 LV，在其上创建一个文件系统，并将其添加到 `/etc/fstab`。

17. 再次设置加密扩展。 这次，在平台级别的新数据磁盘上标记加密设置。 下面是 CLI 示例：

    ``` bash
    az vm encryption enable -g ${RGNAME} --name ${VMNAME} --disk-encryption-keyvault "<your-unique-keyvault-name>"
    ```

18. 检查门户中的加密信息：

    ![屏幕截图显示了门户中加密信息。 突出显示了磁盘名称和加密信息。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-check-portal2.png)

更新加密设置后，可以删除新的 LV。 同时从你创建的 `/etc/fstab` 和 `/etc/crypttab` 中删除条目。

![屏幕截图显示了删除新逻辑卷的代码。 突出显示了已删除的 FS 选项卡和 crypt 选项卡。](./media/disk-encryption/resize-lvm/014-resize-lvm-scenariob-delete-fstab-crypttab.png)

请按照以下步骤完成清理：

1. 卸载 LV：

    ``` bash
    umount /mountpoint
    ```

1. 关闭卷的加密层：

    ``` bash
    cryptsetup luksClose /dev/vgname/lvname
    ```

1. 删除 LV：

    ``` bash
    lvremove /dev/vgname/lvname
    ```

#### <a name="extend-a-traditional-lvm-volume-by-resizing-an-existing-pv"></a>通过重设现有 PV 的大小来扩展传统的 LVM 卷

在某些情况下，你的限制可能需要你重设现有磁盘的大小。 方法如下：

1. 标识加密的磁盘：

    ``` bash
    ls -l /dev/disk/azure/scsi1/
    ```

    ![屏幕截图显示了标识加密磁盘的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-scsi1.png)

    ``` bash
    lsblk -fs
    ```

    ![屏幕截图显示了标识加密磁盘的替代代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/015-resize-lvm-scenarioc-check-lsblk.png)

2. 检查 PV 信息：

    ``` bash
    pvs
    ```

    ![屏幕截图显示了检查物理卷相关信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/016-resize-lvm-scenarioc-check-pvs.png)

    图中的结果显示，所有 PV 上的所有空间当前都已使用。

3. 检查 VG 信息：

    ``` bash
    vgs
    vgdisplay -v vgname
    ```

    ![屏幕截图显示了检查卷组相关信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/017-resize-lvm-scenarioc-check-vgs.png)

4. 检查磁盘大小。 可以使用 `fdisk` 或 `lsblk` 列出驱动器大小。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![屏幕截图显示检查磁盘大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/018-resize-lvm-scenarioc-check-fdisk.png)

    此处，我们通过使用 `lsblk -fs` 来标识哪些 PV 与哪些 LV 相关联。 可以通过运行 `lvdisplay` 来标识关联。

    ``` bash
    lvdisplay --maps VG/LV
    lvdisplay --maps datavg/datalv1
    ```

    ![屏幕截图显示了标识物理卷与本地卷关联的替代方法。 突出显示了结果。](./media/disk-encryption/resize-lvm/019-resize-lvm-scenarioc-check-lvdisplay.png)

    在这种情况下，所有四个数据驱动器都属于同一个 VG 和单个 LV。 配置可能有所不同。

5. 检查当前文件系统使用率：

    ``` bash
    df -h /datalvm*
    ```

    ![屏幕截图显示了检查文件系统使用率的代码。 突出显示了命令和结果。](./media/disk-encryption/resize-lvm/020-resize-lvm-scenarioc-check-df.png)

6. 按照[展开 Azure 托管磁盘](expand-disks.md#expand-an-azure-managed-disk)中的说明重设数据磁盘的大小。 可以使用门户、CLI 或 PowerShell。

    >[!IMPORTANT]
    >VM 正在运行时，无法重设虚拟磁盘的大小。 在此步骤中解除分配 VM。

7. 启动 VM，并使用 `fdisk` 检查新的大小。

    ``` bash
    for disk in `ls -l /dev/disk/azure/scsi1/* | awk -F/ '{print $NF}'` ; do echo "fdisk -l /dev/${disk} | grep ^Disk "; done | bash

    lsblk -o "NAME,SIZE"
    ```

    ![屏幕截图显示了检查磁盘大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/021-resize-lvm-scenarioc-check-fdisk1.png)

    在这种情况下，`/dev/sdd` 的大小从 5 G 调整到 20 G。

8. 检查当前 PV 大小：

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![屏幕截图显示了检查 PV 大小的代码。突出显示了结果。](./media/disk-encryption/resize-lvm/022-resize-lvm-scenarioc-check-pvdisplay.png)
    
    即使磁盘已经重设大小，PV 仍然保持以前的大小。

9. 重设 PV 大小：

    ``` bash
    pvresize /dev/resizeddisk
    ```

    ![屏幕截图显示了重设物理卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/023-resize-lvm-scenarioc-check-pvresize.png)


10. 检查 PV 大小：

    ``` bash
    pvdisplay /dev/resizeddisk
    ```

    ![屏幕截图显示了检查物理卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/024-resize-lvm-scenarioc-check-pvdisplay1.png)

    对所有要重设大小的磁盘应用相同的过程。

11. 检查 VG 信息。

    ``` bash
    vgdisplay vgname
    ```

    ![屏幕截图显示了检查卷组信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/025-resize-lvm-scenarioc-check-vgdisplay1.png)

    VG 现在有足够的空间分配给 LV。

12. 重设 LV 大小：

    ``` bash
    lvresize -r -L +5G vgname/lvname
    lvresize -r -l +100%FREE /dev/datavg/datalv01
    ```

    ![屏幕截图显示了重设 LV 大小的代码。突出显示了结果。](./media/disk-encryption/resize-lvm/031-resize-lvm-scenarioc-check-lvresize1.png)

13. 检查文件系统的大小：

    ``` bash
    df -h /datalvm2
    ```

    ![屏幕截图显示了检查文件系统大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/032-resize-lvm-scenarioc-check-df3.png)

#### <a name="extend-an-lvm-on-crypt-volume-by-adding-a-new-pv"></a>通过添加新的 PV 来扩展 LVM-on-crypt 卷

你也可以通过添加新的 PV 来扩展 LVM-on-crypt 卷。 此方法严格遵循[在加密设备上配置 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md#general-steps) 的步骤。 请参阅介绍如何添加新磁盘并在 LVM-on-crypt 配置中设置它的部分。

可以使用此方法向现有 LV 添加空间。 也可以创建新的 VG 或 LV。

1. 验证 VG 的当前大小：

    ``` bash
    vgdisplay vgname
    ```

    ![屏幕截图显示了检查卷组大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/033-resize-lvm-scenarioe-check-vg01.png)

2. 验证要扩展的文件系统和 LV 的大小：

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![屏幕截图显示了检查本地卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-lv01.png)

    ``` bash
    df -h mountpoint
    ```

    ![屏幕截图显示了检查文件系统大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/034-resize-lvm-scenarioe-check-fs01.png)

3. 向 VM 添加新的数据磁盘并标识它。

    添加新磁盘之前，请检查磁盘：

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![屏幕截图显示了检查磁盘大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk01.png)

    下面是另一种在添加新磁盘之前检查磁盘的方法：

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了检查磁盘大小的替代代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/035-resize-lvm-scenarioe-check-newdisk02.png)

    若要添加新磁盘，可以使用 PowerShell、Azure CLI 或 Azure 门户。 有关详细信息，请参阅[将数据磁盘附加到 Linux VM](attach-disk-portal.md)。

    内核名称方案适用于新添加的设备。 通常向新驱动器分配下一个可用的驱动器号。 在本例中，添加的磁盘是 `sdd`。

4. 检查磁盘以确保已添加新磁盘：

    ``` bash
    fdisk -l | egrep ^"Disk /"
    ```

    ![屏幕截图显示了列出磁盘的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk02.png)

    ``` bash
    lsblk
    ```

    ![屏幕截图显示输出中新添加的磁盘。](./media/disk-encryption/resize-lvm/036-resize-lvm-scenarioe-check-newdisk03.png)

5. 在最近添加的磁盘上创建一个文件系统。 将磁盘与 `/dev/disk/azure/scsi1/` 上的链接设备匹配。

    ``` bash
    ls -la /dev/disk/azure/scsi1/
    ```

    ![屏幕截图显示了创建文件系统的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/037-resize-lvm-scenarioe-check-newdisk03.png)

    ``` bash
    mkfs.ext4 /dev/disk/azure/scsi1/${disk}
    ```

    ![屏幕截图显示了创建文件系统并将磁盘与链接设备匹配的其他代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-mkfs01.png)

6. 为新添加的磁盘创建临时装入点：

    ``` bash
    newmount=/data4
    mkdir ${newmount}
    ```

7. 将最近创建的文件系统添加到 `/etc/fstab`。

    ``` bash
    blkid /dev/disk/azure/scsi1/lun4| awk -F\" '{print "UUID="$2" '${newmount}' "$4" defaults,nofail 0 0"}' >> /etc/fstab
    ```

8. 装载新创建的文件系统：

    ``` bash
    mount -a
    ```

9. 验证是否已装载新文件系统：

    ``` bash
    df -h
    ```

    ![屏幕截图显示了验证文件系统是否已装载的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df.png)

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了验证文件系统是否已装载的其他代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk.png)

10. 重启以前为数据驱动器启动的加密。

    >[!TIP]
    >对于 LVM-on-crypt，建议使用 `EncryptFormatAll`。 否则，你可能会在设置其他磁盘时看到双重加密。
    >
    >有关详细信息，请参阅[在加密设备上配置 LVM 和 RAID](how-to-configure-lvm-raid-on-crypt.md)。

    下面是一个示例：

    ``` bash
    az vm encryption enable \
    --resource-group ${RGNAME} \
    --name ${VMNAME} \
    --disk-encryption-keyvault ${KEYVAULTNAME} \
    --key-encryption-key ${KEYNAME} \
    --key-encryption-keyvault ${KEYVAULTNAME} \
    --volume-type "DATA" \
    --encrypt-format-all \
    -o table
    ```

    加密完成后，你会在新添加的磁盘上看到一个加密层：

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了检查加密层的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk2.png)

11. 卸载新磁盘的加密层：

    ``` bash
    umount ${newmount}
    ```

12. 查看当前的 PV 信息：

    ``` bash
    pvs
    ```

    ![屏幕截图显示了检查物理卷相关信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-currentpvs.png)

13. 在磁盘的加密层上创建一个 PV。 从前面的 `lsblk` 命令获取设备名。 在设备名称前面添加 `/dev/` 映射程序以创建 PV：

    ``` bash
    pvcreate /dev/mapper/mapperdevicename
    ```

    ![屏幕截图显示了在加密层上创建物理卷的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-pvcreate.png)

    将显示一条关于擦除当前 `ext4 fs` 签名的警告。 此警告是在意料之内。 使用 `y` 回答此问题。

14. 验证新 PV 是否已添加到 LVM 配置中：

    ``` bash
    pvs
    ```

    ![屏幕截图显示了验证物理卷是否已添加到 LVM 配置的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-newpv.png)

15. 将新的 PV 添加到需要增大的 VG 中。

    ``` bash
    vgextend vgname /dev/mapper/nameofhenewpv
    ```

    ![屏幕截图显示了将物理卷添加到卷组的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgextent.png)

16. 验证 VG 的新大小和可用空间：

    ``` bash
    vgdisplay vgname
    ```

    ![屏幕截图显示了验证卷组大小和可用空间的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-vgdisplay.png)

    注意 `Total PE` 计数和 `Free PE / Size` 的增加。

17. 增加 LV 和文件系统的大小。 在 `lvextend` 上使用 `-r` 选项。 在本例中，我们将把 VG 中的总可用空间添加到给定的 LV 中。

    ``` bash
    lvextend -r -l +100%FREE /dev/vgname/lvname
    ```

    ![屏幕截图显示了增加本地卷和文件系统大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvextend.png)

按照以下步骤验证更改。

1. 验证 LV 的大小：

    ``` bash
    lvdisplay /dev/vgname/lvname
    ```

    ![屏幕截图显示了验证本地卷新大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lvdisplay.png)

1. 验证文件系统的新大小：

    ``` bash
    df -h mountpoint
    ```

    ![屏幕截图显示了验证文件系统新大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-df1.png)

1. 验证 LVM 层是否位于加密层之上：

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了验证 LVM 层是否位于加密层之上的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk3.png)

    如果你使用不带选项的 `lsblk`，则会多次看到装入点。 命令按设备和 LV 进行排序。 

    你可能需要使用 `lsblk -fs`。 在此命令中，`-fs` 反转排序顺序，使装入点只显示一次。 磁盘显示多次。

    ``` bash
    lsblk -fs
    ```

    ![屏幕截图显示了验证 LVM 层是否位于加密层之上的替代代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/038-resize-lvm-scenarioe-lsblk4.png)

#### <a name="extend-an-lvm-on-a-crypt-volume-by-resizing-an-existing-pv"></a>通过重设现有 PV 的大小来扩展 LVM-on-crypt 卷

1. 标识加密的磁盘：

    ``` bash
    lsblk
    ```

    ![屏幕截图显示了标识加密磁盘的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/039-resize-lvm-scenariof-lsblk01.png)

    ``` bash
    lsblk -s
    ```

    ![屏幕截图显示了标识加密磁盘的替代代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/040-resize-lvm-scenariof-lsblk012.png)

2. 检查 PV 信息：

    ``` bash
    pvs
    ```

    ![屏幕截图显示了检查物理卷信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/041-resize-lvm-scenariof-pvs.png)

3. 检查 VG 信息：

    ``` bash
    vgs
    ```

    ![屏幕截图显示了检查卷组信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/042-resize-lvm-scenariof-vgs.png)

4. 检查 LV 信息：

    ``` bash
    lvs
    ```

    ![屏幕截图显示了检查本地卷信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/043-resize-lvm-scenariof-lvs.png)

5. 检查文件系统使用率：

    ``` bash
    df -h /mountpoint(s)
    ```

    ![屏幕截图显示了检查文件系统使用量的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/044-resize-lvm-scenariof-fs.png)

6. 检查磁盘的大小：

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![屏幕截图显示了检查磁盘大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/045-resize-lvm-scenariof-fdisk01.png)

7. 重设数据磁盘的大小。 可以使用门户、CLI 或 PowerShell。 有关详细信息，请参阅[扩展 Linux VM 上的虚拟硬盘](expand-disks.md#expand-an-azure-managed-disk)中的重设磁盘大小部分。 

    >[!IMPORTANT]
    >VM 正在运行时，无法重设虚拟磁盘的大小。 在此步骤中解除分配 VM。

8. 检查磁盘大小：

    ``` bash
    fdisk
    fdisk -l | egrep ^"Disk /"
    lsblk
    ```

    ![屏幕截图显示了检查磁盘大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/046-resize-lvm-scenariof-fdisk02.png)

    在这种情况下，两个磁盘的大小都从 2 GB 调整到 4 GB。 但是文件系统、LV 和 PV 的大小保持不变。

9. 检查当前 PV 大小。 请记住，在 LVM-on-crypt 上，PV 是 `/dev/mapper/` 设备，而不是 `/dev/sd*` 设备。

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![屏幕截图显示了检查当前物理卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/047-resize-lvm-scenariof-pvs.png)

10. 重设 PV 大小：

    ``` bash
    pvresize /dev/mapper/devicemappername
    ```

    ![屏幕截图显示了重设物理卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/048-resize-lvm-scenariof-resize-pv.png)

11. 检查新的 PV 大小：

    ``` bash
    pvdisplay /dev/mapper/devicemappername
    ```

    ![屏幕截图显示了检查物理卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/049-resize-lvm-scenariof-pv.png)

12. 重设 PV 上加密层的大小：

    ``` bash
    cryptsetup resize /dev/mapper/devicemappername
    ```

    对所有要重设大小的磁盘应用相同的过程。

13. 检查 VG 信息：

    ``` bash
    vgdisplay vgname
    ```

    ![屏幕截图显示了检查卷组信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/050-resize-lvm-scenariof-vg.png)

    VG 现在有足够的空间分配给 LV。

14. 检查 LV 信息：

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![屏幕截图显示了检查本地卷信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/051-resize-lvm-scenariof-lv.png)

15. 检查文件系统使用率：

    ``` bash
    df -h /mountpoint
    ```

    ![屏幕截图显示了检查文件系统利用率的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/052-resize-lvm-scenariof-fs.png)

16. 重设 LV 大小：

    ``` bash
    lvresize -r -L +2G /dev/vgname/lvname
    ```

    ![屏幕截图显示了重设本地卷大小的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/053-resize-lvm-scenariof-lvresize.png)

    此处，我们也使用 `-r` 选项来重设文件系统的大小。

17. 检查 LV 信息：

    ``` bash
    lvdisplay vgname/lvname
    ```

    ![屏幕截图显示了获取本地卷信息的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/054-resize-lvm-scenariof-lvsize.png)

18. 检查文件系统使用率：

    ``` bash
    df -h /mountpoint
    ```

    ![屏幕截图显示了检查文件系统使用率的代码。 突出显示了结果。](./media/disk-encryption/resize-lvm/055-resize-lvm-scenariof-fs.png)

对其他任何需要重设大小的 LV 应用相同的重设大小过程。

## <a name="next-steps"></a>后续步骤

[对 Azure 磁盘加密进行故障排除](disk-encryption-troubleshooting.md)