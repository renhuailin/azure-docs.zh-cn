---
title: 已启用 Azure Arc 的数据服务 - 发行说明
description: 最新发行说明
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 05/04/2021
ms.topic: conceptual
ms.openlocfilehash: 58dd7baa612e2dcf302ce87b2d77cd0e71f90187
ms.sourcegitcommit: 02d443532c4d2e9e449025908a05fb9c84eba039
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/06/2021
ms.locfileid: "108763840"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>发行说明 - 已启用 Azure Arc 的数据服务（预览版）

本文重点介绍了已启用 Azure Arc 的数据服务的最近发布或改进的功能、特性和增强功能。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="april-2021"></a>2021 年 4 月

此预览版发布于 2021 年 4 月 29 日。

### <a name="whats-new"></a>新变化

本节介绍此版本中引入或启用的新功能。 

#### <a name="platform"></a>平台

- 直接连接的群集自动将遥测数据信息自动上传到 Azure。 

####    <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- 现支持直接连接模式下的已启用 Azure Arc 的超大规模 PostgreSQL。 你现可从 Azure 门户中的 Azure 市场部署已启用 Azure Arc 的超大规模 PostgreSQL。 
- 已启用 Azure Arc 的超大规模 PostgreSQL 随附 Citus 10.0 扩展，其中包含列式表格存储
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持完全用户/角色管理。
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持具有 `Tdigest` 和 `pg_partman` 的其他扩展。
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持在服务器组中按 PostgreSQL 实例的每个角色配置 vCore 和内容设置。
- 已启用 Azure Arc 的超大规模 PostgreSQL 现支持在服务器组中按 PostgreSQL 实例的每个角色配置数据库引擎/服务器设置。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 你在可将数据库还原为包含 3 个副本的 SQL 托管实例，它将自动添加到可用性组。 
- 连接到部署有 3 个副本的 SQL 托管实例上的只读辅助终结点。 使用 `azdata arc sql endpoint list` 查看只读辅助连接终结点。

### <a name="known-issues"></a>已知问题

- 你可以使用 Azure 门户在直接连接模式下创建数据控制器。 不支持通过其他已启用 Azure Arc 的数据服务工具进行部署。 具体而言，在此版本中，你不能使用以下任何工具在直接连接模式下部署数据控制器。
   - Azure Data Studio
   - Azure Data CLI (`azdata`)
   - Kubernetes 本机工具 (`kubectl`)

   [部署 Azure Arc 数据控制器 | 直接连接模式](deploy-data-controller-direct-mode.md)说明了如何在门户中创建数据控制器。 

- 在直接连接模式下，使用 `azdata arc dc upload` 上传使用量、指标和日志当前已被阻止。 使用量会自动上传。 在间接连接模式下创建的数据控制器的上传应继续工作。
- 如果通过 `–proxy-cert <path-t-cert-file>` 使用代理，则在直接连接模式下自动上传使用量数据将失败。
- 已启用 Azure Arc 的 SQL 托管实例和已启用 Azure Arc 的超大规模 PostgreSQL 均未通过 GB18030 认证。
- 目前，在直接连接模式下仅支持每个 kubernetes 群集有一个 Azure Arc 数据控制器。

#### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 直接模式下的已启用 Azure Arc 的 SQL 托管实例的部署只能通过 Azure 门户完成，而不能通过 azdata、Azure Data Studio 或 kubectl 等工具进行。

#### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- 目前不支持在 NFS 存储进行时间点还原。
- 不能同时启用和配置 `pg_cron` 扩展。 为此，你需要使用两个命令，分别是 启用命令和配置命令。 例如：

   1. 启用扩展：
   
      ```console
      azdata arc postgres server edit -n myservergroup --extensions pg_cron 
      ```

   1. 重启服务器组。

   1. 配置扩展：
   
      ```console
      azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
      ```

   如果在重启之前执行第二个命令，该命令将失败。 如果命令失败，只需等待一段时间，然后再次执行第二个命令。

- 在编辑服务器组的配置以启用其他扩展时，向 `--extensions` 参数传递无效值会错误地将启用的扩展列表重置为创建服务器组时的列表，并阻止用户创建其他扩展。 如发生该情况，唯一的解决方法是删除服务器组，然后重新部署。

