---
title: 使用适用于 Azure NetApp 文件 Azure 应用程序一致性快照工具进行灾难恢复 |Microsoft Docs
description: 介绍如何在使用可与 Azure NetApp 文件一起使用 Azure 应用程序一致快照工具时执行灾难恢复。
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
ms.topic: how-to
ms.date: 12/14/2020
ms.author: phjensen
ms.openlocfilehash: 70e1823b30814d7dc29fef69215fcb53a2a2ab96
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 01/23/2021
ms.locfileid: "98730864"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool-preview"></a>使用 Azure 应用程序一致性快照工具进行灾难恢复 (预览版) 

本文介绍如何在使用可与 Azure NetApp 文件一起使用 Azure 应用程序一致快照工具时执行灾难恢复。

> [!IMPORTANT]
> 此操作仅适用于 **Azure 大型实例** 。

## <a name="introduction"></a>简介

Azure 大型实例平台还可以有一个配置为可以将存储卷快照复制到的灾难恢复站点。  如果使用此类设置正确配置了快照，则可以在此站点上执行灾难恢复。  本文档旨在提供此安装程序的灾难恢复指南。

## <a name="prerequisites-for-disaster-recovery-setup"></a>灾难恢复设置的先决条件

在规划灾难恢复故障转移之前，必须先满足以下先决条件。

- 在 DR 站点上预配了一个 DR 节点。 DR 有两个选项。 一个是正常 DR，另一个是多用途 DR。
- 你的存储复制工作正常。 Microsoft 运营团队会自动在 DR 预配时执行存储复制设置。 你可以使用 DR 站点上的命令监视存储复制 `azacsnap -c details --details replication` 。
- 已在主位置设置并配置了存储快照。
- 你在 DR 站点上安装了一个 HANA 实例，该实例具有与主实例相同的 SID。
- 阅读并了解在[Azure 上 SAP HANA 大型实例高可用性和灾难恢复](../virtual-machines/workloads/sap/hana-failover-procedure.md)中所述的 DR 故障转移过程
- 已在 DR 位置设置和配置了存储快照。
- 配置文件 (例如， `DR.json` 使用 dr 服务器上的 dr 存储卷和相关信息创建) 。
- 你已完成灾难恢复站点中的步骤，以便：
  - 启用与存储的通信。
  - 启用与 SAP HANA 的通信。

## <a name="set-up-disaster-recovery"></a>设置灾难恢复

Microsoft 支持灾难恢复的存储级别复制。 可以通过两种方式来设置 DR。

一个是 **正常** 的，另一个是多 **用途**。 在 **普通** dr 中，你在 dr 位置有一个专用实例用于故障转移。 在多 **用途** 灾难恢复方案中，在 DR 站点上有一个在 HANA 大型实例单元上运行的另一个 QA 或开发 HANA 实例。 但你还安装了一个处于休眠状态的预安装 HANA 实例，该实例与要故障转移到该 HANA 大型实例单元的 HANA 实例具有相同的 SID。 Microsoft 操作根据服务请求表单中提供的输入来设置环境，以便在载入时 (SRF) 。

> [!IMPORTANT]
> 确保灾难恢复设置满足所有先决条件。

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>监视从主站点到灾难恢复站点的数据复制

Microsoft 操作团队已经管理并监视从主站点到 DR 站点的 DR 链接。
可以使用 snapshot 命令监视从主服务器到 DR 服务器的数据复制 `azacsnap -c details --details replication` 。

## <a name="perform-a-failover-to-dr-site"></a>执行到 DR 站点的故障转移

在 DR 站点 () 运行故障转移命令 `azacsnap -c restore --restore revertvolume` 。

> [!IMPORTANT]
> 此 `azacsnap -c restore --restore revertvolume` 命令会将从生产站点到灾难恢复站点的存储复制中断。 必须与 Microsoft 操作联系，才能重新设置复制。 重新启用复制后，此 SID 的 DR 存储中的所有数据都将被初始化。 执行故障转移的命令使你可以使用最近复制的存储快照。 如果需要还原到较旧的快照，请打开支持请求，以便操作可以帮助在 DR 站点中提供先前还原的快照。

从较高层次来看，需要执行以下步骤来执行 DR 故障转移：

