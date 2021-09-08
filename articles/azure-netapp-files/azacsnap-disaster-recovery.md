---
title: 使用 Azure NetApp 文件的 Azure 应用程序一致性快照工具进行灾难恢复 | Microsoft Docs
description: 说明在使用可用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具时如何执行灾难恢复。
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
ms.date: 04/21/2021
ms.author: phjensen
ms.openlocfilehash: 1fbf8052a3852e010fc002116cab131ea4f371b8
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123438095"
---
# <a name="disaster-recovery-using-azure-application-consistent-snapshot-tool"></a>使用 Azure 应用程序一致性快照工具进行灾难恢复

本文介绍在使用可用于 Azure NetApp 文件的 Azure 应用程序一致性快照工具时如何执行灾难恢复。

> [!IMPORTANT]
> 此操作仅适用于 Azure 大型实例。

## <a name="introduction"></a>简介

Azure 大型实例平台也可以配置灾难恢复站点，从而将存储卷快照复制到那里。  如果已正确为快照配置这种设置，则可以在此站点上执行灾难恢复。  本文档旨在提供这种设置的灾难恢复指南。

## <a name="prerequisites-for-disaster-recovery-setup"></a>灾难恢复设置的先决条件

在规划灾难恢复故障转移之前，必须先满足以下先决条件。

- 在 DR 站点上已预配一个 DR 节点。 DR 有两个选项。 其一为正常 DR，另一个是多用途 DR。
- 存储复制工作正常。 Microsoft 运营团队会在预配 DR 时自动执行存储复制设置。 可以使用 DR 站点上的命令 `azacsnap -c details --details replication` 监视存储复制。
- 已在主位置设置并配置存储快照。
- 已在 DR 站点上为主位置安装 HANA 实例，其 SID 与主实例的 SID 相同。
- 阅读并了解“[Azure 上的 SAP HANA 大型实例高可用性和灾难恢复](../virtual-machines/workloads/sap/hana-failover-procedure.md)”中所述的 DR 故障转移过程
- 已在 DR 位置设置并配置存储快照。
- 已在 DR 服务器上创建配置文件（例如 `DR.json`），其中包含 DR 存储卷和相关信息。
- 已在 DR 站点上完成以下任务的相关步骤：
  - 启用与存储的通信。
  - 启用与 SAP HANA 的通信。

## <a name="set-up-disaster-recovery"></a>设置灾难恢复

Microsoft 支持存储级别复制以执行 DR 恢复。 设置 DR 有两种方法。

其一为正常方式，另一种是多用途方式。  对于正常 DR，在 DR 位置有一个专用实例用于故障转移。 对于多用途 DR 方案，DR 站点上的 HANA 大型实例单元中运行着另一个 QA 或开发 HANA 实例。 同时，你已安装预安装 HANA 实例，它处于休眠状态并与你想要故障转移到 HANA 大型实例单元的 HANA 实例具有相同的 SID。 Microsoft 运营团队根据载入时服务请求表 (SRF) 中提供的信息为你设置环境，包括存储复制。

> [!IMPORTANT]
> 确保满足 DR 设置的所有先决条件。

## <a name="monitor-data-replication-from-primary-to-dr-site"></a>监视从主站点到 DR 站点的数据复制

Microsoft 运营团队会始终管理和监视从主站点到 DR 站点的 DR 链接。
你可以使用快照命令 `azacsnap -c details --details replication` 监视从主服务器到 DR 服务器的数据复制。

## <a name="perform-a-failover-to-dr-site"></a>执行故障转移到 DR 站点

在 DR 站点上运行故障转移命令 (`azacsnap -c restore --restore revertvolume`)。

> [!IMPORTANT]
> `azacsnap -c restore --restore revertvolume` 命令会中断从生产站点到 DR 站点的存储复制。 必须联系 Microsoft 运营团队，才能再次设置复制。 重新启用复制后，此 SID 的所有 DR 存储数据都将进行初始化。 执行故障转移的命令将使用最近复制的存储快照。 如果需要还原到较旧的快照，请打开一个支持请求，以便操作协助提供 DR 站点中较早还原的快照。

下面是执行 DR 故障转移在较高层面要采取的步骤：

- 必须在主站点关闭 HANA 实例。 只有真正要故障转移到 DR 站点以免数据不一致时，才需要执行此操作。
- 关闭生产 SID DR 节点上的 HANA 实例。
- 在具有要恢复的 SID 的 DR 节点上执行 `azacsnap -c restore --restore revertvolume` 命令
  - 该命令会中断从主站点到 DR 站点的存储复制链接
  - 此命令只会还原 /data 和 /logbackups 卷，不会恢复 /shared 卷，而是对 DR 位置上的 SID 使用现有的 /shared。
  - 装载 /data 和 /logbackups 卷 – 确保将其添加到 fstab 文件
- 还原 HANA SYSTEMDB 快照。 HANA Studio 只显示在执行快照命令 `azacsnap -c restore --restore revertvolume` 时，还原的存储快照下可用的最新 HANA 快照。
- 恢复租户数据库。
- 在生产 SID 的 DR 站点上启动 HANA 实例（示例：在本例中为 H80）。
- 执行测试。

