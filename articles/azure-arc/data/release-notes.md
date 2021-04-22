---
title: 已启用 Azure Arc 的数据服务 - 发行说明
description: 最新发行说明
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 6b4d5c1372a8351f1fe5a6608aff38bf232aabd8
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121943"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>发行说明 - 已启用 Azure Arc 的数据服务（预览版）

本文重点介绍了已启用 Azure Arc 的数据服务的最近发布或改进的功能、特性和增强功能。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.3.1。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata)。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

其他更新包括：

- 已启用 Azure Arc 的 SQL 托管实例
   - 使用 Always On 可用性组实现高可用性

- 已启用 Azure Arc 的超大规模 PostgreSQL Azure Data Studio： 
   - 概述页现在显示按节点分项的服务器组的状态
   - 现在可以使用新的属性页显示有关服务器组的更多详细信息
   - 从“节点参数”页配置 Postgres 引擎参数

有关与此版本相关的问题，请参阅[已知问题 - 已启用 Azure Arc 的数据服务（预览版）](known-issues.md)

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.3.0。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata)。 可以从[安装 Azure 数据 CLI (`azdata`)](/sql/azdata/install/deploy-install-azdata) 中安装 `azdata`。

其他更新包括：
- 适用于 17 种新语言的本地化门户
- 对 Kube 本机 .yaml 文件进行了细微的更改
- 新版本的 Grafana 和 Kibana
- 已解决在 Azure Data Studio 的笔记本中使用 azdata 时 Python 环境的问题
- pg_audit 扩展现可用于超大规模 PostgreSQL
- 执行超大规模 PostgreSQL 数据库的完全还原时，不再需要备份 ID
- 将报告构成服务器组的每个 PostgreSQL 实例的状态（运行状况状态）

   在早期版本中，状态在服务器组级别聚合，而不是在 PostgreSQL 节点级别逐项列出。

- PostgreSQL 部署现在采用 create 命令中指定的卷大小参数
- 现在，在编辑服务器组时采用引擎版本参数
- 已启用 Azure Arc 的超大规模 PostgreSQL 的 pod 的命名约定已更改

    它现在采用以下形式：`ServergroupName{c, w}-n`。 例如，一个具有三个节点（一个协调器节点和两个工作器节点）的服务器组表示为：
   - `Postgres01c-0`（协调器节点）
   - `Postgres01w-0`（工作器节点）
   - `Postgres01w-1`（工作器节点）

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新功能和特性

Azure 数据 CLI (`azdata`) 版本号：20.2.5。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata)。

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

### <a name="known-issues"></a>已知问题

- 在 Azure Kubernetes 服务 (AKS) 上，不支持 Kubernetes 版本 1.19.x。
- 在 Kubernetes 1.19 上，不支持 `containerd`。
- Azure 中的数据控制器资源当前为 Azure 资源。 任何更新（例如删除）都不会传回 Kubernetes 群集。
- 实例名称不能超出 13 个字符
- Azure Arc 数据控制器或数据库实例不支持就地升级。
- 启用了 Arc 的数据服务容器映像未签名。  你可能需要将 Kubernetes 节点配置为允许拉取未签名的容器映像。  例如，如果使用 Docker 作为容器运行时，则可以设置 DOCKER_CONTENT_TRUST=0 环境变量，然后重启。  其他容器运行时具有类似的选项，例如在 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 无法从 Azure 门户创建已启用 Azure Arc 的 SQL 托管实例或 PostgreSQL 超大规模服务器组。
- 现阶段，如果使用的是 NFS，则需要在创建 Azure Arc 数据控制器之前，先在部署配置文件中将 `allowRunAsRoot` 设置为 `true`。
- 仅限 SQL 和 PostgreSQL 登录身份验证。  不支持 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上创建数据控制器时，需要放宽安全性约束。  有关详细信息，请参阅文档。
- 如果在 Azure Stack Hub 上使用的 Azure Kubernetes 服务 (AKS) 引擎中包含 Azure Arc 数据控制器和数据库实例，则不支持升级到较新的 Kubernetes 版本。 请先卸载 Azure Arc 数据控制器和所有数据库实例，然后再升级 Kubernetes 群集。
- 已启用 Azure Arc 的数据服务目前不支持跨[多个可用性区域](../../aks/availability-zones.md)的 AKS 群集。 若要避免此问题，在 Azure 门户中创建 AKS 群集时，如果选择有可用性区域的区域，请从选择控件中清除所有区域。 参看下图：

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="取消选中每个区域对应的复选框，以指定为“无”。":::

#### <a name="postgresql"></a>PostgreSQL

- 如果已启用 Azure Arc 的超大规模 PostgreSQL 无法还原到指示的相对时间点，将返回不准确的错误消息。 例如，当指定的还原时间点早于备份包含的时间点时，还原将失败并出现如下错误消息：错误: (404)。 原因: 未找到。 HTTP 响应正文: {"代码":404，"internalStatus": "NOT_FOUND"，"原因":"未能还原服务器的备份...}
发生这种情况时，请指示一个处于备份对应的日期范围内的时间点，然后重启该命令。 通过列出备份并查看对应日期，即可确定此范围。
- 仅在服务器组上支持时间点还原。 时间点还原操作的目标服务器不能是备份时所用的服务器。 必须选择其他服务器组。 但是，支持对同一服务器组进行完全还原。
- 执行完整还原时，需要备份 ID。 默认情况下，如果未指示备份 ID，则将使用最新的备份。 这不适用于本版本。

## <a name="october-2020"></a>2020 年 10 月 

Azure 数据 CLI (`azdata`) 版本号：20.2.3。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata)。

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
