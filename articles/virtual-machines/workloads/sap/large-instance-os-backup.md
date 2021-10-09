---
title: Azure 上的 SAP HANA（大型实例）的操作系统备份和还原 | Microsoft Docs
description: 了解如何为 Azure 上的 SAP HANA（大型实例）执行操作系统备份和还原。
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.subservice: baremetal-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 06/22/2021
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e7d2621aab6d49c8bd6f4b6083c35d5e7299bf50
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128630502"
---
# <a name="os-backup-and-restore"></a>OS 备份和还原

本文介绍用于执行操作系统 (OS) 文件级备份和还原的步骤。 该过程因类型 I 或类型 II、修订版 3 或更高版本、位置等参数而异。 请与 Microsoft 运营中心联系，以获取你的资源的这些参数值。

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>修订版 3 标记的类型 II SKU 的操作系统备份和还原

以下信息介绍如何为修订版 3 的 HANA 大型实例的“类型 II SKU”执行操作系统文件级备份和还原。

>[!Important]
> 本文不适用于修订版 4 HANA 大型实例标记中的类型 II SKU 部署。 修订版 4 HANA 大型实例标记中部署的类型 II HANA 大型实例的引导 LUN 可以使用存储快照进行备份，这对于修订版 3 标记中的类型 I SKU 也是一样。


>[!NOTE]
>OS 备份脚本使用预安装在服务器中的 ReaR 软件。  

Microsoft 服务管理团队完成预配后，默认情况下，将通过两次计划配置服务器来备份文件系统级 OS。 可以使用以下命令查看备份作业的计划：

```
#crontab –l
```
可以使用以下命令随时更改备份计划：
```
#crontab -e
```
### <a name="take-a-manual-backup"></a>创建手动备份

已使用 cron job 计划操作系统文件系统备份。 但也可以手动执行操作系统文件级备份。 要执行手动备份，请运行以下命令：

```
#rear -v mkbackup
```
以下屏幕演示了示例手动备份：

![如何操作](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="restore-a-backup"></a>还原备份

可以从备份中还原整个备份或单个文件。 若要还原，请使用以下命令：

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
还原后，文件会在当前工作目录中恢复。

以下命令演示从备份文件 backup.tar.gz 还原文件 /etc/fstab ：
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>从备份中还原文件后，需要将文件复制到所需位置。

以下屏幕截图演示了完整备份的还原。

![屏幕截图显示包含还原的命令提示符窗口。](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="install-the-rear-tool-and-change-the-configuration"></a>安装 ReaR 工具并更改配置 

Relax-and-Recover (ReaR) 包预安装在 HANA 大型实例的类型 II SKU 中 。 你无需采取任何措施。 可以直接开始使用 ReaR 工具进行操作系统备份。

但如果需要自行安装此包，可以使用以下步骤来安装并配置 ReaR 工具。

若要安装 ReaR 备份包，请使用以下命令：

对于 SLES 操作系统，请使用以下命令：
```
#zypper install <rear rpm package>
```
对于 RHEL 操作系统，请使用以下命令： 

```
#yum install rear -y
```
若要配置 ReaR 工具，需要在 /etc/rear/local.conf 文件中更新 OUTPUT_URL 和 BACKUP_URL。

```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

以下屏幕截图显示了完整还原备份：![屏幕截图显示了使用 ReaR 工具进行还原的命令提示符窗口。](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)


## <a name="os-backup-and-restore-for-all-other-skus"></a>为所有其他 SKU 执行 OS 备份和还原

以下信息介绍如何为所有修订版的所有 SKU（修订版 3 的 HANA 大型实例的“类型 II SKU”除外）执行操作系统文件级备份和还原。

### <a name="take-a-manual-backup"></a>创建手动备份

请按照以[什么是 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-introduction.md)开头的一系列文章中的说明，获取最新 Microsoft Snapshot Tools for SAP HANA。 按照以下文章中的说明对其进行配置和测试：

- [配置 Azure 应用程序一致的快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [测试 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-test.md) 

此评审将使你准备好按照[使用 Azure 应用程序一致性快照工具进行备份](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)中所述，通过 `crontab` 定期运行备份。 

有关详细信息，请参阅以下参考：

- [安装 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-installation.md)
- [配置 Azure 应用程序一致的快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-configure.md)
- [测试 Azure 应用程序一致性快照工具](../../../azure-netapp-files/azacsnap-cmd-ref-test.md)
- [使用 Azure 应用程序一致的快照工具进行备份](../../../azure-netapp-files/azacsnap-cmd-ref-backup.md)
- [使用 Azure 应用程序一致性快照工具获取详细信息](../../../azure-netapp-files/azacsnap-cmd-ref-details.md)
- [使用 Azure 应用程序一致性快照工具进行删除](../../../azure-netapp-files/azacsnap-cmd-ref-delete.md)
- [使用 Azure 应用程序一致快照工具进行还原](../../../azure-netapp-files/azacsnap-cmd-ref-restore.md)
- [使用 Azure 应用程序一致性快照工具进行灾难恢复](../../../azure-netapp-files/azacsnap-disaster-recovery.md)
- [排查 Azure 应用程序一致性快照工具问题](../../../azure-netapp-files/azacsnap-troubleshoot.md)
- [有关使用 Azure 应用程序一致性快照工具的提示和技巧](../../../azure-netapp-files/azacsnap-tips.md)


### <a name="restore-a-backup"></a>还原备份

无法从 OS 本身完成还原操作。 需要向 Microsoft 运营中心提交支持工单。 还原操作要求 HANA 大型实例 (HLI) 处于关闭状态，因此请进行相应的计划。

### <a name="managed-os-snapshots"></a>托管 OS 快照

Azure 可以自动为你的 HLI 资源进行 OS 备份。 这些备份每天进行一次，Azure 会保留最新的三个此类备份。 默认情况下，将为以下区域中的所有客户启用这些备份：
- 美国西部
- 澳大利亚东部
- 澳大利亚东南部
- 美国中南部
- 美国东部 2

此设备在以下区域中部分可用：
- 美国东部
- 北欧
- 西欧

无法更改此设备所执行的备份的频率或保持期。 如果 HLI 资源需要不同的 OS 备份策略，可以通过向 Microsoft 运营中心提交支持工单来选择退出此设备。 然后使用前面[进行手动备份](#take-a-manual-backup)部分中提供的说明，将 Microsoft Snapshot Tools for SAP HANA 配置为执行 OS 备份。

## <a name="next-steps"></a>后续步骤

了解如何为 HANA 大型实例启用 kdump。

> [!div class="nextstepaction"]
> [Azure SAP HANA 大型实例的 kdump](hana-large-instance-enable-kdump.md)
