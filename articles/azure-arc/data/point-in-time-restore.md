---
title: 将数据库还原到某个时间点
description: 介绍如何执行时间点还原操作
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: fe4d250035c58bde2ba52e71046ea2f88854ff78
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121743830"
---
#  <a name="perform-a-point-in-time-restore"></a>执行时间点还原


已启用 Azure Arc 的 SQL 托管实例内置了许多类似 PaaS 的功能。 其中一项功能是在预配置的保留设置内，将数据库还原到某个时间点。 本文介绍如何在已启用 Azure Arc 的 SQL 托管实例中进行数据库的时间点还原。

时间点还原是一个实例级别的设置，其中包含两个属性 - 恢复点目标 (RPO) 和保留时间 (RT)。 恢复点目标设置确定事务日志备份的频率。 这也是预计数据丢失的时间。 保留时间是指备份（完整备份、差异备份和事务日志备份）保留的时间。  

目前，时间点还原可以按如下方式还原数据库：

- 从 SQL 实例上的现有数据库进行还原
- 还原到同一 SQL 实例上的新数据库

### <a name="limitations"></a>限制

已启用 Azure Arc 的 SQL 托管实例的时间点还原具有以下限制：

- 只能还原到已启用 Azure Arc 的同一 SQL 托管实例
- 时间点还原只能通过 YAML 文件执行 
- 需要手动清理超过预配置的保留期的旧备份文件
- 重命名数据库会在新文件夹中启动新的备份链
- 目前不能正确删除和创建同名的不同数据库

### <a name="edit-pitr-settings"></a>编辑 PITR 设置

##### <a name="enabledisable-automated-backups"></a>启用/禁用自动备份

时间点还原 (PITR) 服务默认处于启用状态，设置如下：

- 恢复点目标 (RPO) = 300 秒。 接受的值为 0，或介于 300 到 600 之间（以秒为单位）

这意味着，默认情况下，每 300 秒（5 分钟）将执行一次已启用 Azure Arc 的 SQL 托管实例上的所有数据库的日志备份。 可以将此值更改为 0，以禁用正在进行的备份，也可以更改为一个更大的值（以秒为单位），具体取决于 SQL 实例上的数据库所需的 RPO 要求。 

无法禁用 PITR 服务本身，但可以禁用已启用 Azure Arc 的 SQL 托管实例的特定实例的自动备份，或更改默认设置。

可以通过更改属性 ```recoveryPointObjectiveInSeconds``` 的值来编辑 RPO，如下所示：

```
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

这应该会在你的默认编辑器中打开已启用 Azure Arc 的 SQL 托管实例的自定义资源规范。 在 ```spec``` 下查找 ```backup``` 设置：

```
backup:
  recoveryPointObjectiveInSeconds: 300
```

在编辑器中编辑 ```recoveryPointObjectiveInSeconds``` 的值并保存所做的更改，使新设置生效。 

> [!NOTE]
> 编辑 RPO 设置会重新启动包含已启用 Azure Arc 的 SQL 托管实例的 Pod。 

### <a name="restore-a-database-to-a-point-in-time"></a>将数据库还原到某个时间点

可对已启用 Azure Arc 的 SQL 托管实例执行还原操作，以便在保留期内从源数据库还原到某个时间点。 
(1) 在编辑器中按如下所示创建 YAML 文件：

```
apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
kind: SqlManagedInstanceRestoreTask
metadata:
  name: sql01-restore-20210707
  namespace: arc
spec:
  source:
    name: sql01
    database: db01
  restorePoint: "2021-07-01T02:00:00Z"
  destination:
    name: sql01
    database: db02
```

- 名称 - Kubernetes 所需的每个自定义资源的唯一字符串
- 命名空间 - 其中已启用 Azure Arc 的 SQL 托管实例正在运行的命名空间
- 源 > 名称 - 已启用 Azure Arc 的 SQL 托管实例的名称
- 源 > 数据库 - 已启用 Azure Arc 的 SQL 托管实例上的源数据库的名称
- restorePoint - 还原操作的时间点（使用“UTC”日期时间格式）。
- 目标 > 名称 - 要还原到的目标已启用 Azure Arc 的 SQL 托管实例的名称。 目前仅支持还原到相同的实例。
- 目标 > 数据库 - 要将还原应用到的目标新数据库的名称

(2) 应用 YAML 文件来创建用于启动还原操作的任务

按如下所示运行命令，以启动还原操作：

```
kubectl apply -f sql01-restore-task.yaml
```

> [!NOTE]
> 自定义资源内的任务的名称与文件名不必相同。


**检查还原的状态**

- 大约每 10 秒更新一次还原任务状态，并且状态从“正在等待”更改为“正在还原”，再更改为“已完成”/“失败”。 
- 还原数据库时，状态将反映“正在还原”。

可按如下所示检索任务的状态：

```
kubectl get sqlmirestoretask -n arc
``` 

### <a name="monitor-your-backups"></a>监视备份

备份存储在 ```/var/opt/mssql/backups/archived/<dbname>/<datetime>``` 文件夹下，其中 ```<dbname>``` 是数据库的名称，```<datetime>``` 是一个采用 UTC 格式的时间戳，表示每个完整备份的开始。 每次启动完整备份时，将创建一个新文件夹，其中包含完整备份以及所有后续差异备份和事务日志备份。 最新的完整备份及其后续差异备份和事务日志备份都存储在 ```/var/opt/mssql/backups/current/<dbname><datetime>``` 文件夹下。


### <a name="clean-up"></a>清理 

如果需要删除较旧的备份以释放空间或不再需要它们，则可以删除 ```/var/opt/mssql/backups/archived/``` 文件夹下的任何文件夹。 在时间线中间删除文件夹可能会影响在该时间段内还原到某个时间点的功能。 建议先删除最旧的文件夹，确保有一个持续的还原功能时间线。 


