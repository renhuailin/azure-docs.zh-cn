---
title: 在加密设备上配置 LVM 和 RAID - Azure 磁盘加密
description: 本文介绍如何在适用于 Linux VM 的加密设备上配置 LVM 和 RAID。
author: jofrance
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: how-to
ms.author: jofrance
ms.date: 03/17/2020
ms.custom: seodec18, devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: e769e5a8e35854d0b871ed8ecd9bc343cdcc04b6
ms.sourcegitcommit: 58d82486531472268c5ff70b1e012fc008226753
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122697029"
---
# <a name="configure-lvm-and-raid-on-encrypted-devices"></a>在加密设备上配置 LVM 和 RAID

**适用于：** :heavy_check_mark: Linux VM :heavy_check_mark: 灵活规模集 

本文分步介绍如何在加密设备上执行逻辑卷管理 (LVM) 和 RAID。 该过程适用于以下环境：

- Linux 发行版
    - RHEL 7.6+
    - Ubuntu 18.04+
    - SUSE 12+
- Azure 磁盘加密单一传递扩展
- Azure 磁盘加密双重传递扩展


## <a name="scenarios"></a>方案

本文中的过程支持以下方案：  

- 在加密设备上配置 LVM (LVM-on-crypt)
- 在加密设备上配置 RAID (RAID-on-crypt)

在加密基础设备或设备之后，就可以在该加密层上创建 LVM 或 RAID 结构。 

物理卷 (PV) 在加密层上创建。 物理卷用于创建卷组。 创建卷并在 /etc/fstab 上添加所需的条目。 

![LVM 结构层关系图](./media/disk-encryption/lvm-raid-on-crypt/000-lvm-raid-crypt-diagram.png)

以类似的方式，在磁盘上的加密层上创建 RAID 设备。 在 RAID 设备上创建文件系统，并将其作为常规设备添加到 /etc/fstab。

## <a name="considerations"></a>注意事项

建议使用 LVM-on-crypt。 由于特定的应用程序或环境限制而无法使用 LVM 时，可以选择 RAID。

