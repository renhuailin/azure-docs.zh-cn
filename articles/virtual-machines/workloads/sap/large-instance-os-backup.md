---
title: Azure 上的 SAP HANA（大型实例）的操作系统备份和还原 | Microsoft Docs
description: 为 Azure 上的 SAP HANA（大型实例）执行操作系统备份和还原
services: virtual-machines-linux
documentationcenter: ''
author: Ajayan1008
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: madhukan
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c624f2cdee61d138722632869901e2c59110b1a9
ms.sourcegitcommit: eda26a142f1d3b5a9253176e16b5cbaefe3e31b3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/11/2021
ms.locfileid: "109735111"
---
# <a name="os-backup-and-restore"></a>OS 备份和还原

本文档介绍执行操作系统文件级备份和还原的步骤。 此过程因某些参数（例如类型 I 或类型 II、修订版 3 或更高版本、位置等）而有所不同。请与 Microsoft 运营中心核对，以获取资源中这些参数的准确值。

## <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>修订版 3 标记的类型 II SKU 的操作系统备份和还原

以下信息介绍如何为修订版 3 的 HANA 大型实例的“类型 II SKU”执行操作系统文件级备份和还原。

>[!Important]
> 本文不适用于修订版 4 HANA 大型实例标记中的类型 II SKU 部署。 在修订版 4 HANA 大型实例标记中部署的类型 II HANA 大型实例单元的引导 LUN 可以使用存储快照进行备份，因为修订版 3 标记中的类型 I SKU 也是这样


>[!NOTE]
>OS 备份脚本使用预安装在服务器中的 ReaR 软件。  

Microsoft `Service Management` 团队完成预配后，默认情况下，为服务器配置两个备份计划来备份操作系统的文件系统级备份。 可以使用以下命令查看备份作业的计划：
```
#crontab –l
```
可以使用以下命令随时更改备份计划：
```
#crontab -e
```
### <a name="how-to-take-a-manual-backup"></a>如何执行手动备份？

已使用 cron job 计划操作系统文件系统备份。 但也可以手动执行操作系统文件级备份。 要执行手动备份，请运行以下命令：

```
#rear -v mkbackup
```
以下屏幕演示了示例手动备份：

![如何操作](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


### <a name="how-to-restore-a-backup"></a>如何还原备份？

可以从备份中还原整个备份或单个文件。 若要还原，请使用以下命令：

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
还原后，文件会在当前工作目录中恢复。

以下命令演示从备份文件 backup.tar.gz 还原文件 /etc/fstab
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>从备份中还原文件后，需要将文件复制到所需位置。

以下屏幕截图显示完整还原备份：

![屏幕截图显示包含还原的命令提示符窗口。](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

### <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>如何安装 ReaR 工具并更改配置？ 

Relax-and-Recover (ReaR) 包预安装在 HANA 大型实例的类型 II SKU 中，你不需要执行任何操作。 可以直接开始使用 ReaR 进行操作系统备份。
但如果需要自行安装此包，则可以按照列出的步骤安装并配置 ReaR 工具。

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

以下信息介绍为除修订版 3 的 HANA 大型实例的“类型 II SKU”以外的所有修订版的所有 SKU 执行操作系统文件级备份和还原的步骤。

### <a name="how-to-take-a-manual-backup"></a>如何执行手动备份？

从 [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 获取最新的用于 SAP HANA 的 Microsoft 快照工具，并将其配置为通过带有 `--type=boot` 标志的 `crontab` 定期运行。 这将确保定期备份 OS。 下面的示例显示了 `/etc/crontab` 中类型 I SKU OS 备份的 cron 计划：

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeI --prefix=dailyboot --frequncy=15min --retention=28
```

下面的示例显示了 `/etc/crontab` 中类型 II SKU OS 备份的 cron 计划：

```
30 00 * * *  ./azure_hana_backup --type=boot --boottype=TypeII --prefix=dailyboot --frequency=15min --retention=28
```

其他参考：
- [设置存储快照](hana-backup-restore.md#set-up-storage-snapshots)
- [GitHub](https://github.com/Azure/hana-large-instances-self-service-scripts/blob/master/latest/release.md) 上的“用于 SAP HANA 的 Microsoft 快照工具”指南。

### <a name="how-to-restore-a-backup"></a>如何还原备份？

无法从 OS 本身完成还原操作。 请针对此问题向 Microsoft 运营中心提交支持工单。 还原操作要求 HLI 实例处于关闭状态，因此请进行相应的计划。

### <a name="managed-os-snapshots"></a>托管 OS 快照

Azure 可以自动为你的 HLI 资源进行 OS 备份。 这些备份每天进行一次，Azure 会保留最新的三个此类备份。 默认情况下，将为以下区域中的所有客户启用此功能：
- 美国西部
- 澳大利亚东部
- 澳大利亚东南部
- 美国中南部
- 美国东部 2

此功能在以下区域中部分可用：
- 美国东部
- 北欧
- 西欧

无法更改此设备所执行的备份的频率或保持期。 如果你的 HLI 资源需要不同的 OS 备份策略，则可以选择退出此设备，方法是向 Microsoft 运营中心提交支持工单，然后使用本文档上一部分中提供的说明，配置用于 SAP HANA 的 Microsoft 快照工具以执行 OS 备份。