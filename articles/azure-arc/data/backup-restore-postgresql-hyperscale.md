---
title: 为 Azure Database for PostgreSQL 超大规模服务器组进行备份和还原
description: 为 Azure Database for PostgreSQL 超大规模服务器组进行备份和还原
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 12/09/2020
ms.topic: how-to
ms.openlocfilehash: 8b3304c673e8606667246a7d0df9ad8f3be11d9b
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686693"
---
# <a name="back-up-and-restore-azure-arc-enabled-postgresql-hyperscale-server-groups"></a>备份和还原启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

备份或还原启用了 Azure Arc 的 PostgreSQL 超大规模服务器组时，将备份和/或还原服务器组的所有 PostgreSQL 节点上的整个数据库集。

## <a name="take-a-manual-full-backup"></a>进行手动完整备份

若要对服务器组的整个数据和日志文件夹进行完整备份，请运行以下命令：
```console
azdata arc postgres backup create [--name <backup name>] --server-name <server group name> [--no-wait] 
```
其中：
- __名称__ 指示备份的名称
- __服务器名称__ 指示服务器组
- "__否"-等待__ 指示命令行不会等待备份完成，你可以继续使用此命令行窗口

此命令将在构成启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的所有节点之间协调分布式完整备份。 换句话说，它将备份协调器和辅助节点中的所有数据。

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
`+xx:yy` 指示进行备份的时间的时区。 在此示例中，"+ 00:00" 表示 utc 时间 (UTC + 00 小时00分钟) 。

> [!NOTE]
> 尚不能：
> - 计划自动备份
> - 显示执行备份的进度

## <a name="list-backups"></a>列出备份

若要列出可用于还原的备份，请运行以下命令：

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

Timestamp 列指示进行备份的时间点。

## <a name="restore-a-backup"></a>还原备份
在本部分中，我们将演示如何执行完整还原或时间点还原。 还原完整备份时，将还原整个备份内容。 执行时间点还原时，会还原到所指示的时间点。 在此时间点之后完成的任何事务都不会还原。

### <a name="restore-a-full-backup"></a>还原完整备份
若要还原备份的整个内容，请运行以下命令：
```console
azdata arc postgres backup restore --server-name <target server group name> [--source-server-name <source server group name> --backup-id <backup id>]
or
azdata arc postgres backup restore -sn <target server group name> [-ssn <source server group name> --backup-id <backup id>]
```
<!--To read the general format of restore command, run: azdata arc postgres backup restore --help -->

其中：
- __backup-id__ 是上面所示的列表备份命令中所示的备份 id。
这会在构成启用了 Azure Arc 的 PostgreSQL 超大规模服务器组的所有节点之间协调分布式完全还原。 换句话说，它将还原协调器和工作节点中的所有数据。

#### <a name="examples"></a>示例：

__将服务器组 postgres01 还原到自身：__

```console
azdata arc postgres backup restore -sn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```

仅 PostgreSQL 版本12和更高版本支持此操作。

__将服务器组 postgres01 还原到不同的服务器组 postgres02：__

```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 --backup-id d134f51aa87f4044b5fb07cf95cf797f
```
任何版本的 PostgreSQL （从版本11开始）都支持此操作。 必须在还原操作之前创建目标服务器组，必须为同一配置，并且必须使用与源服务器组相同的备份 PVC。

还原操作完成后，它会将类似于下面的输出返回到命令行：

```json
{
  "ID": "d134f51aa87f4044b5fb07cf95cf797f",
  "state": "Done"
}
```

> [!NOTE]
> 尚不能：
> - 通过指示备份的名称来还原备份
> - 显示还原操作的进度


### <a name="do-a-point-in-time-restore"></a>执行时间点还原

若要将服务器组还原到特定的时间点，请运行以下命令：
```console
azdata arc postgres backup restore --server-name <target server group name> --source-server-name <source server group name> --time <point in time to restore to>
or
azdata arc postgres backup restore -sn <target server group name> -ssn <source server group name> -t <point in time to restore to>
```

若要读取还原命令的常规格式，请运行： `azdata arc postgres backup restore --help` 。

其中 `time` ，是要还原到的时间点。 提供时间戳或数字，并为分钟、 `m` `h` 小时、 `d` 天或 `w`) 周提供 (。 例如， `1.5h` 90 分钟后返回。

#### <a name="examples"></a>示例：
__将服务器组 postgres01 的时间点还原到自身：__

但不能将服务器组的时间点还原到其自身。

__将服务器组 postgres01 的时间点还原到不同的服务器组 postgres02 到特定时间戳：__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "2020-12-08 04:23:48.751326+00"
``` 

此示例将还原为服务器组，postgres02 服务器组 postgres01 在12月8日 2020 12：23： 48.75 UTC 处的状态。 请注意，"+ 00" 指示所指示的时间点的时区。 如果不指明时区，将使用运行还原操作的客户端的时区。

例如：
- `2020-12-08 04:23:48.751326+00` 被解释为 `2020-12-08 04:23:48.751326` UTC
- 如果你处于太平洋标准时区 (PST = UTC + 08) ， `2020-12-08 04:23:48.751326` 则会将其解释为 `2020-12-08 12:23:48.751326` UTC，从版本11开始，PostgreSQL 的任何版本都支持此操作。 必须先创建目标服务器组，然后才能还原操作，并且必须使用与源服务器组相同的备份 PVC。


__将服务器组 postgres01 的时间点还原到不同的服务器组 postgres02 到过去的特定时间量：__
```console
azdata arc postgres backup restore -sn postgres02 -ssn postgres01 -t "22m"
```

此示例还原为服务器组 postgres02 服务器组 postgres01 在22分钟之前的状态。
任何版本的 PostgreSQL （从版本11开始）都支持此操作。 必须先创建目标服务器组，然后才能还原操作，并且必须使用与源服务器组相同的备份 PVC。

> [!NOTE]
> 尚不能：
> - 显示还原操作的进度

## <a name="delete-backups"></a>删除备份

无法在预览中设置备份保持期。 不过，您可以手动删除不需要的备份。
用于删除备份的常规命令是：

```console
azdata arc postgres backup delete  [--server-name, -sn] {[--name, -n], -id}
```

其中：
- `--server-name` 用户要从中删除备份的服务器组的名称
- `--name` 要删除的备份的名称
- `-id`要删除的备份的 ID

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

可以通过运行上一段中所述的 "列出备份" 命令来检索备份的名称和 ID。

有关 delete 命令的详细信息，请运行：

```console
azdata arc postgres backup delete --help
```

## <a name="next-steps"></a>后续步骤
- 阅读[ (向) 服务器组添加辅助角色节点](scale-out-postgresql-hyperscale-server-group.md)
- 阅读有关[扩展或缩减 (增加/减少服务器组) 的内存/vcore](scale-up-down-postgresql-hyperscale-server-group-using-cli.md)