我们将使用“EncryptFormatAll”选项。 有关此选项的详细信息，请参阅[将 EncryptFormatAll 功能用于 Linux VM 上的数据磁盘](./disk-encryption-linux.md#use-encryptformatall-feature-for-data-disks-on-linux-vms)。

虽然在加密操作系统时也可以使用此方法，但在这里仅加密数据驱动器。

这些过程假定已检查 [Linux VM 上的 Azure 磁盘加密方案](./disk-encryption-linux.md)和[快速入门：使用 Azure CLI 创建 Linux VM 并对其进行加密](./disk-encryption-cli-quickstart.md)中的先决条件。

Azure 磁盘加密双重传递版本位于弃用路径上，不应再用于新加密。

## <a name="general-steps"></a>常规步骤

使用“on-crypt”配置时，请使用以下过程中概述的过程。

>[!NOTE] 
>我们将在本文中使用变量。 请相应地替换值。

### <a name="deploy-a-vm"></a>部署 VM 
以下命令可选，但建议将其应用于新部署的虚拟机 (VM)。

PowerShell：

```powershell
New-AzVm -ResourceGroupName ${RGNAME} `
-Name ${VMNAME} `
-Location ${LOCATION} `
-Size ${VMSIZE} `
-Image ${OSIMAGE} `
-Credential ${creds} `
-Verbose
```
Azure CLI：

```azurecli
az vm create \
-n ${VMNAME} \
-g ${RGNAME} \
--image ${OSIMAGE} \
--admin-username ${username} \
--admin-password ${password} \
-l ${LOCATION} \
--size ${VMSIZE} \
-o table
```
### <a name="attach-disks-to-the-vm"></a>将磁盘附加到 VM
对要附加到 VM 的 `$N` 个新磁盘重复以下命令。

PowerShell：

```powershell
$storageType = 'Standard_LRS'
$dataDiskName = ${VMNAME} + '_datadisk0'
$diskConfig = New-AzDiskConfig -SkuName $storageType -Location $LOCATION -CreateOption Empty -DiskSizeGB 5
$dataDisk1 = New-AzDisk -DiskName $dataDiskName -Disk $diskConfig -ResourceGroupName ${RGNAME}
$vm = Get-AzVM -Name ${VMNAME} -ResourceGroupName ${RGNAME} 
$vm = Add-AzVMDataDisk -VM $vm -Name $dataDiskName -CreateOption Attach -ManagedDiskId $dataDisk1.Id -Lun 0
Update-AzVM -VM ${VM} -ResourceGroupName ${RGNAME}
```

Azure CLI：

```azurecli
az vm disk attach \
-g ${RGNAME} \
--vm-name ${VMNAME} \
--name ${VMNAME}datadisk1 \
--size-gb 5 \
--new \
-o table
```

### <a name="verify-that-the-disks-are-attached-to-the-vm"></a>验证磁盘是否已附加到 VM
PowerShell：
```powershell
$VM = Get-AzVM -ResourceGroupName ${RGNAME} -Name ${VMNAME}
$VM.StorageProfile.DataDisks | Select-Object Lun,Name,DiskSizeGB
```
![PowerShell 中附加磁盘的列表](./media/disk-encryption/lvm-raid-on-crypt/001-lvm-raid-check-disks-powershell.png)

Azure CLI：

```azurecli
az vm show -g ${RGNAME} -n ${VMNAME} --query storageProfile.dataDisks -o table
```
![Azure CLI 中附加磁盘的列表](./media/disk-encryption/lvm-raid-on-crypt/002-lvm-raid-check-disks-cli.png)

门户：

![门户中附加磁盘的列表](./media/disk-encryption/lvm-raid-on-crypt/003-lvm-raid-check-disks-portal.png)

OS：

```bash
lsblk 
```
![OS 中附加磁盘的列表](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="configure-the-disks-to-be-encrypted"></a>配置要加密的磁盘
此配置在操作系统级别完成。 将相应的磁盘配置为通过 Azure 磁盘加密进行传统加密：

- 文件系统是在磁盘上创建的。
- 创建临时装入点以装载文件系统。
- 文件系统在 /etc/fstab 上配置为在启动时装载。

检查分配给新磁盘的设备号。 在此示例中，使用四个数据磁盘。

```bash
lsblk 
```
![附加到 OS 的数据磁盘](./media/disk-encryption/lvm-raid-on-crypt/004-lvm-raid-check-disks-os.png)

### <a name="create-a-file-system-on-top-of-each-disk"></a>在每个磁盘上创建文件系统
此命令在“for”循环的“in”部分中定义的每个磁盘上迭代创建 ext4 文件系统。

```bash
for disk in c d e f; do echo mkfs.ext4 -F /dev/sd${disk}; done |bash
```
![创建 ext4 文件系统](./media/disk-encryption/lvm-raid-on-crypt/005-lvm-raid-create-temp-fs.png)

查找最近创建的文件系统的通用唯一标识符 (UUID)，创建临时文件夹，在 /etc/fstab 上添加相应的条目，然后装载所有文件系统。

此命令还会在“for”循环的“in”部分中定义的每个磁盘上进行迭代：

```bash
for disk in c d e f; do diskuuid="$(blkid -s UUID -o value /dev/sd${disk})"; \
mkdir /tempdata${disk}; \
echo "UUID=${diskuuid} /tempdata${disk} ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
``` 

### <a name="verify-that-the-disks-are-mounted-properly"></a>验证磁盘是否已正确装载
```bash
lsblk
```
![已装载的临时文件系统列表](./media/disk-encryption/lvm-raid-on-crypt/006-lvm-raid-verify-temp-fs.png)

还要验证磁盘是否已配置：

```bash
cat /etc/fstab
```
![通过 fstab 配置信息](./media/disk-encryption/lvm-raid-on-crypt/007-lvm-raid-verify-temp-fstab.png)

### <a name="encrypt-the-data-disks"></a>加密数据磁盘
使用密钥加密密钥 (KEK) 的 PowerShell：

```powershell
$sequenceVersion = [Guid]::NewGuid() 
Set-AzVMDiskEncryptionExtension -ResourceGroupName $RGNAME `
-VMName ${VMNAME} `
-DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl `
-DiskEncryptionKeyVaultId $KeyVaultResourceId `
-KeyEncryptionKeyUrl $keyEncryptionKeyUrl `
-KeyEncryptionKeyVaultId $KeyVaultResourceId `
-VolumeType 'DATA' `
-EncryptFormatAll `
-SequenceVersion $sequenceVersion `
-skipVmBackup;
```

使用 KEK 的 Azure CLI：

```azurecli
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
### <a name="verify-the-encryption-status"></a>验证加密状态

仅当所有磁盘都已加密后，才继续下一步。

PowerShell：

```powershell
Get-AzVmDiskEncryptionStatus -ResourceGroupName ${RGNAME} -VMName ${VMNAME}
```
![PowerShell 中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/008-lvm-raid-verify-encryption-status-ps.png)

Azure CLI：

```azurecli
az vm encryption show -n ${VMNAME} -g ${RGNAME} -o table
```
![Azure CLI 中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/009-lvm-raid-verify-encryption-status-cli.png)

门户：

![门户中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/010-lvm-raid-verify-encryption-status-portal.png)

OS 级别：

```bash
lsblk
```
![OS 中的加密状态](./media/disk-encryption/lvm-raid-on-crypt/011-lvm-raid-verify-encryption-status-os.png)

扩展会将文件系统添加到 /var/lib/azure_disk_encryption_config/azure_crypt_mount（旧加密）或 /etc/crypttab（新加密）。

>[!NOTE] 
>请不要修改任何其他文件。

此文件将负责在启动过程中激活这些磁盘，以便 LVM 或 RAID 以后可以使用这些磁盘。 

不用担心此文件上的装入点。 当在这些加密设备上创建物理卷或 RAID 设备之后，Azure 磁盘加密将无法将磁盘作为普通文件系统安装。 （这将删除在准备过程中使用的文件系统格式。）

### <a name="remove-the-temporary-folders-and-temporary-fstab-entries"></a>删除临时文件夹和临时 fstab 条目
在将用作 LVM 一部分的磁盘上卸载文件系统。

```bash
for disk in c d e f; do umount /tempdata${disk}; done
```
同时删除 /etc/fstab 条目：

```bash
vi /etc/fstab
```
### <a name="verify-that-the-disks-are-not-mounted-and-that-the-entries-on-etcfstab-were-removed"></a>验证是否未装载磁盘以及是否已删除 /etc/fstab 上的条目

```bash
lsblk
```
![验证是否已卸载临时文件系统](./media/disk-encryption/lvm-raid-on-crypt/012-lvm-raid-verify-disks-not-mounted.png)

同时验证是否已配置磁盘：
```bash
cat /etc/fstab
```
![验证是否已删除临时 fstab 条目](./media/disk-encryption/lvm-raid-on-crypt/013-lvm-raid-verify-fstab-temp-removed.png)

## <a name="steps-for-lvm-on-crypt"></a>LVM-on-crypt 的步骤
加密基础磁盘之后，接下来即可创建 LVM 结构。

不要使用设备名称，而是使用每个磁盘的 /dev/mapper 路径来创建物理卷（在磁盘上的加密层上，而不是磁盘本身上）。

### <a name="configure-lvm-on-top-of-the-encrypted-layers"></a>在加密层上配置 LVM
#### <a name="create-the-physical-volumes"></a>创建物理卷
你将收到一条警告，询问是否可以擦除文件系统签名。 输入 y 继续操作，或使用回显“y”，如下所示 ：

```bash
echo "y" | pvcreate /dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052
echo "y" | pvcreate /dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1
echo "y" | pvcreate /dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a
echo "y" | pvcreate /dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![验证是否已创建物理卷](./media/disk-encryption/lvm-raid-on-crypt/014-lvm-raid-pvcreate.png)

>[!NOTE] 
>需要根据 lsblk 的输出，将此处的 /dev/mapper/device 名称替换为实际值。

#### <a name="verify-the-information-for-physical-volumes"></a>验证物理卷的信息
```bash
pvs
```

![物理卷的信息](./media/disk-encryption/lvm-raid-on-crypt/015-lvm-raid-pvs.png)

#### <a name="create-the-volume-group"></a>创建卷组
使用已初始化的相同设备创建卷组：

```bash
vgcreate vgdata /dev/mapper/
```

### <a name="check-the-information-for-the-volume-group"></a>检查卷组的信息

```bash
vgdisplay -v vgdata
```
```bash
pvs
```
![卷组的信息](./media/disk-encryption/lvm-raid-on-crypt/016-lvm-raid-pvs-on-vg.png)

#### <a name="create-logical-volumes"></a>创建逻辑卷

```bash
lvcreate -L 10G -n lvdata1 vgdata
lvcreate -L 7G -n lvdata2 vgdata
``` 

#### <a name="check-the-created-logical-volumes"></a>检查创建的逻辑卷

```bash
lvdisplay
lvdisplay vgdata/lvdata1
lvdisplay vgdata/lvdata2
```
![逻辑卷的信息](./media/disk-encryption/lvm-raid-on-crypt/017-lvm-raid-lvs.png)

#### <a name="create-file-systems-on-top-of-the-structures-for-logical-volumes"></a>在逻辑卷的结构上创建文件系统

```bash
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata1
echo "yes" | mkfs.ext4 /dev/vgdata/lvdata2
```

#### <a name="create-the-mount-points-for-the-new-file-systems"></a>创建新文件系统的装入点

```bash
mkdir /data0
mkdir /data1
```

#### <a name="add-the-new-file-systems-to-etcfstab-and-mount-them"></a>将新文件系统添加到 /etc/fstab 并对其进行装载

```bash
echo "/dev/mapper/vgdata-lvdata1 /data0 ext4 defaults,nofail 0 0" >>/etc/fstab
echo "/dev/mapper/vgdata-lvdata2 /data1 ext4 defaults,nofail 0 0" >>/etc/fstab
mount -a
```

#### <a name="verify-that-the-new-file-systems-are-mounted"></a>验证是否已装载新文件系统

```bash
lsblk -fs
df -h
```
![屏幕截图显示控制台窗口，其中文件系统装载为 data0 和 data1。](./media/disk-encryption/lvm-raid-on-crypt/018-lvm-raid-lsblk-after-lvm.png)

在 lsblk 的这种变体中，我们将按相反顺序列出显示依赖项的设备。 此选项有助于标识按逻辑卷（而不是原始的 /dev/sd[磁盘] 设备名称）分组的设备。

务必确保将 nofail 选项添加到在通过 Azure 磁盘加密进行加密的设备上创建的 LVM 卷的装入点选项中。 它可以防止 OS 在启动过程中（或处于维护模式）停滞。

如果不使用 nofail 选项：

- OS 将永远不会进入启动 Azure 磁盘加密以及解锁和装载数据磁盘的阶段。 
- 启动过程结束时，将解锁加密的磁盘。 LVM 卷和文件系统会自动装载，直到 Azure 磁盘加密将其解锁。 

可以测试重启 VM 的过程，并验证启动后是否还自动装载了文件系统。 此过程可能需要几分钟时间，具体取决于文件系统的数量和大小。

#### <a name="reboot-the-vm-and-verify-after-reboot"></a>重启 VM 并在重启后对其进行验证

```bash
shutdown -r now
```
```bash
lsblk
df -h
```
## <a name="steps-for-raid-on-crypt"></a>RAID-on-crypt 的步骤
现在，基础磁盘已加密，可以继续创建 RAID 结构。 此过程与 LVM 的过程相同，但使用的是每个磁盘的 /dev/mapper 路径，而不是设备名称。

#### <a name="configure-raid-on-top-of-the-encrypted-layer-of-the-disks"></a>在磁盘的加密层上配置 RAID
```bash
mdadm --create /dev/md10 \
--level 0 \
--raid-devices=4 \
/dev/mapper/c49ff535-1df9-45ad-9dad-f0846509f052 \
/dev/mapper/6712ad6f-65ce-487b-aa52-462f381611a1 \
/dev/mapper/ea607dfd-c396-48d6-bc54-603cf741bc2a \
/dev/mapper/4159c60a-a546-455b-985f-92865d51158c
```
![通过 mdadm 命令配置的 RAID 的信息](./media/disk-encryption/lvm-raid-on-crypt/019-lvm-raid-md-creation.png)

>[!NOTE] 
>需要根据 lsblk 的输出，将此处的 /dev/mapper/device 名称替换为实际值。

### <a name="checkmonitor-raid-creation"></a>检查/监视 RAID 的创建过程
```bash
watch -n1 cat /proc/mdstat
mdadm --examine /dev/mapper/[]
mdadm --detail /dev/md10
```
![RAID 状态](./media/disk-encryption/lvm-raid-on-crypt/020-lvm-raid-md-details.png)

### <a name="create-a-file-system-on-top-of-the-new-raid-device"></a>在新的 RAID 设备上创建文件系统
```bash
mkfs.ext4 /dev/md10
```

为文件系统创建新的装入点，将新的文件系统添加到 /etc/fstab 并装载该文件：

>[!NOTE] 
>在本特定示例中，此循环仅在一台设备上循环访问，采用这种构建方式以便在需要时用于多个 md 设备。

```bash
for device in md10; do diskuuid="$(blkid -s UUID -o value /dev/${device})"; \
mkdir /raiddata; \
echo "UUID=${diskuuid} /raiddata ext4 defaults,nofail 0 0" >> /etc/fstab; \
mount -a; \
done
```

验证是否已装载新文件系统：

```bash
lsblk -fs
df -h
```
![屏幕截图显示控制台窗口，其中文件系统已装载为 raiddata。](./media/disk-encryption/lvm-raid-on-crypt/021-lvm-raid-lsblk-md-details.png)

务必确保将 nofail 选项添加到在通过 Azure 磁盘加密进行加密的设备上创建的 RAID 卷的装入点选项中。 它可以防止 OS 在启动过程中（或处于维护模式）停滞。

如果不使用 nofail 选项：

- OS 将永远不会进入启动 Azure 磁盘加密以及解锁和装载数据磁盘的阶段。
- 启动过程结束时，将解锁加密的磁盘。 RAID 卷和文件系统会自动装载，直到 Azure 磁盘加密将其解锁。

可以测试重启 VM 的过程，并验证启动后是否还自动装载了文件系统。 此过程可能需要几分钟时间，具体取决于文件系统的数量和大小。

```bash
shutdown -r now
```

可以登录时：

```bash
lsblk
df -h
```
## <a name="next-steps"></a>后续步骤

- [重设使用 Azure 磁盘加密进行加密的逻辑卷管理设备的大小](how-to-resize-encrypted-lvm.md)
- [Azure 磁盘加密疑难解答](disk-encryption-troubleshooting.md)
