---
title: 排查 Azure VM 文件恢复问题
description: 排查从 Azure VM 备份恢复文件和文件夹时遇到的问题。
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: 0ee856b10c6a5fbea6f6f76b2082949ab9c1e0db
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101700297"
---
# <a name="troubleshoot-issues-in-file-recovery-of-an-azure-vm-backup"></a>排查 Azure VM 备份的文件恢复问题

本文提供的故障排查步骤可帮助你解决从 Azure 虚拟机 (VM) 备份恢复文件和文件夹时出现的问题。

## <a name="common-error-messages"></a>常见错误消息

本部分提供了针对可能出现的错误消息的故障排除步骤。

### <a name="exception-caught-while-connecting-to-target"></a>“连接到目标时捕获到异常”

**可能的原因**：脚本无法访问恢复点。

**建议的操作**：若要解决此问题，请按 [脚本运行但连接失败](#the-script-runs-but-the-connection-to-the-iscsi-target-failed)中列出的步骤进行操作。

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>“已经通过 iSCSI 会话登录目标”

**可能的原因**：脚本已在同一台计算机上运行，并且已附加驱动器。

**建议的操作**：已附加恢复点所在的卷。 不能使用原始 VM 的驱动器号装载这些卷。 请在文件资源管理器中浏览可用卷。

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>“此脚本无效，因为磁盘已通过门户卸除/已超过 12 小时限制。 请从门户下载新脚本”

**可能的原因**：磁盘已从门户卸除或已超过 12 小时的时间限制。

**建议的操作**：下载脚本 12 小时后，脚本将失效，无法运行。 请转到门户，下载新脚本，以继续进行文件恢复。

### <a name="iscsi_tcp-module-cant-be-loaded-or-iscsi_tcp_module-not-found"></a>无法加载 iscsi_tcp 模块或者找不到 iscsi_tcp_module

**建议的操作**：若要解决此问题，请按照 [脚本下载成功但无法运行](#the-script-downloads-successfully-but-fails-to-run)中的步骤进行操作。

## <a name="common-problems"></a>常见问题

本部分提供的步骤用于排查在下载和执行用于文件恢复的脚本时可能遇到的常见问题。

### <a name="you-cant-download-the-script"></a>无法下载脚本

1. 确保你具有[下载脚本所需的权限](./backup-azure-restore-files-from-vm.md#select-recovery-point-who-can-generate-script)。
1. 验证与 Azure 目标 IP 的连接。 在已提升权限的命令提示符下运行以下命令之一：

   `nslookup download.microsoft.com`

    或

    `ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>脚本下载成功，但无法运行

在 SUSE Linux Enterprise Server 12 SP4 上运行用于项级恢复 (ILR) 的 Python 脚本时，脚本会失败，并显示错误“无法加载 iscsi_tcp 模块”或“找不到 iscsi_tcp_module”。

**可能的原因**：ILR 模块使用 iscsi_tcp 与备份服务建立 TCP 连接。 作为 SLES 12 SP4 版本的一部分，SUSE 从 open-iscsi 包中删除了 iscsi_tcp，因此 ILR 操作会失败。

**建议的操作**：SUSE 12 SP4 VM 不支持文件恢复脚本执行。 在较旧版本的 SUSE 12 SP4 上尝试还原操作。

### <a name="the-script-runs-but-the-connection-to-the-iscsi-target-failed"></a>脚本可以运行，但无法连接到 iSCSI 目标

你可能会看到“连接到目标时捕获到异常”错误消息。

1. 确保运行脚本的计算机满足[访问要求](./backup-azure-restore-files-from-vm.md#step-4-access-requirements-to-successfully-run-the-script)。
1. 验证与 Azure 目标 IP 的连接。 在已提升权限的命令提示符下运行以下命令之一：

   `nslookup download.microsoft.com`

   或

   `ping download.microsoft.com`
1. 确保有权访问 iSCSI 出站端口 3260。
1. 检查是否有防火墙或 NSG 阻止流量流向 Azure 目标 IP 或恢复服务 URL。
1. 确保防病毒软件未阻止脚本的执行。

### <a name="youre-connected-to-the-recovery-point-but-the-disks-werent-attached"></a>你已连接到恢复点，但磁盘未附加

请按照适合你的操作系统的步骤来解决此问题。

#### <a name="windows-file-recovery-fails-on-server-with-storage-pools"></a>Windows 文件恢复在具有存储池的服务器上失败

在 Windows Server 2012 R2 和 Windows Server 2016（具有存储池）上首次运行脚本时，存储池可能会以只读状态附加到 VM。

>[!Tip]
> 确保具有[符合要求的计算机来运行脚本](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

若要解决此问题，请手动分配对存储池的读写访问权限并附加虚拟磁盘：

1. 转到“服务器管理器” > “文件和存储服务” > “卷” > “存储池”   。

   ![显示了存储池选项的屏幕截图。](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. 在“存储池”窗口中，右键单击可用的存储池，然后选择“设置读写访问权限” 。

   ![屏幕截图，显示了存储池的右键菜单选项。](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 为存储池分配读写访问权限后，在“虚拟磁盘”部分单击右键，然后选择“附加虚拟磁盘”。

   ![屏幕截图，显示了虚拟磁盘的右键菜单选项。](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="linux-file-recovery-fails-to-auto-mount-because-the-disk-doesnt-contain-volumes"></a>Linux 文件恢复无法自动装载，因为磁盘未包含卷

执行文件恢复时，备份服务会检测卷并自动装载。 但是，如果备份的磁盘具有原始分区，则这些磁盘不会自动装载，你无法看到要恢复的数据磁盘。

若要解决此问题，请转到[从 Azure 虚拟机备份恢复文件](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)。

#### <a name="linux-file-recovery-fails-because-the-os-couldnt-identify-the-file-system"></a>Linux 文件恢复失败，因为 OS 无法识别文件系统。

运行文件恢复脚本时，无法附加数据磁盘。 你看到了“由于 OS 无法识别文件系统，未能装载以下分区”错误。

若要解决此问题，请检查是否使用第三方应用程序对该卷进行了加密。 如果已加密，则磁盘或 VM 不会在门户上显示为已加密。

1. 登录到已备份的 VM，并运行以下命令：

   `lsblk -f`

   ![屏幕截图，显示了用于列出成组设备的命令的结果。](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

1. 验证文件系统和加密。 如果卷已加密，则不支持文件恢复。 从 [Azure VM 备份的支持矩阵](./backup-support-matrix-iaas.md#support-for-file-level-restore)了解详细信息。

### <a name="disks-are-attached-but-the-volumes-arent-mounted"></a>磁盘已附加，但卷未装载

请按照适合你的操作系统的步骤来解决此问题。

#### <a name="windows"></a>Windows

运行适用于 Windows 的文件恢复脚本时，你看到“附加了 0 个恢复卷”消息。 但是，会在磁盘管理控制台中发现这些磁盘。

**可能的原因**：通过 iSCSI 附加卷时，检测到的某些卷进入脱机状态。 当 iSCSI 通道在 VM 和服务之间通信时，它将检测这些卷并将其联机，但不会装载它们。

   ![屏幕截图，显示了“附加了 0 个恢复卷”消息。](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

若要识别并解决此问题，请执行以下步骤：

>[!Tip]
>确保具有[符合要求的计算机来运行脚本](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

1. 在“cmd”窗口中，运行 diskmgmt 以打开“磁盘管理”。
1. 查找是否有任何附加磁盘。 在下面的示例中，磁盘 2 是一个附加磁盘。

   ![“磁盘管理”窗口的屏幕截图，显示了附加磁盘。](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. 右键单击“新建卷”，并选择“更改驱动器号和路径” 。

   ![屏幕截图，显示了有关附加磁盘的右键菜单选项。](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. 在“更改驱动器号或路径”窗口中，选择“分配以下驱动器号”，分配可用的驱动器，然后选择“确定”  。

   ![“更改驱动器号或路径”窗口的屏幕截图。](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 打开文件资源管理器以查看你选择的驱动器并浏览文件。

#### <a name="linux"></a>Linux

>[!Tip]
>确保具有[符合要求的计算机来运行脚本](./backup-azure-restore-files-from-vm.md#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

如果受保护的 Linux VM 使用 LVM 或 RAID 阵列，请按照[从 Azure 虚拟机备份恢复文件](./backup-azure-restore-files-from-vm.md#lvmraid-arrays-for-linux-vms)中的步骤操作。

### <a name="you-cant-copy-the-files-from-mounted-volumes"></a>无法从装载的卷复制文件

复制可能会失败，并出现错误“0x80070780:系统无法访问该文件。” 

请检查源服务器是否已启用磁盘重复数据删除功能。 如果已启用该功能，请确保还原服务器也在驱动器上启用了重复数据删除功能。 你可以不配置重复数据删除功能，这样就不会对还原服务器上的驱动器执行重复数据删除操作。

## <a name="next-steps"></a>后续步骤

- [从 Azure 虚拟机备份恢复文件和文件夹](backup-azure-restore-files-from-vm.md)