---
title: 启用 Azure Arc 托管实例高可用性
titleSuffix: Deploy Azure Arc enabled Managed Instance with high availability
description: 了解如何部署启用了 Azure Arc 托管实例的高可用性。
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: conceptual
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.openlocfilehash: e04b9b98db26b3b9a024a60c6f82820fe20fcbf1
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101692999"
---
# <a name="azure-arc-enabled-managed-instance-high-availability"></a>启用 Azure Arc 托管实例高可用性

启用 Azure Arc 托管实例作为容器化应用程序部署在 Kubernetes 上，并使用 Kubernetes 构造（如有状态集和永久存储）来提供内置的运行状况监视、故障检测和故障转移机制来维护服务运行状况。 为了提高可靠性，还可以配置启用了 Azure Arc 托管实例以便在高可用性配置中使用额外副本进行部署。 监视、故障检测和自动故障转移由 Arc data services 数据控制器管理。 无需用户干预即可提供此服务：从设置可用性组、配置数据库镜像终结点到将数据库添加到可用性组或故障转移和升级协调等等。 本文档介绍这两种类型的高可用性。

## <a name="built-in-high-availability"></a>内置的高可用性 

当远程持久存储已配置并与 Arc 数据服务部署使用的节点共享时，Kubernetes 提供内置的高可用性。 在此配置中，Kubernetes 充当群集业务流程协调程序的作用。 当容器或基础节点中的托管实例发生故障时，orchestrator 会引导该容器的另一个实例，并将其附加到同一个永久性存储。 默认情况下，在部署启用了 Azure Arc 托管实例的情况下启用此类型。

### <a name="verify-built-in-high-availability"></a>验证内置的高可用性

本部分将验证 Kubernetes 提供的内置高可用性。 当你按照这些步骤来测试此功能时，你将删除现有托管实例的 pod，并验证 Kubernetes 是否从此操作恢复。 

### <a name="prerequisites"></a>先决条件

