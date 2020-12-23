---
title: 排查 Azure VM 文件恢复问题
description: 排查从 Azure VM 备份恢复文件和文件夹时遇到的问题。
ms.topic: troubleshooting
ms.date: 07/12/2020
ms.openlocfilehash: bd369577e320cf6dca510183948f41e6cf91779b
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605286"
---
# <a name="troubleshooting-issues-in-file-recovery-of-azure-vm-backup"></a>排查 Azure VM 备份文件恢复问题

本文提供故障排除步骤，这些步骤可帮助你解决在从 Azure VM 备份恢复文件和文件夹时遇到的与问题相关的 Azure 备份错误。

## <a name="common-error-messages"></a>常见错误消息

### <a name="exception-caught-while-connecting-to-target"></a>连接到目标时捕获到异常

**可能的原因**：脚本无法访问恢复点。

**建议的操作**：检查计算机是否满足所有 [访问要求](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)。

### <a name="the-target-has-already-been-logged-in-via-an-iscsi-session"></a>目标已经通过 iSCSI 会话登录

**可能的原因**：该脚本已在同一台计算机上执行，并且已附加驱动器。

**建议的操作**：已附加恢复点的卷。 不能使用与原始 VM 相同的驱动器号装载这些卷。 在文件资源管理器中浏览所有可用卷。

### <a name="this-script-is-invalid-because-the-disks-have-been-dismounted-via-portalexceeded-the-12-hr-limit-download-a-new-script-from-the-portal"></a>此脚本无效，因为磁盘已通过门户卸载/已超过 12 小时限制。 从门户下载新脚本

**可能的原因**：磁盘已从门户卸除或超过了12小时限制。

**建议的操作**：脚本在下载后的12小时后无效，无法执行。 访问门户并下载新的脚本，以继续进行文件恢复。

## <a name="troubleshooting-common-scenarios"></a>排查常见方案的问题

本部分介绍了在下载和执行用于文件恢复的脚本时可能遇到的问题的步骤。

### <a name="cant-download-the-script"></a>无法下载该脚本

建议的操作：

