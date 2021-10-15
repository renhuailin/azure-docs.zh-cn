---
title: 将已启用 Azure Arc 的 SQL 托管实例中的数据库还原到之前的某个时间点
description: 说明如何将数据库还原到已启用 Azure Arc 的 SQL 托管实例上的特定时间点。
author: dnethi
ms.author: dinethi
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 0d091e9c90217ff9df6fba45d308296a80970053
ms.sourcegitcommit: 03e84c3112b03bf7a2bc14525ddbc4f5adc99b85
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/03/2021
ms.locfileid: "129401084"
---
#  <a name="perform-a-point-in-time-restore"></a>执行时间点还原

使用时间点还原 (PITR) 将数据库创建为过去某个时间的另一个数据库的副本。 本文介绍如何在已启用 Azure Arc 的 SQL 托管实例中进行数据库的时间点还原。

时间点还原可以通过以下方式还原数据库：

- 从现有数据库进行还原
- 还原到同一已启用 Azure Arc 的 SQL 托管实例上的新数据库

可以将数据库还原到预配置的保留设置中的某个时间点。

时间点还原是一个实例级别的设置，其中包含两个属性 - 恢复点目标 (RPO) 和保留期 (RT)。 RPO 确定事务日志备份的频率。 RPO 是预计数据丢失的时间。 以分钟为单位指定 RPO。 保留期确定你希望已启用 Azure Arc 的数据服务存储数据库备份的时间（以天为单位）。 保留期适用于完整备份、差异备份和事务日志备份文件。  

目前，时间点还原可以通过以下方式还原数据库：

- 从实例上的现有数据库进行还原
- 还原到同一实例上的新数据库

## <a name="limitations"></a>限制

已启用 Azure Arc 的 SQL 托管实例的时间点还原具有以下限制：

- 无法对整个已启用 Azure Arc 的 SQL 托管实例执行时间点还原。 
- 使用高可用性（预览版）部署的已启用 Azure Arc 的 SQL 托管实例当前不支持时间点还原。
- 只能还原到同一已启用 Azure Arc 的 SQL 托管实例。
- 时间点还原只能通过 YAML 文件执行。
- 需要手动清理超过预配置的保留期的旧备份文件。
- 重命名数据库会在新文件夹中启动新的备份链。
- 目前不能正确删除和创建同名的不同数据库。

## <a name="description"></a>说明

时间点还原将数据库还原到特定的时间点。 为了将数据库还原到特定的时间点，已启用 Azure Arc 的数据服务会按特定顺序应用备份文件。 例如：

1. 完整备份
2. 差异备份 
3. 一个或多个事务日志备份

:::image type="content" source="media/point-in-time-restore/point-in-time-restore.png" alt-text="时间点还原":::

已启用 Azure Arc 的 SQL 托管实例附带了用于执行时点还原的内置功能。 每当在已启用 Azure Arc 的 SQL 托管实例上创建或还原新数据库时，都会自动进行备份。 

有两个影响时间点还原功能的参数：

- 恢复点目标 
- 保留期

## <a name="create-a-database-from-a-point-in-time"></a>从某个时间点创建数据库

以下是使用 `kubectl` 将数据库还原到同一已启用 Azure Arc 的 SQL 托管实例的步骤：

1. 为还原操作创建一个任务。 使用还原参数创建 .yaml 文件。

   例如：

   ```json
   apiVersion: tasks.sql.arcdata.microsoft.com/v1beta1
      kind: SqlManagedInstanceRestoreTask
   metadata:
     name: sql01-restore-20210909
   namespace: arc
   spec:
     source:
       name: sql01
       database: db01
     restorePoint: "2021-09-09T02:00:00Z"
     destination:
       name: sql01
       database: db02
   ```

   编辑上述 yaml 文件：

   - `metadata` > `name` - 任务自定义资源 (CR) 的名称
   - `metadata` > `namespace` - 已启用 Azure Arc 的 SQL 托管实例的命名空间
   - `source` > `name` - 已启用 Azure Arc 的 SQL 托管实例的名称
   - `source` > `database` - 已启用 Azure Arc 的 SQL 托管实例上从中还原的源数据库的名称
   - `restorePoint` - 要还原到的时间点，采用“UTC”日期时间。
   - `destination` > `name` - 已启用 Azure Arc 的 SQL 托管实例的名称
   - `destination` > `database` - 已启用 Azure Arc 的 SQL 托管实例上的目标数据库的名称


   > [!NOTE] 
   > 源和目标已启用 Azure Arc 的 SQL 托管实例的名称应相同。

