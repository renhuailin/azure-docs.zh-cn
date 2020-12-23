---
title: 使用适用于 Azure NetApp 文件 Azure 应用程序一致性快照工具进行还原 |Microsoft Docs
description: 提供有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的 restore 命令的指南。
services: azure-netapp-files
documentationcenter: ''
author: Phil-Jensen
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: reference
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 1c6b7ec6c4ef24ec00fbfc55a65a968e00561c2e
ms.sourcegitcommit: 8c3a656f82aa6f9c2792a27b02bbaa634786f42d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/17/2020
ms.locfileid: "97632588"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 应用程序一致性快照工具进行还原 (预览版) 

本文提供了有关运行可与 Azure NetApp 文件一起使用的 Azure 应用程序一致性快照工具的 restore 命令的指南。

## <a name="introduction"></a>简介

使用命令从快照执行卷还原 `azacsnap -c restore` 。

> [!IMPORTANT]
> 这不会执行数据库恢复，只会按下面的每个选项中所述) 还原卷 (。

## <a name="command-options"></a>命令选项

`-c restore`命令包含以下选项：

- `--restore snaptovol` 基于目标卷上的最新快照创建新卷。 此命令基于配置的目标卷创建新的 "克隆" 卷，并使用最新的卷快照作为创建新卷的基础。  此命令不会中断从主数据库到辅助副本的存储复制。 相反，会在 DR 站点上创建最新的可用快照的克隆，并提供克隆卷的建议文件系统装载点。 此命令应在 **DR 区域中** 的 Azure 大型实例系统上运行， (即目标故障转移系统) 。

- `--restore revertvolume` 根据最新的快照，将目标卷恢复到先前的状态。  使用此命令作为 DR 故障转移的一部分加入到配对的 DR 区域中。 此命令 **停止** 从主站点到辅助站点的存储复制，并将目标 dr 卷 () s 恢复到 DR 卷上的最新可用快照，并将建议的 filesystem 装载点用于恢复的 dr 卷。 此命令应在 **DR 区域中** 的 Azure 大型实例系统上运行， (即目标故障转移系统) 。
    > [!NOTE]
    > 子命令 (`--restore revertvolume`) 仅适用于 Azure 大型实例，不适用于 Azure NetApp 文件。
- `--hanasid <SAP HANA SID>` 要从配置文件中选择要将卷还原命令应用于的 SAP HANA SID。

- `[--configfile <config filename>]` 是允许自定义配置文件名称的可选参数。

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>执行测试 DR 故障转移 `azacsnap -c restore --restore snaptovol`

此命令类似于 "完全" DR 故障转移命令 (`--restore restorevolume`) ，但不会中断主站点与灾难恢复站点之间的复制，而是从灾难恢复卷中创建克隆卷，以允许还原 DR 站点中的最新快照。 这样，客户就可以使用这些克隆的卷来测试灾难恢复，而无需对主站点和灾难恢复站点之间的复制协议产生完全故障转移。

- 可以通过这种方式测试多个不同的还原点，每个还原点都有自己的还原点。
- 在执行命令时，将由时间戳指定克隆，并表示运行时可用的最新数据和其他快照。

> [!IMPORTANT]
> 此操作仅适用于 Azure 大型实例。
>
> - 当执行此命令时，它需要在4周后删除克隆之前，联系电子邮件以便与 liaise 进行操作。
> - 此命令的每次执行都将创建一个新的克隆，该克隆在测试结束后必须由 Microsoft 操作删除。
> - 4周后，创建的任何克隆卷都将自动删除。

例如，)  (配置文件 `DR.json` 只包含 DR 卷，而不包含生产卷，否则生产卷可能会创建克隆。

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol`Single-Node 方案的命令 (的输出) 

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --hanasid H80
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to simulate a Disaster Recovery
  failover without actually requiring a failover and subsequent failback.
* Any other restore points must be handled by Microsoft Operations.
* As part of the process, a clone is created of the each of the 'data' and 'other'
  volumes per the configuration file.

Do you wish to continue? (y/n) [n]: y

About to create clones of volumes based on the latest snapshot, these will be
kept for 4 weeks before being automatically deleted by Microsoft Operations.
Enter an email address to contact when deleting clones: <b>person@nowhere.com</b>
Checking state of HLI volumes for SID 'PEW'
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Configured volumes (Data and Other) are not ready to clone, will retry in 00:00:10 seconds
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_sapprdhdb80_mnt00001_t020_xdp_rwclone_20200916_0256  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_sapprdhdb80_t020_xdp_rwclone_20200916_0256  /hana/log_backups/H80/01 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*******************  HANA Test DR Restore Steps  ******************************
* Complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
*  These snapshot copies (clones) are kept for 4 weeks before                  *
*  being automatically removed.                                                *
*  Please contact Microsoft Operations to delete them earlier.                 *
********************************************************************************
```

> [!IMPORTANT]
> "按卷显示装入点" 输出在各种情况下都是不同的。

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>执行完全 DR 故障转移 `azacsnap -c restore --restore revertvolume`

此命令 **停止** 从主站点到辅助站点的存储复制，还原 dr 卷上最新的快照，并为 dr 卷提供装载点。

必须使用配置文件在 DR 服务器上执行此命令 (例如， `DR.json` 仅限 dr 卷) ！

执行命令，执行到 DR 站点的故障转移 `azacsnap -c restore --restore revertvolume` 。  此命令需要一个作为参数添加的 SID。 这是 HANA 实例的 SID，需要在 DR 站点上恢复。

> [!IMPORTANT]
> 仅当计划执行 DR 练习或测试时才运行此命令。 此命令会断开复制。 必须联系 Microsoft 操作以重新启用复制。

在高级别中，执行 DR 故障转移的步骤如下：

- 你必须在 **主** 站点上关闭 HANA 实例。 仅当你确实要将故障转移到 DR 站点，以避免数据不一致时，才需要执行此操作。
- 关闭生产 SID 的 DR 节点上的 HANA 实例。
- `azacsnap -c restore --restore revertvolume`在 DR 节点上执行命令，并在其中恢复 SID。
  - 此命令会将存储复制链接从主站点中断到 DR 站点
  - 该命令还原已配置的 "数据" 和 "其他" 卷。  通常，此操作适用于 `/hana/data` 和 `/hana/logbackups` 文件系统的卷。  `/hana/shared`不会恢复该文件系统，而是使用 `/hana/shared` DR 位置的现有 for SID。
  - 装载 `/hana/data` 和 `/hana/logbackups` 卷–确保将其添加到 `/etc/fstab` 文件
- 还原 HANA SYSTEMDB 快照。 仅在执行快照命令时，将在已还原的存储快照中显示最新的 HANA 快照 `azacsnap -c restore --restore revertvolume` 。
- 恢复租户数据库。
- 在 DR 站点上启动生产 SID 的 HANA 实例 (示例： H80 在本例中为) 。
- 执行任何数据库测试。

## <a name="next-steps"></a>后续步骤

- [进行备份](azacsnap-cmd-ref-backup.md)
- [获取快照详细信息](azacsnap-cmd-ref-details.md)
