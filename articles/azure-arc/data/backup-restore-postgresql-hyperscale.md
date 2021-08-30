---
title: 为 Azure Database for PostgreSQL 超大规模服务器组进行备份和还原
description: 为 Azure Database for PostgreSQL 超大规模服务器组进行备份和还原
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: de908a8f29d85aea1954415ba5c9793349837c5f
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121733568"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>备份并还原已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

> [!IMPORTANT]
> 当前预览版中不支持备份和还原已启用 Azure Arc 的超大规模 PostgreSQL 服务器。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

在备份或还原已启用 Azure Arc 的超大规模 PostgreSQL 服务器组时，将备份和/或还原服务器组的所有 PostgreSQL 节点上的整套数据库。

## <a name="take-a-manual-full-backup"></a>采用手动完整备份

要对服务器组的整个数据和日志文件夹进行完整备份，请运行以下命令：
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
其中：
- name 表示备份的名称
- server-name 表示服务器组
- no-wait 表示命令行不会等待备份完成，因此你可以继续使用此命令行窗口

此命令将在构成已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的所有节点之间协调分布式完整备份。 换句话说，它将备份协调器节点和工作器节点中的所有数据。

例如：

```console
azdata arc postgres backup create --name backup12082020-0250pm --server-name postgres01
```

备份完成后，将返回备份的 ID、名称、大小、状态和时间戳。 例如：
```console
{
  "ID": "8085723fcbae4aafb24798c1458f4bb7",
  "name": "backup12082020-0250pm",
  "size": "9.04 MiB",
  "state": "Done",
  "timestamp": "2020-12-08 22:50:22+00:00"
}
```
`+xx:yy` 表示备份时间对应的时区。 在此示例中，“+00:00”表示 UTC 时间（UTC + 00 小时 00 分钟）。

> [!NOTE]
> 目前还不能执行以下操作：
> - 计划自动备份
> - 显示备份进度

## <a name="list-backups"></a>列出备份

要列出可用于还原的备份，请运行以下命令：

```console
azdata arc postgres backup list --server-name <servergroup name>
```

例如：

```console
azdata arc postgres backup list --server-name postgres01
```

返回如下所示的输出：

```output
ID                                Name                   Size       State    Timestamp
--------------------------------  ---------------------  ---------  -------  -------------------------
d744303b1b224ef48be9cba4f58c7cb9  backup12072020-0731pm  13.83 MiB  Done     2020-12-08 03:32:09+00:00
c4f964d28da34318a420e6d14374bd36  backup12072020-0819pm  9.04 MiB   Done     2020-12-08 04:19:49+00:00
a304c6ef99694645a2a90ce339e94714  backup12072020-0822pm  9.1 MiB    Done     2020-12-08 04:22:26+00:00
47d1f57ec9014328abb0d8fe56020760  backup12072020-0827pm  9.06 MiB   Done     2020-12-08 04:27:22+00:00
8085723fcbae4aafb24798c1458f4bb7  backup12082020-0250pm  9.04 MiB   Done     2020-12-08 22:50:22+00:00
```

“时间戳”列指示进行备份的 UTC 时间点。

## <a name="restore-a-backup"></a>还原备份
在此部分，我们将演示如何执行完整还原或时间点还原。 执行完整备份还原时，将还原整个备份内容。 执行时间点还原时，将还原到指定时间点。 在此时间点之后完成的任何事务都不会还原。

> [!CAUTION]
> 你只能还原到与创建备份时具有相同数量的工作器节点的服务器组。 如果你在备份之后增加或减少了工作器节点的数目，则在还原之前，你需要增加/减少工作器节点的数目，或者创建新的服务器组以匹配备份的内容。 当工作器节点的数目不匹配时，还原将失败。

### <a name="restore-a-full-backup"></a>还原完整备份
要还原整个备份内容，请运行以下命令：
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