- Kubernetes 群集必须具有 [共享的远程存储](https://docs.microsoft.com/en-us/azure/azure-arc/data/storage-configuration#factors-to-consider-when-choosing-your-storage-configuration) 
- 已启用 Azure Arc 托管实例使用一个副本部署 (默认) 

1. 查看 pod。 

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

2. 删除托管实例 pod。

   ```console
   kubectl delete pod <name of managed instance>-0 -n <namespace of data controller>
   ```

   例如

   ```output
   user@pc:/# kubectl delete pod sql1-0 -n arc
   pod "sql1-0" deleted
   ```

3. 查看 pod，验证托管实例是否正在恢复。

   ```console
   kubectl get pods -n <namespace of data controller>
   ```

   例如

   ```output
   user@pc:/# kubectl get pods -n arc
   NAME                 READY   STATUS    RESTARTS   AGE
   sql1-0               2/3     Running   0          22s
   ```

在 pod 中的所有容器都恢复之后，可以连接到托管实例。

## <a name="deploy-with-always-on-availability-groups"></a>部署 Always On 可用性组

为了提高可靠性，你可以配置启用了 Azure Arc 托管实例以便在高可用性配置中使用额外副本进行部署。 

可用性组启用的功能：

- 当使用多个副本部署时，将创建一个名为的单一可用性组 `containedag` 。 默认情况下，`containedag` 有三个副本，其中包括主要副本。 可用性组的所有 CRUD 操作都在内部进行管理，包括创建可用性组或将副本联接到创建的可用性组。 无法在启用了 Azure Arc 托管实例中创建其他可用性组。

- 所有数据库都将自动添加到可用性组，包括所有用户和系统数据库（如 `master` 和 `msdb`）。 此功能提供跨可用性组副本的单系统视图。 `containedag_master` `containedag_msdb` 如果直接连接到实例，请注意和数据库。 `containedag_*` 数据库表示可用性组中的 `master` 和 `msdb`。

- 系统会自动预配外部终结点，以便与可用性组中的数据库建立连接。 此终结点 `<managed_instance_name>-svc-external` 扮演可用性组侦听器的角色。

### <a name="deploy"></a>部署

若要使用可用性组部署托管实例，请运行以下命令。

```console
azdata arc sql mi create -n <name of instance> --replicas 3
```

### <a name="check-status"></a>查看状态
部署实例后，运行以下命令检查实例的状态：

```console
azdata arc sql mi list
azdata arc sql mi show -n <name of instance>
```

示例输出：

```output
user@pc:/# azdata arc sql mi list
ExternalEndpoint    Name    Replicas    State
------------------  ------  ----------  -------
20.131.31.58,1433   sql2    3/3         Ready

user@pc:/#  azdata arc sql mi show -n sql2
{
...
  "status": {
    "AGStatus": "Healthy",
    "externalEndpoint": "20.131.31.58,1433",
    "logSearchDashboard": "link to logs dashboard",
    "metricsDashboard": "link to metrics dashboard",
    "readyReplicas": "3/3",
    "state": "Ready"
  }
}
```

请注意额外数量的 `Replicas` 和 `AGstatus` 指示可用性组的运行状况的字段。 如果所有副本都已启动并同步，则此值为 `healthy` 。 

### <a name="restore-a-database"></a>还原数据库 
若要将数据库还原到可用性组，则需要执行其他步骤。 以下步骤演示如何将数据库还原到托管实例，并将其添加到可用性组。 

1. 通过创建新的 Kubernetes 服务来公开主实例外部终结点。

    通过连接到托管实例并运行以下内容来确定承载主副本的 pod：

    ```sql
    SELECT @@SERVERNAME
    ```
    如果 kubernetes 群集使用 nodePort services，请运行以下命令，以创建 kubernetes 服务到主实例。 `podName`将替换为在上一步骤中返回的服务器的名称，将 `serviceName` 创建 Kubernetes 服务的首选名称。

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=NodePort
    ```

    对于 LoadBalancer 服务，运行相同的命令，但所创建的服务的类型为 `LoadBalancer` 。 例如： 

    ```bash
    kubectl -n <namespaceName> expose pod <podName> --port=1533  --name=<serviceName> --type=LoadBalancer
    ```

    下面是针对 Azure Kubernetes 服务运行的此命令的示例，其中承载主副本的 pod 是 `sql2-0` ：

    ```bash
    kubectl -n arc-cluster expose pod sql2-0 --port=1533  --name=sql2-0-p --type=LoadBalancer
    ```

    获取创建的 Kubernetes 服务的 IP：

    ```bash
    kubectl get services -n <namespaceName>
    ```
2. 将数据库还原到主实例终结点。

    将数据库备份文件添加到主实例容器。

    ```console
    kubectl cp <source file location> <pod name>:var/opt/mssql/data/<file name> -n <namespace name>
    ```

    示例

    ```console
    kubectl cp /home/WideWorldImporters-Full.bak sql2-1:var/opt/mssql/data/WideWorldImporters-Full.bak -c arc-sqlmi -n arc
    ```

    通过运行以下命令，还原数据库备份文件。

    ```sql 
    RESTORE DATABASE test FROM DISK = '/var/opt/mssql/data/<file name>.bak'
    WITH MOVE '<database name>' to '/var/opt/mssql/data/<file name>.mdf'  
    ,MOVE '<database name>' to '/var/opt/mssql/data/<file name>_log.ldf'  
    ,RECOVERY, REPLACE, STATS = 5;  
    GO
    ```
    
    示例

    ```sql
    RESTORE Database WideWorldImporters
    FROM DISK = '/var/opt/mssql/data/WideWorldImporters-Full.BAK'
    WITH
    MOVE 'WWI_Primary' TO '/var/opt/mssql/data/WideWorldImporters.mdf',
    MOVE 'WWI_UserData' TO '/var/opt/mssql/data/WideWorldImporters_UserData.ndf',
    MOVE 'WWI_Log' TO '/var/opt/mssql/data/WideWorldImporters.ldf',
    MOVE 'WWI_InMemory_Data_1' TO '/var/opt/mssql/data/WideWorldImporters_InMemory_Data_1',
    RECOVERY, REPLACE, STATS = 5;  
    GO
    ```

3. 将数据库添加到可用性组。

    对于要添加到 AG 的数据库，必须在完整恢复模式下运行，并且必须执行日志备份。 运行以下 TSQL 语句，将还原的数据库添加到可用性组中。

    ```sql
    ALTER DATABASE <databaseName> SET RECOVERY FULL;
    BACKUP DATABASE <databaseName> TO DISK='<filePath>'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE <databaseName>
    ```

    以下示例添加了一个名为 `WideWorldImporters` 的数据库，该数据库在实例上已还原：

    ```sql
    ALTER DATABASE WideWorldImporters SET RECOVERY FULL;
    BACKUP DATABASE WideWorldImporters TO DISK='/var/opt/mssql/data/WideWorldImporters.bak'
    ALTER AVAILABILITY GROUP containedag ADD DATABASE WideWorldImporters
    ```

> [!IMPORTANT]
> 最佳做法是，通过运行以下命令删除上面创建的 Kubernetes 服务进行清理：
>
>```bash
>kubectl delete svc sql2-0-p -n arc
>```

### <a name="limitations"></a>限制

启用 Azure Arc 托管实例可用性组具有与 [大数据群集可用性组相同的限制。单击此处了解详细信息。](/sql/big-data-cluster/deployment-high-availability#known-limitations)

## <a name="next-steps"></a>后续步骤

了解有关[已启用 Azure Arc 的 SQL 托管实例的特性和功能](managed-instance-features.md)的详细信息
