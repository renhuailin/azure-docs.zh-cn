---
title: 排查 Azure VM 文件恢复问题
description: 排查从 Azure VM 备份恢复文件和文件夹时遇到的问题。
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: c4d0d233237cb477d72efea0b91d4e5288e2a302
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98735871"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>排查 Azure VM 备份的文件恢复问题

本文提供了一些故障排除步骤，可帮助你解决从 Azure 虚拟机 (VM) 备份中恢复文件和文件夹的问题。

## <a name="common-error-messages"></a>常见错误消息

本部分提供了对可能看到的错误消息进行故障排除的步骤。

### <a name="exception-caught-while-connecting-to-target"></a>"连接到目标时捕获到异常"

**可能的原因**：脚本无法访问恢复点。

**建议的操作**：若要解决此问题，请执行脚本运行中列出的步骤， [但连接失败](#the-script-runs-but-the-connection-to-the-iscsi-target-failed)。

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>"目标已经通过 iSCSI 会话登录"

**可能的原因**：该脚本已在同一台计算机上运行，并且已附加驱动器。

**建议的操作**：已附加恢复点所在的卷。 它们不能以原始 VM 的相同驱动器号装入。 在文件资源管理器中浏览可用的卷。

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>"此脚本无效，因为磁盘已通过门户卸载/已超过12小时的限制。 从门户下载新脚本 "

**可能的原因**：磁盘已从门户卸除或超过了12小时的时间限制。

**建议的操作**：在下载脚本后的12小时后，该脚本将失效，无法运行。 转到门户，然后下载新的脚本，继续进行文件恢复。

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>无法加载 iscsi_tcp 模块 (或) iscsi_tcp_module 找不到

**建议的操作**：若要解决此问题，请执行 [脚本下载成功但未能运行的](#the-script-downloads-successfully-but-fails-to-run)步骤。

## <a name="common-problems"></a>常见问题

本部分介绍了在下载和执行用于文件恢复的脚本时可能会遇到的常见问题的步骤。

### <a name="you-cant-download-the-script"></a>无法下载该脚本

1. 确保你具有 [下载该脚本所需的权限](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script)。
1. 验证与 Azure 目标 Ip 的连接。 在提升的命令提示符下运行以下命令之一：

   `nslookup download.microsoft.com`

    或

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>脚本下载成功，但无法运行

在 SUSE Linux Enterprise Server 12 SP4 上运行项目级恢复 (ILR) 的 Python 脚本时，它将失败，并出现错误 "无法加载 iscsi_tcp 模块" 或 "找不到 iscsi_tcp_module"。

**可能的原因**： ILR 模块使用 **iscsi_tcp** 与备份服务建立 tcp 连接。 作为 SLES 12 SP4 版本的一部分，已从开放 iscsi 包中删除 **iscsi_tcp** ，因此 ILR 操作将失败。

**建议的操作**： SUSE 12 SP4 vm 不支持文件恢复脚本执行。 在 SUSE 12 SP4 的较早版本上尝试还原操作。

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>脚本将运行，但与 iSCSI 目标的连接失败

你可能会看到 "连接到目标时捕获到异常" 错误消息。

1. 确保运行脚本的计算机满足 [访问要求](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script)。
1. 验证与 Azure 目标 Ip 的连接。 在提升的命令提示符下运行以下命令之一：

   `nslookup download.microsoft.com`

   或

   `ping download.microsoft.com`
1. 确保访问 iSCSI 出站端口3260。
1. 检查防火墙或 NSG 阻止流量流向 Azure 目标 Ip 或恢复服务 Url。
1. 请确保防病毒软件未阻止脚本的执行。

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>你已连接到恢复点，但未附加磁盘

按照适用于你的操作系统的步骤解决此问题。

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>具有存储池的服务器上的 Windows 文件恢复失败

当你在 Windows Server 2012 R2 和 Windows Server 2016 (上首次运行脚本时，如果) 存储池，则存储池可能会以只读方式附加到 VM。

>[!Tip]
> 确保具有[符合要求的计算机来运行脚本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

若要解决此问题，请手动分配存储池的读写访问权限并附加虚拟磁盘：

1. 请参阅 **服务器管理器**  >  **文件和存储服务**"" 卷 ""  >    >  **存储池**"。

   ![显示存储池选项的屏幕截图。](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. 在 " **存储池** " 窗口中，右键单击可用存储池，然后选择 " **设置 Read-Write 访问**"。

   ![显示存储假脱机的右键单击选项的屏幕截图。](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 为存储池分配读写访问权限后，右键单击 " **虚拟磁盘** " 部分，然后选择 " **附加虚拟磁盘**"。

   ![显示虚拟磁盘的右键单击选项的屏幕截图。](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Linux 文件恢复无法自动装载，因为磁盘不包含卷

执行文件恢复时，备份服务会检测卷和自动装入。 但是，如果备份磁盘具有原始分区，则这些磁盘不会自动装入，你将无法查看数据磁盘进行恢复。

若要解决此问题，请参阅 [从 Azure 虚拟机备份恢复文件](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)。

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Linux 文件恢复失败，因为 OS 无法识别文件系统

运行文件恢复脚本时，无法附加数据磁盘。 出现 "由于 OS 无法识别文件系统而未能装载以下分区" 错误。

若要解决此问题，请检查是否使用第三方应用程序对该卷进行了加密。 如果对其进行了加密，则不会在门户中将磁盘或 VM 显示为已加密。

1. 登录到已备份的 VM，并运行以下命令：

   `lsblk -f`

   ![显示列出阻止设备的命令结果的屏幕截图。](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. 验证文件系统和加密。 如果卷已加密，则不支持文件恢复。 了解 [AZURE VM 备份的支持矩阵的](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)详细信息。

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>磁盘已连接，但未装入卷

按照适用于你的操作系统的步骤解决此问题。

#### <a name="windows"></a>Windows

当你运行适用于 Windows 的文件恢复脚本时，你会看到 "0 个恢复卷已附加" 消息。 但是，会在磁盘管理控制台中发现这些磁盘。

**可能的原因**：通过 iSCSI 附加卷时，检测到的某些卷处于脱机状态。 当 iSCSI 通道在 VM 和服务之间通信时，它将检测这些卷并将其联机，但不会装载它们。

   ![显示附加的0个恢复卷的屏幕截图。](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

若要识别并解决此问题，请执行以下步骤：

>[!Tip]
>确保具有[符合要求的计算机来运行脚本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

1. 在 **cmd** 窗口中，运行 **diskmgmt.msc** 以打开 " **磁盘管理**"。
1. 查找其他所有磁盘。 在下面的示例中， **磁盘 2** 是附加磁盘。

   ![带有额外磁盘的 "磁盘管理" 窗口的屏幕截图。](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. 右键单击 " **新建卷**"，然后选择 " **更改驱动器号和路径**"。

   ![显示其他磁盘上的右键单击选项的屏幕截图。](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. 在 " **更改驱动器号或路径** " 窗口中，选择 " **分配以下驱动器号**"、"分配可用驱动器"，然后选择 **"确定"**。

   ![更改驱动器号或路径窗口的屏幕截图。](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 打开文件资源管理器以查看选择的驱动器并浏览这些文件。

#### <a name="linux"></a>Linux

>[!Tip]
>确保具有[符合要求的计算机来运行脚本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

如果受保护的 Linux VM 使用 LVM 或 RAID 阵列，请遵循 [从 Azure 虚拟机备份恢复文件](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)中的步骤。

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>无法从装载的卷复制文件

复制可能会失败，并出现错误 "0x80070780：无法访问文件。" 

检查源服务器是否已启用磁盘删除重复。 如果是这样，请确保还原服务器还在驱动器上启用了删除重复。 可以不配置重复数据删除，以便不删除重复还原服务器上的驱动器。

## <a name="next-steps"></a>后续步骤

- [从 Azure 虚拟机备份恢复文件和文件夹](backup-azure-restore-files-from-vm.md)