其中：
- backup-id 是上述列表备份命令中所示备份的 ID。
它将在构成已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的所有节点之间协调分布式完整还原。 换句话说，它将还原协调器节点和工作器节点中的所有数据。

#### <a name="examples"></a>示例：

__将服务器组 postgres01 还原到自身：__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

仅 PostgreSQL 版本 12 和更高版本支持此操作。

__将服务器组 postgres01 还原到其他服务器组 postgres02：__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
从版本 11 以上的任何版本的 PostgreSQL 都支持此操作。 目标服务器组必须在还原操作之前创建，必须采用同一配置，并且必须使用与源服务器组相同的备份 PVC。

还原操作完成后，它会向命令行返回如下所示的输出：

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> 目前还不能执行以下操作：
> - 通过指示备份的名称来还原备份
> - 显示还原操作的进度


### <a name="do-a-point-in-time-restore"></a>进行时间点还原

要将服务器组还原到特定时间点，请运行以下命令：
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

要读取还原命令的一般格式，请运行：`azdata arc postgres backup restore --help`。

其中 `time` 是要还原到的时间点。 提供时间戳或数字加后缀（`m` 代表分钟，`h` 代表小时，`d` 代表天数，或者 `w` 代表周数）。 例如，`1.5h` 表示还原到 90 分钟前。

#### <a name="examples"></a>示例：
__通过时间点还原将服务器组 postgres01 还原到自身：__

但目前还不能通过时间点还原将服务器组还原到自身。

__通过时间点还原在其他服务器组 postgres02 上将服务器组 postgres01 还原到特定时间戳：__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

此示例会在服务器组 postgres02 上还原服务器组 postgres01 在 2020 年 12 月 8 日 04:23:48.75 UTC 时的状态。 注意，“+00”表示指定时间点的时区。 如果不指明时区，将使用执行还原操作时所在客户端的时区。

例如：
- `2020-12-08 04:23:48.751326+00` 解释为 `2020-12-08 04:23:48.751326` UTC
- 如果在太平洋标准时区 (PST = UTC+08)，`2020-12-08 04:23:48.751326` 则解释为 `2020-12-08 12:23:48.751326` UTC。从版本 11 以上的任何版本的 PostgreSQL 都支持此操作。 目标服务器组必须在还原操作之前创建，并且必须使用与源服务器组相同的备份 PVC。


__通过时间点还原在其他服务器组 postgres02 上将服务器组 postgres01 还原到某个特定时间量之前：__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

此示例会在服务器组 postgres02 上还原服务器组 postgres01 在 22 分钟之前的状态。
从版本 11 以上的任何版本的 PostgreSQL 都支持此操作。 目标服务器组必须在还原操作之前创建，并且必须使用与源服务器组相同的备份 PVC。

> [!NOTE]
> 目前还不能执行以下操作：
> - 显示还原操作的进度

## <a name="delete-backups"></a>删除备份

预览中无法设置备份保持期。 不过，可以手动删除不需要的备份。
用于删除备份的常规命令是：

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

其中：
- `--server-name` 是用户要从中删除备份的服务器组的名称
- `--name` 是要删除的备份的名称
- `-id` 是要删除的备份的 ID

> [!NOTE]
> `--name` 和 `-id` 互斥。

例如：

```console
azdata arc postgres backup delete -sn postgres01 -n MyBackup091720200110am
{
  "ID": "5b0481dfc1c94b4cac79dd56a1bb21f4",
  "name": "MyBackup091720200110am",
  "state": "Done"
}
```

可以通过运行上一段中所述的 list backup 命令来检索备份的名称和 ID。

有关 delete 命令的详细信息，请运行：

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>后续步骤
- 了解如何[横向扩展（添加工作器节点）](scale-out-in-postgresql-hyperscale-server-group.md)服务器组
- 了解如何[纵向扩展或缩减（增加/减少内存/vCore）](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)服务器组