1. 确保你具有 [下载该脚本所需](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#select-recovery-point-who-can-generate-script)的所有权限。
1. 确保与 Azure 目标 Ip 建立连接

若要验证连接，请从提升的命令提示符运行以下命令之一。

`nslookup download.microsoft.com`

或

`ping download.microsoft.com`

### <a name="the-script-downloads-successfully-but-fails-to-run"></a>脚本下载成功，但无法运行

#### <a name="for-sles-12-sp4-os-linux"></a>对于 SLES 12 SP4 操作系统 (Linux) 

**错误**：找不到 iscsi_tcp_module

**可能的原因**：在 SUSE Linux OS 版本12sp4 上运行项目级恢复)  (的 Python 脚本时，它会失败，并出现错误 **_iscsi_tcp 模块无法加载_*。

ILR 模块使用 _ *iscsi_tcp** 与备份服务建立 tcp 连接。 作为12SP4 版本的一部分，SUSE 从开放 iscsi 包中删除 **iscsi_tcp** ，因此 ILR 操作将失败。

**建议的操作**： SUSE 12SP4 vm 不支持文件恢复脚本执行。 在较旧版本的 SUSE 12SP4 上尝试还原操作。

### <a name="the-script-runs-but-connection-to-iscsi-target-failed"></a>脚本运行失败，但无法连接到 iSCSI 目标

**错误**：连接到目标时捕获到异常

1. 确保运行脚本的计算机满足所有 [访问要求](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-4-access-requirements-to-successfully-run-the-script)。
1. 检查与 Azure 目标 Ip 的连接。
若要验证连接，请从提升的命令提示符运行以下命令之一。

   `nslookup download.microsoft.com`

   或

   `ping download.microsoft.com`
1. 确保有权访问 iSCSI 出站端口3260。
1. 确保没有防火墙或 NSG 阻止流量流向 Azure 目标 Ip 或恢复服务 Url。
1. 检查防病毒是否正在阻止脚本的执行。

### <a name="connected-to-recovery-point-but-disks-didnt-get-attached"></a>已连接到恢复点，但未附加磁盘

确保你有 [正确的计算机来运行该脚本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)

#### <a name="on-a-windows-vm"></a>在 Windows VM 上

**VM 上的存储池以只读模式附加**：在使用存储) 池的 Windows 2012 R2 和 windows 2016 (上，首次运行脚本时，附加到 VM 的存储池可能进入只读状态。

若要解决此问题，我们需要手动将 Read-Write 访问权限设置为首次存储池，并附加虚拟磁盘。 请遵循以下步骤进行配置：

1. 设置 Read-Write 访问。

   导航到 "**服务器管理器**  >  **文件和存储服务**" "卷" "  >    >  **存储池**"。

   ![Windows 存储](./media/backup-azure-restore-files-from-vm/windows-storage-1.png)

1. 在 " **存储池** " 窗口中，右键单击可用存储池，然后选择 " **设置 Read-Write 访问** " 选项。

   ![Windows 存储读取写入](./media/backup-azure-restore-files-from-vm/windows-storage-read-write-2.png)

1. 为存储池分配读/写访问权限后，附加该虚拟磁盘。

   导航到 **服务器管理器 UI**。 在 " **虚拟磁盘** " 部分下 > 右键单击以选择 " **附加虚拟磁盘** " 选项。

   ![服务器管理器虚拟磁盘](./media/backup-azure-restore-files-from-vm/server-manager-virtual-disk-3.png)

#### <a name="on-a-linux-vm"></a>在 Linux VM 上

##### <a name="file-recovery-fails-to-auto-mount-because-disk-doesnt-contain-volumes"></a>文件恢复无法自动装载，因为磁盘不包含卷

执行文件恢复时，备份服务会检测卷和 automounts。 但是，如果备份磁盘具有原始分区，则这些磁盘不 automounted，并且你无法看到数据磁盘进行恢复。

若要解决此问题，请按照 [本文](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)中所述的步骤进行操作。

##### <a name="the-os-couldnt-identify-the-filesystem-causing-linux-file-recovery-to-fail-while-mountings-disks"></a>在 mountings 磁盘时，OS 无法识别导致 Linux 文件恢复失败的文件系统

运行文件恢复脚本时，数据磁盘未能附加，出现以下错误：

"由于 OS 无法识别 filesystem，未能装载以下分区"

若要解决此问题，请检查是否使用第三方应用程序对该卷进行了加密。 如果已加密，则磁盘或 VM 不会在门户上显示为已加密。

1. 登录到已备份的 VM，并运行以下命令：

   `*lsblk -f*`

   ![没有卷的磁盘](./media/backup-azure-restore-files-from-vm/disk-without-volume-5.png)

2. 验证文件系统和加密。
3. 如果卷已加密，则不支持文件恢复。 [了解详细信息](https://docs.microsoft.com/azure/backup/backup-support-matrix-iaas#support-for-file-level-restore)。

### <a name="disks-are-attached-but-unable-to-mount-volumes"></a>磁盘已连接，但无法装入卷

- 确保你有 [正确的计算机来运行脚本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。

#### <a name="on-windows-vms"></a>在 Windows Vm 上

运行适用于 Windows 的文件恢复脚本时，会出现一条消息，其中包含 ***0 个卷**。 但是，在磁盘管理控制台中发现了这些磁盘。 通过 iSCSI 附加卷时，检测到的某些卷会进入脱机状态。 当 iSCSI 通道在 VM 和服务之间通信时，它将检测这些卷并将其联机，但不会装载它们。

   ![未附加磁盘](./media/backup-azure-restore-files-from-vm/disk-not-attached-6.png)

若要确定并解决此问题，请执行以下步骤：

1. 在 cmd 窗口中运行 **diskmgmt.msc** 命令，以中转到 "*磁盘管理*"。
1. 检查是否显示了其他磁盘。 在下面的示例中，磁盘2是附加磁盘。

   ![磁盘 management0](./media/backup-azure-restore-files-from-vm/disk-management-7.png)

1. 右键单击 **新卷** ，并选择 " **更改驱动器号和路径**"。

   ![磁盘 management1](./media/backup-azure-restore-files-from-vm/disk-management-8.png)

1. 在 " **添加驱动器号或路径** " 窗口中，选择 " **分配以下驱动器号** 并分配可用驱动器"，然后选择 **"确定"**。

   ![磁盘 management2](./media/backup-azure-restore-files-from-vm/disk-management-9.png)

1. 在文件资源管理器中，查看所选的驱动器号并浏览这些文件。

#### <a name="on-linux-vms"></a>在 Linux Vm 上

- 确保你有 [正确的计算机来运行脚本](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#step-2-ensure-the-machine-meets-the-requirements-before-executing-the-script)。
- 如果受保护的 Linux VM 使用 LVM 或 RAID 阵列，请按照 [本文](https://docs.microsoft.com/azure/backup/backup-azure-restore-files-from-vm#lvmraid-arrays-for-linux-vms)中列出的步骤进行操作。

### <a name="cant-copy-the-files-from-mounted-volumes"></a>无法从装载的卷复制文件

此复制可能会失败，并出现错误 **0x80070780：系统无法访问该文件。** 在这种情况下，请检查源服务器是否已启用磁盘重复删除。 然后，确保还原服务器还在驱动器上启用了删除重复。 您可以保留未配置的重复数据删除角色，以便不删除重复还原服务器上的驱动器。

## <a name="next-steps"></a>后续步骤

- [从 Azure 虚拟机备份恢复文件和文件夹](backup-azure-restore-files-from-vm.md)
