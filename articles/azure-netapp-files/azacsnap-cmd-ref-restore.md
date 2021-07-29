---
title: 使用 Azure NetApp 文件的 Azure 应用程序一致快照工具进行还原 | Microsoft Docs
description: 提供有关运行可用于 Azure NetApp 文件的 Azure 应用程序一致快照工具的还原命令的指南。
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
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: df292fc59d46d59c94f43dae0b1827c068b3e057
ms.sourcegitcommit: bd1a4e4df613ff24e954eb3876aebff533b317ae
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/23/2021
ms.locfileid: "107929860"
---
# <a name="restore-using-azure-application-consistent-snapshot-tool"></a>使用 Azure 应用程序一致快照工具进行还原

本文提供有关运行可用于 Azure NetApp 文件的 Azure 应用程序一致快照工具的还原命令的指南。

## <a name="introduction"></a>简介

从快照执行卷还原是使用 `azacsnap -c restore` 命令来完成的。

> [!IMPORTANT]
> 这样不会执行数据库恢复，而是只还原卷，如以下每个选项的说明所述。

## <a name="command-options"></a>命令选项

`-c restore` 命令的选项如下：

- `--restore snaptovol` 基于目标卷上最新的快照创建新卷。 此命令基于配置的目标卷创建新的“克隆”卷，使用最新的卷快照作为创建该新卷的基础。  此命令并不会中断从主站点到辅助站点的存储复制。 而是会在 DR 站点创建最新可用快照的克隆，并且会提供建议克隆卷使用的文件系统装载点。 此命令应在 DR 区域中的 Azure 大型实例系统（即目标故障转移系统）上运行。

- `--restore revertvolume` 根据最新的快照将目标卷还原到先前的状态。  使用此命令作为 DR 故障转移到配对的 DR 区域中的一部分。 此命令会停止从主站点到辅助站点的存储复制，并将目标 DR 卷恢复到它们在 DR 卷上最新的可用快照，还会提供建议已还原的 DR 卷使用的文件系统装载点。 此命令应在 DR 区域中的 Azure 大型实例系统（即目标故障转移系统）上运行。
    > [!NOTE]
    > 子命令 (`--restore revertvolume`) 仅适用于 Azure 大型实例，不适用于 Azure NetApp 文件。
- `--dbsid <SAP HANA SID>` 是从配置文件中选择的要将卷还原命令应用到的 SAP HANA SID。

- `[--configfile <config filename>]` 是可选参数，可用于自定义配置文件名称。

## <a name="perform-a-test-dr-failover-azacsnap--c-restore---restore-snaptovol"></a>执行测试 DR 故障转移 `azacsnap -c restore --restore snaptovol`

此命令类似于“完全”的 DR 故障转移命令 (`--restore restorevolume`)，但不会中断主站点与灾难恢复站点之间的复制，而是会从灾难恢复卷中创建克隆卷，允许还原 DR 站点中的最新快照。 然后，客户可使用这些克隆的卷来测试灾难恢复，而无需对这些卷的 HANA 环境（中断主站点和灾难恢复站点之间的复制协议）执行完整的故障转移。

- 可以通过这种方式测试多个不同的还原点，每次测试都使用它们自己的还原点。
- 在执行命令时，会按时间戳来指定克隆，并且克隆表示在运行时可用的最新数据和其他快照。

> [!IMPORTANT]
> 此操作仅适用于 Azure 大型实例。
>
> - 此命令在执行时需要操作的联系人电子邮件，以便在 4 周后删除克隆之前与之联系。
> - 此命令的每次执行都将创建新的克隆，该克隆在测试结束后必须由 Microsoft Operations 删除。
> - 任何已创建的克隆卷在 4 周后都会被自动删除。

配置文件（例如 `DR.json`）应该只包含 DR 卷，而不包含生产卷，否则，生产卷可能会创建克隆。

### <a name="output-of-the-azacsnap--c-restore---restore-snaptovol-command-for-single-node-scenario"></a>`azacsnap -c restore --restore snaptovol`命令的输出（用于单一节点方案）

```output
> azacsnap --configfile DR.json -c restore --restore snaptovol --dbsid H80
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
> “按卷显示装入点”输出在各种不同情况下都是不同的。

## <a name="perform-full-dr-failover-azacsnap--c-restore---restore-revertvolume"></a>执行完全 DR 故障转移 `azacsnap -c restore --restore revertvolume`

此命令停止从主站点到辅助站点的存储复制，还原 DR 卷上最新的快照，并为 DR 卷提供装载点。

此命令必须使用只有 DR 卷的配置文件（例如 `DR.json`）在 DR 服务器上执行！

通过执行 `azacsnap -c restore --restore revertvolume` 命令来执行到 DR 站点的故障转移。  此命令需要一个要作为参数添加的 SID。 这是需要在 DR 站点恢复的 HANA 实例的 SID。

> [!IMPORTANT]
> 请只在计划执行 DR 练习或测试时运行此命令。 此命令会中断复制。 必须联系 Microsoft Operations 才能重新启用复制。

下面总结一下执行 DR 故障转移的步骤：

- 必须在主站点关闭 HANA 实例。 只有在为了避免数据不一致而确实要对 DR 站点进行故障转移时才需要执行此操作。
- 关闭生产 SID 的 DR 节点上的 HANA 实例。
- 在具有要恢复的 SID 的 DR 节点上执行 `azacsnap -c restore --restore revertvolume` 命令。
  - 该命令会中断从主站点到 DR 站点的存储复制链接
  - 该命令会按配置还原“数据”卷和“其他”卷。  通常，此操作用于 `/hana/data` 和 `/hana/logbackups` 文件系统的卷。  `/hana/shared` 文件系统不会恢复，而是会将现有的 `/hana/shared` 用于 DR 位置的 SID。
  - 装载 `/hana/data` 和 `/hana/logbackups` 卷 – 确保将这些卷添加到 `/etc/fstab` 文件
- 还原 HANA SYSTEMDB 快照。 HANA 工作室只显示在执行快照命令 `azacsnap -c restore --restore revertvolume` 的过程中还原的存储快照下可用的最新 HANA 快照。
- 恢复租户数据库。
- 启动生产 SID 的 DR 站点上的 HANA 实例（示例：在本例中为 H80）。
- 执行任何数据库测试。

## <a name="next-steps"></a>后续步骤

- [进行备份](azacsnap-cmd-ref-backup.md)
- [获取快照详细信息](azacsnap-cmd-ref-details.md)