2. 创建任务以启动时间点还原操作

   ```console
   kubectl apply -f sql-restore-task.yaml
   ```

3. 检查还原的状态

   运行以下命令以检查还原操作的状态。

   ```console
   kubectl get sqlmirestoretask -n <namespace>
   ```

   将 `<namespace>` 替换为托管 SQL 实例的命名空间。

还原任务的状态显示为“已完成”后，新数据库应可用。 

## <a name="troubleshoot-failed-restore-operations"></a>排查失败的还原操作问题

如果还原任务状态显示为“失败”，请运行以下命令，以在事件中查找根本原因。

```console
kubectl describe sqlmirestoretask <taskname> -n <namespace>
```

例如：
```console
kubectl describe sqlmirestoretask sql01-restore-20210909 -n arc
```

## <a name="enabledisable-automated-backups"></a>启用/禁用自动备份

时间点还原 (PITR) 服务默认处于启用状态，设置如下：

- 恢复点目标 (RPO) = 300 秒。 接受的值为 0，或介于 300 到 600 之间（以秒为单位）

RPO 将服务设置为默认情况下每 300 秒（5 分钟）为已启用 Azure Arc 的 SQL 托管实例上的所有数据库执行一次日志备份。 可以将此值更改为 0，以禁用正在进行的备份，也可以更改为一个更大的值（以秒为单位），具体取决于 SQL 实例上的数据库所需的 RPO 要求。 

可为已启用 Azure Arc 的 SQL 托管实例的特定实例禁用自动备份，也可更改默认设置。 无法禁用 PITR 服务本身。

可通过更改 `recoveryPointObjectiveInSeconds` 属性的值来编辑 RPO，如下所示：

```console
kubectl edit sqlmi <sqlinstancename>  -n <namespace> -o yaml
```

此命令会在默认编辑器中打开已启用 Azure Arc 的 SQL 托管实例的自定义资源规范。 在 `spec` 下查找 `backup` 设置：

```json
backup:
  recoveryPointObjectiveInSeconds: 300
```

在编辑器中编辑 `recoveryPointObjectiveInSeconds` 的值并保存所做的更改，使新设置生效。 

> [!NOTE]
> 编辑 RPO 设置会重新启动包含已启用 Azure Arc 的 SQL 托管实例的 Pod。 

## <a name="monitor-backups"></a>监视器备份

备份存储在 `/var/opt/mssql/backups/archived/<dbname>/<datetime>` 文件夹下，其中 `<dbname>` 是数据库的名称，`<datetime>` 是一个采用 UTC 格式的时间戳，表示每个完整备份的开始。 每次启动完整备份时，将创建一个新文件夹，其中包含完整备份以及所有后续差异备份和事务日志备份。 最新的完整备份及其后续差异备份和事务日志备份都存储在 `/var/opt/mssql/backups/current/<dbname><datetime>` 文件夹下。

### <a name="clean-up"></a>清理 

如果需要删除较旧的备份以释放空间或不再需要它们，则可以删除 `/var/opt/mssql/backups/archived/` 文件夹下的任何文件夹。 在时间线中间删除文件夹可能会影响在该时间段内还原到某个时间点的功能。 先删除最旧的文件夹，确保有一个持续的还原功能时间线。 

## <a name="next-steps"></a>后续步骤

[详细了解已启用 Azure Arc 的 SQL 托管实例的特性和功能](managed-instance-features.md)

[首先创建数据控制器](create-data-controller.md)

[已创建数据控制器？请创建已启用 Azure Arc 的 SQL 托管实例](create-sql-managed-instance.md)