### <a name="example-performing-disaster-recovery"></a>执行灾难恢复的示例

本小节介绍故障转移到灾难恢复站点的详细步骤。

#### <a name="step-1-get-the-volume-details-of-the-dr-node"></a>步骤 1：获取 DR 节点的卷详细信息

执行命令 `df –h` 以列出故障转移后要引用的文件系统和关联卷。

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

#### <a name="step-2-shut-down-hana-on-the-primary-site"></a>步骤 2：在主站点上关闭 HANA

如果执行生产工作负载完全故障转移，并且可以连接到主生产站点，请关闭正在故障转移到 DR 的 SAP HANA 实例。

例如，如果以 root 身份登录，下面示例将演示如何关闭 SAP HANA。  将 \<sid\> 替换为你的 SAP HANA SID。

```bash
su - <sid>adm
HDB stop
```

#### <a name="step-3-shut-down-hana-on-dr-site"></a>步骤 3：关闭 DR 站点上的 HANA

在恢复卷之前，请务必关闭 DR 站点上的 SAP HANA。

例如，如果以 root 身份登录，下面示例将演示如何关闭 SAP HANA。  将 \<sid\> 替换为你的 SAP HANA SID。

```bash
su - <sid>adm
HDB stop
```

> [!IMPORTANT]
> 还原任何卷之前，请确保 DR 站点上的 HANA 实例已脱机。

#### <a name="step-4-restore-the-volumes"></a>步骤 4：还原卷

```bash
azacsnap -c restore --restore revertvolume --dbsid H80
```

**_DR 故障转移命令的输出_**。

```bash
azacsnap --configfile DR.json -c restore --restore revertvolume --dbsid H80
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
> 在控制台显示结束后，需要执行步骤来完成 DR 故障转移的存储准备。

#### <a name="step-5-unmount-unnecessary-filesystems"></a>步骤 5：卸载不必要的文件系统

执行命令 `umount` 以卸载不需要的文件系统/卷。

```bash
umount <Mount point>
```

卸载数据和日志备份装入点。 在横向扩展方案中，可能有多个数据装入点。

#### <a name="step-6-configure-the-mount-points"></a>步骤 6：配置装入点

修改文件 `/etc/fstab`，以注释禁止用于主 SID（在本例中 SID=H80）的数据和日志备份条目，并添加基于主站点 DR 卷创建的新装入点条目。 命令输出中将提供新的装入点条目。

- 注释禁止 DR 站点上运行的带 `#` 字符的现有装入点：

  ```output
  #172.18.20.241:/hana_data_h80_mnt00001_t020_vol /hana/data/H80/mnt00001 nfs     rw,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  #172.18.20.241:/hana_log_backups_h80_t020 /hana/logbackups/H80 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

- 将以下行添加到 `/etc/fstab`
  > 此输入应与命令的输出相同

  ```output
  10.50.251.34:/hana_data_h80_mnt00001_t020_xdp  /hana/data/H80/mnt00001 nfs  rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  10.50.251.36:/hana_log_backups_h80_t020_xdp01  /hana/log_backups/H80/01 nfs rw,bg,hard,timeo=600,vers=4,rsize=1048576,wsize=1048576,intr,noatime,lock 0 0
  ```

#### <a name="step-7-mount-the-recovery-volumes"></a>步骤 7：装载恢复卷

执行命令 `mount –a` 以装载所有装入点。

```bash
mount -a
```

现在，如果执行 `df –h`，应会看到已装载的 `*_dp` 卷。

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

#### <a name="step-8-recover-the-systemdb"></a>步骤 8：恢复 SYSTEMDB

在 HANA Studio 中，右键单击 SYSTEMDB 实例，选择“备份和恢复”，然后选择“恢复系统数据库”

请参阅指南以从快照恢复数据库（尤其是 SYSTEMDB）。

#### <a name="step-9-recover-the-tenant-database"></a>步骤 9：恢复租户数据库

在 HANA Studio 中，右键单击 SYSTEMDB 实例，选择“备份和恢复”，然后选择“恢复租户数据库”。

请参阅指南以从快照恢复数据库（尤其是 TENANT 数据库）。

### <a name="run-azacsnap--c-backup-at-the-dr-site"></a>在 DR 站点上运行 `azacsnap -c backup`

如果在 DR 站点上运行基于快照的备份，则在 DR 站点的 `azacsnap` 配置文件中配置的 HANA 服务器名称应与生产服务器名称相同。

> [!IMPORTANT]
> 运行 `azacsnap -c backup` 可以在 DR 站点上创建存储快照，这些快照不会自动复制到其他站点。  请咨询 Microsoft 运营团队，以更好地了解如何将任何文件或数据恢复到原始生产站点。

## <a name="next-steps"></a>后续步骤

- [获取快照详细信息](azacsnap-cmd-ref-details.md)
- [进行备份](azacsnap-cmd-ref-backup.md)