- 你必须在 **主** 站点上关闭 HANA 实例。 仅当你确实要将故障转移到 DR 站点，这样就不会出现数据不一致时，才需要执行此操作。
- 关闭生产 SID 的 DR 节点上的 HANA 实例。
- `azacsnap -c restore --restore revertvolume`在 DR 节点上执行命令，并在其中恢复 SID
  - 此命令会将存储复制链接从主站点中断到 DR 站点
  - 此命令只还原/data sources 和/logbackups 卷，不恢复/shared 卷，而是使用 DR 位置的现有/shared for SID。
  - 装入/data sources 和/logbackups 卷–确保将其添加到 fstab 文件
- 还原 HANA SYSTEMDB 快照。 HANA studio 仅向你显示在作为命令执行过程中还原的存储快照下可用的最新 HANA 快照 `azacsnap -c restore --restore revertvolume` 。
- 恢复租户数据库。
- 在 DR 站点上启动生产 SID 的 HANA 实例 (示例： H80 在本例中为) 。
- 执行测试。

### <a name="example-performing-disaster-recovery"></a>执行灾难恢复的示例

本小节介绍故障转移到灾难恢复站点的详细步骤。

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>步骤1：获取 DR 节点的卷详细信息

执行命令 `df –h` 以列出故障转移后要引用的文件系统和关联的卷。

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0%
/dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0%
/sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>步骤2：在主站点上关闭 HANA