## <a name="march-2021"></a>2021 年 3 月

2021 年 3 月版最初于 2021 年 4 月 5 日推出，发布的最后阶段于 2021 年 4 月 9 日完成。

Azure Data CLI (`azdata`) 版本号：20.3.2。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

### <a name="data-controller"></a>数据控制器

- 在门户上以直接连接模式部署已启用 Azure Arc 的数据服务数据控制器。 从[部署数据控制器 - 直接连接模式（先决条件）](deploy-data-controller-direct-mode-prerequisites.md)开始。

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

PostgreSQL 的两个自定义资源定义 (CRD) 已经合并到一个 CRD 中。 请参阅下表。

|发布 |CRD |
|-----|-----|
|2021 年 2 月及之前| postgresql-11s.arcdata.microsoft.com<br/>postgresql-12s.arcdata.microsoft.com |
|2021 年 3 月开始 | postgresqls.arcdata.microsoft.com

清除过去的安装时，将删除以前的 CRD。 请参阅[清除过去的安装](create-data-controller-using-kubernetes-native-tools.md#cleanup-from-past-installations)。

### <a name="azure-arc-enabled-sql-managed-instance"></a>已启用 Azure Arc 的 SQL 托管实例

- 你现在可以在 Azure 门户中以接连接模式创建一个 SQL 托管实例。

- 你现可将数据库还原为包含 3 个副本的 SQL 托管实例，它将自动添加到可用性组中。 

- 你现可连接到部署有 3 个副本的 SQL 托管实例上的只读辅助终结点。 使用 `azdata arc sql endpoint list` 查看只读辅助连接终结点。

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.3.1。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

其他更新包括：

- 已启用 Azure Arc 的 SQL 托管实例
   - 使用 Always On 可用性组实现高可用性

- 已启用 Azure Arc 的超大规模 PostgreSQL Azure Data Studio： 
   - 概述页面显示按节点分项的服务器组状态
   - 新的属性页显示有关服务器组的更多详细信息
   - 从“节点参数”页配置 Postgres 引擎参数

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.3.0。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

其他更新包括：
- 适用于 17 种新语言的本地化门户
- 对 Kube 本机 .yaml 文件进行了细微的更改
- 新版本的 Grafana 和 Kibana
- 已解决在 Azure Data Studio 的笔记本中使用 azdata 时 Python 环境的问题
- pg_audit 扩展现可用于超大规模 PostgreSQL
- 执行超大规模 PostgreSQL 数据库的完全还原时，不再需要备份 ID
- 将报告构成服务器组的每个 PostgreSQL 实例的状态（运行状况状态）

   在早期版本中，状态在服务器组级别聚合，而不是在 PostgreSQL 节点级别逐项列出。

- PostgreSQL 部署采用 create 命令中指定的卷大小参数
- 在编辑服务器组时，系统现会采用引擎版本参数
- 已启用 Azure Arc 的超大规模 PostgreSQL 的 pod 的命名约定已更改

    它现在采用以下形式：`ServergroupName{c, w}-n`。 例如，一个具有三个节点（一个协调器节点和两个工作器节点）的服务器组表示为：
   - `Postgres01c-0`（协调器节点）
   - `Postgres01w-0`（工作器节点）
   - `Postgres01w-1`（工作器节点）

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.2.5。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

使用 Azure 数据 CLI (`azdata`) 通过 `azdata arc sql endpoint list` 和 `azdata arc postgres endpoint list` 命令查看 SQL 托管实例和超大规模 PostgreSQL 的终结点。

使用 Azure Data Studio 编辑 SQL 托管实例资源（CPU 内核和内存）的请求和限制。

已启用 Azure Arc 的超大规模 PostgreSQL 现在不仅支持 PostgreSQL 版本 11 和 12 的完全备份还原，而且还支持时间点还原。 通过时间点还原功能，可以指示要还原到的特定日期和时间。

已启用 Azure Arc 的超大规模 PostgreSQL 的 pod 的命名约定已更改。 它现在采用以下形式：ServergroupName{r, s}-n。 例如，一个具有三个节点（一个协调器节点和两个工作器节点）的服务器组表示为：
- `postgres02r-0`（协调器节点）
- `postgres02s-0`（工作器节点）
- `postgres02s-1`（工作器节点）

### <a name="breaking-change"></a>重大更改

#### <a name="new-resource-provider"></a>新资源提供程序

此版本引入了一个称为 `Microsoft.AzureArcData` 的更新的[资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)。 你需要注册此资源提供程序，然后才能使用此功能。 

若要注册此资源提供程序，请执行以下操作： 

1. 在 Azure 门户中，选择“订阅” 
2. 选择自己的订阅
3. 在“设置”下，选择“资源提供程序”  
4. 搜索 `Microsoft.AzureArcData`，并选择“注册” 

有关详细步骤，请查看 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)。 此更改还会删除已上传到 Azure 门户的所有现有 Azure 资源。 若要使用资源提供程序，需要更新数据控制器并使用最新的 `azdata` CLI。  

### <a name="platform-release-notes"></a>平台发行说明

#### <a name="direct-connectivity-mode"></a>直接连接模式

此版本引入了直接连接模式。 直接连接模式使数据控制器能够自动将使用情况信息上传到 Azure。 在使用情况上传过程中，将在门户中自动创建 Arc 数据控制器资源（如果尚未通过 `azdata` 上传创建）。  

创建数据控制器时，可以指定直接连接。 以下示例使用直接连接模式 (`connectivity-mode direct`) 通过名为 `arc` 的 `azdata arc dc create` 创建数据控制器。 在运行该示例之前，请将 `<subscription id>` 替换为你的订阅 ID。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

## <a name="october-2020"></a>2020 年 10 月 

Azure 数据 CLI (`azdata`) 版本号：20.2.3。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

### <a name="breaking-changes"></a>中断性变更

此版本引入了以下中断性变更： 

* 在 PostgreSQL 自定义资源定义 (CRD) 中，术语 `shards` 重命名为 `workers`。 此术语 (`workers`) 与命令行参数名称匹配。

* `azdata arc postgres server delete` 在删除 postgres 实例之前提示你进行确认。  使用 `--force` 跳过提示。

### <a name="additional-changes"></a>其他更改

* 已向 `azdata arc postgres server create` 添加了一个新的可选参数，称为 `--volume-claim mounts`。 此值是以逗号分隔的卷声明装载列表。 卷声明装载是一对卷类型和 PVC 名称。 目前唯一支持的卷类型是 `backup`。  在 PostgreSQL 中，当卷类型为 `backup` 时，PVC 将装载到 `/mnt/db-backups`。  这样就可以在 PostgresSQL 实例之间共享备份，以便可以在另一个实例中还原一个 PostgresSQL 实例的备份。

* PostgresSQL 自定义资源定义的新短名称： 
  * `pg11` 
  * `pg12`
* 遥测上传为用户提供：
   * 上传到 Azure 的点数或 
   * 如果没有数据加载到 Azure，则会提示再次尝试。
* `azdata arc dc debug copy-logs` 现在也从 `/var/opt/controller/log` 文件夹中读取并收集 Linux 上的 PostgreSQL 引擎日志。
*   在使用超大规模 PostgreSQL 创建和还原备份期间显示工作指示器。
* `azdata arc postrgres backup list` 现在包含备份大小信息。
* SQL 托管实例的管理员名称属性已添加到 Azure 门户中“概述”边栏选项卡的右侧栏中。
* Azure Data Studio 支持为超大规模 PostgreSQL 配置工作器节点数、vCore 和内存设置。 
* 预览版支持 Postgres 版本 11 和 12 的备份/还原。

## <a name="september-2020"></a>2020 年 9 月

已启用 Azure Arc 的数据服务已发布公共预览版。 已启用 Arc 的数据服务允许你在任意位置管理数据服务。

- SQL 托管实例
- PostgreSQL 超大规模

有关说明，请参阅[什么是已启用 Azure Arc 的数据服务？](overview.md)

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 在 AKS、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 上或在 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。

- [安装客户端工具](install-client-tools.md)
- [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
- [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）
- [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
- [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)