如果执行生产工作负荷的完全故障转移，并且可以连接到主生产站点，请关闭) 故障转移到 DR 的 SAP HANA 实例 (。

例如，如果以 root 身份登录，下面的示例演示如何可以关闭 SAP HANA。  <sid>将替换为 SAP HANA SID。

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>步骤3：在 DR 站点上关闭 HANA

在恢复卷之前，请务必关闭 DR 站点上的 SAP HANA。

例如，如果以 root 身份登录，下面的示例演示如何可以关闭 SAP HANA。  <sid>将替换为 SAP HANA SID。

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> 请确保 DR 站点上的 HANA 实例处于脱机状态，然后再还原任何卷。

#### <a name="step-4-restore-the-volumes"></a>步骤4：还原卷

```bash
azacsnap -c restore --restore revertvolume --hanasid H80
```

**_DR 故障转移命令的输出_**。

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --hanasid H80
```

```output
* This program is designed for those customers who have previously installed the
  Production HANA instance in the Disaster Recovery Location either as a
  stand-alone instance or as part of a multi-purpose environment.
* This program should be executed from the Disaster Recovery location otherwise
  unintended consequences may result.
* This program is intended to allow the customer to complete a Disaster Recovery
  failover.
* Any other restore points must be handled by Microsoft Operations.
* All volumes ('data' and 'other') are reverted to their most recent snapshot.
* The SnapMirror replication relationship between Prod and DR will be broken.

  CAUTION: a failback will be required after running this command and failback
   might not be a quick process and will require multiple steps in coordination
   with Microsoft Operations.

Do you wish to continue? (y/n) [n]: y
Checking state of HLI volumes for SID 'H80'
Configured volumes (Data and Other) are not quiesced for revert, will retry in 00:00:10 seconds
Volumes All Ok to Revert = True
Reverting volume 'hana_data_h80_mnt00001_t020_xdp' to snapshot 'H80_HANA_DATA_30MIN.2020-09-16_0330.0'
DR.json Data Volume #1 'hana_data_h80_mnt00001_t020_xdp' assigning to mountpoint 'mnt00001'
Reverting volume 'hana_log_backups_h80_t020_xdp01' to snapshot 'H80_HANA_LOGS_3MIN_X9.2020-09-16_0339.recent'
DR.json Other Volume #1 'hana_log_backups_h80_t020_xdp01' assigning to mountpoint '01'
HLI Volume revert completed for SID 'H80'
Displaying Mount Points by Volume as follows:
10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
*********************  HANA DR Restore Steps  **********************************
* Please complete the following steps to recover your HANA database:           *
* 1. Ensure ALL the target mount points exist to mount the snapshot clones.    *
*    e.g. mkdir /hana/logbackups/H99_SOURCE                                    *
* 2. Add Mount Point Details from 'Displaying Mount Points by Volume' as       *
*    output above into /etc/fstab of DR Server.                                *
* 3. Mount newly added filesystems.                                            *
* 4. Perform HANA Snapshot Recovery using HANA Studio.                         *
********************************************************************************
```

> [!NOTE]
> 需要使用控制台显示结尾的步骤来完成灾难恢复故障转移的存储准备工作。

#### <a name="step-5-unmount-unnecessary-filesystems"></a>步骤5：卸载不必要的文件系统

执行命令 `umount` 以卸载不需要的文件系统/卷。

```bash
umount <Mount point>
```

卸载数据和日志备份装载点。 在扩展方案中，可能会有多个数据装入点。

#### <a name="step-6-configure-the-mount-points"></a>步骤6：配置装入点

修改该文件 `/etc/fstab` 以注释掉主 SID (的数据和日志备份条目。在此示例中，SID = H80) 并添加从主站点 DR 卷创建的新装入点条目。 命令输出中提供了新的装入点项。

- 注释掉在 DR 站点上运行的现有装入点，其中包含 `#` 以下字符：

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- 将以下行添加到 `/etc/fstab`
  > 这应与命令中的输出相同

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>步骤7：装载恢复卷

执行命令 `mount –a` 以装入所有装入点。

```bash
mount -a
```

现在，如果执行， `df –h` 应会看到 `*_dp` 装载的卷。

```bash
df -h
```

```output
Filesystem Size Used Avail Use% Mounted on
devtmpfs 378G 8.0K 378G 1% /dev
tmpfs 569G 0 569G 0% /dev/shm
tmpfs 378G 18M 378G 1% /run
tmpfs 378G 0 378G 0% /sys/fs/cgroup
/dev/mapper/3600a098038304445622b4b584c575a66-part2 47G 20G 28G 42% /
/dev/mapper/3600a098038304445622b4b584c575a66-part1 979M 57M 856M 7% /boot
172.18.20.241:/hana_log_h80_mnt00003_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00001_t020_vol 512G 5.5G 507G 2% /hana/log/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00003_t020_vol 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_h80_mnt00002_t020_vol 512G 2.1G 510G 1% /hana/log/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00002_t020_vol 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00001_t020_vol 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_shared_h80_t020_vol/usr_sap_node1 2.7T 11G 2.7T 1% /usr/sap/H80
tmpfs 76G 0 76G 0% /run/user/0
172.18.20.241:/hana_shared_h80_t020_vol 2.7T 11G 2.7T 1% /hana/shared
172.18.20.241:/hana_data_h80_mnt00001_t020_xdp 1.2T 6.4G 1.2T 1% /hana/data/H80/mnt00001
172.18.20.241:/hana_data_h80_mnt00002_t020_xdp 1.2T 300M 1.2T 1% /hana/data/H80/mnt00002
172.18.20.241:/hana_data_h80_mnt00003_t020_xdp 1.2T 332M 1.2T 1% /hana/data/H80/mnt00003
172.18.20.241:/hana_log_backups_h80_t020_xdp 512G 15G 498G 3% /hana/logbackups/H80_T250
```

#### <a name="step-8-recover-the-systemdb"></a>步骤8：恢复 SYSTEMDB

在 HANA Studio 中，右键单击 "SYSTEMDB 实例"，然后选择 "备份和恢复"，然后单击 "恢复系统数据库"

请参阅从快照中恢复数据库的指南，尤其是 SYSTEMDB。

#### <a name="step-9-recover-the-tenant-database"></a>步骤9：恢复租户数据库

在 HANA Studio 中，右键单击 "SYSTEMDB 实例"，然后选择 "备份和恢复"，然后选择 "恢复租户数据库"。

请参阅从快照中恢复数据库的指南，尤其是租户数据库 (s) 。

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>`azacsnap -c backup`在 DR 站点上运行

如果在 DR 站点上运行基于快照的备份，则在 DR 站点的配置文件中配置的 HANA 服务器名称 `azacsnap` 应与生产服务器名称相同。

> [!IMPORTANT]
> 运行 `azacsnap -c backup` 可以在 DR 站点创建存储快照，这些快照不会自动复制到其他站点。  使用 Microsoft 操作来更好地了解将任何文件或数据返回到原始生产站点。

## <a name="next-steps"></a>后续步骤

- [获取快照详细信息](azacsnap-cmd-ref-details.md)
- [进行备份](azacsnap-cmd-ref-backup.md)
