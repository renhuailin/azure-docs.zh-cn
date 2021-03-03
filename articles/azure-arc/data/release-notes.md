---
title: 启用了 Azure Arc 的数据服务-发行说明
description: 最新发行说明
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: d3eb4e4b45a13872a11515dd4f53fcbbc069a954
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101686676"
---
# <a name="release-notes---azure-arc-enabled-data-services-preview"></a>发行说明-启用了 Azure Arc 的数据服务 (预览) 

本文重点介绍了 Azure Arc 启用的数据服务最近发布或改进的功能、功能和增强功能。 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 年 2 月

### <a name="new-capabilities-and-features"></a>新功能和功能

Azure 数据 CLI (`azdata`) 版本号：20.3.1。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata) 。 你可以 `azdata` 从 [安装 AZURE 数据 CLI (`azdata`) ](/sql/azdata/install/deploy-install-azdata)进行安装。

其他更新包括：

- 已启用 Azure Arc 的 SQL 托管实例
   - Always On 可用性组的高可用性

- 已启用 Azure Arc PostgreSQL 超大规模 Azure Data Studio： 
   - 概述页现在显示每个节点的服务器组的状态
   - 现在可以使用新的 "属性" 页显示有关服务器组的更多详细信息。
   - 通过 **节点参数** 配置 Postgres 引擎参数页 =

有关与此版本相关的问题，请参阅 [已知问题-启用了 Azure Arc 的数据服务 (预览) ](known-issues.md)

## <a name="january-2021"></a>2021 年 1 月

### <a name="new-capabilities-and-features"></a>新功能和功能

Azure 数据 CLI (`azdata`) 版本号：20.3.0。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata) 。 你可以 `azdata` 从 [安装 AZURE 数据 CLI (`azdata`) ](/sql/azdata/install/deploy-install-azdata)进行安装。

其他更新包括：
- 适用于17种新语言的本地化门户
- 对 Kube 的细微更改-yaml 文件
- 新版本的 Grafana 和 Kibana
- 在 Azure Data Studio 中使用笔记本中的 azdata 时，Python 环境出现问题
- Pg_audit 扩展现可用于 PostgreSQL 超大规模
- 在对 PostgreSQL 超大规模数据库执行完整还原时，不再需要备份 ID
- 为组成服务器组的每个 PostgreSQL 实例报告状态 (健康状况) 

   在早期版本中，状态在服务器组级别进行聚合，而不是在 PostgreSQL 节点级别进行详细介绍。

- PostgreSQL 部署现在服从 create 命令中指定的卷大小参数
- 在编辑服务器组时，引擎版本参数现已生效
- 启用了 Azure Arc PostgreSQL 超大规模的 pod 的命名约定已更改

    它现在采用以下格式： `ServergroupName{c, w}-n` 。 例如，具有三个节点的服务器组、一个协调器节点和两个工作节点均表示为：
   - `Postgres01c-0` (协调器 "节点) 
   - `Postgres01w-0` (辅助节点) 
   - `Postgres01w-1` (辅助节点) 

## <a name="december-2020"></a>2020 年 12 月

### <a name="new-capabilities--features"></a>新功能 & 功能

Azure 数据 CLI (`azdata`) 版本号：20.2.5。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata) 。

使用 Azure 数据 CLI (`azdata`) 和命令查看 SQL 托管实例和 PostgreSQL 超大规模的终结 `azdata arc sql endpoint list` 点 `azdata arc postgres endpoint list` 。

使用 Azure Data Studio 编辑 SQL 托管实例资源 (CPU 核心和内存) 请求和限制。

启用 Azure Arc 后，PostgreSQL 超大规模现在支持时间点还原，以及针对 PostgreSQL 版本11和12的完整备份还原。 使用时间点还原功能可以指示要还原到的特定日期和时间。

启用了 Azure Arc PostgreSQL 超大规模的 pod 的命名约定已更改。 它的格式现在为： ServergroupName {r，s}-_n_。 例如，具有三个节点的服务器组、一个协调器节点和两个工作节点均表示为：
- `postgres02r-0` (协调器 "节点) 
- `postgres02s-0` (辅助节点) 
- `postgres02s-1` (辅助节点) 

### <a name="breaking-change"></a>重大更改

#### <a name="new-resource-provider"></a>新资源提供程序

此版本引入了一个称为 `Microsoft.AzureArcData` 的更新的[资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)。 你需要注册此资源提供程序，然后才能使用此功能。 

注册此资源提供程序： 

1. 在 Azure 门户中，选择 "**订阅**" 
2. 选择自己的订阅
3. 在“设置”下，选择“资源提供程序”  
4. 搜索 `Microsoft.AzureArcData`，并选择“注册” 

可以在 [Azure 资源提供程序和类型](../../azure-resource-manager/management/resource-providers-and-types.md)上查看详细步骤。 此更改还会删除已上载到 Azure 门户的所有现有 Azure 资源。 若要使用资源提供程序，需要更新数据控制器并使用最新的 `azdata` CLI。  

### <a name="platform-release-notes"></a>平台发行说明

#### <a name="direct-connectivity-mode"></a>直接连接模式

此版本引入了直接连接模式。 直接连接模式使数据控制器能够自动将使用情况信息上载到 Azure。 在使用情况上载过程中，将在门户中自动创建 Arc 数据控制器资源（如果尚未通过 `azdata` 上传创建）。  

创建数据控制器时，可以指定直接连接。 下面的示例 `azdata arc dc create` `arc` 使用 () 的直接连接模式创建名为的数据控制器 `connectivity-mode direct` 。 在运行该示例之前，请 `<subscription id>` 将替换为你的订阅 ID。

```console
azdata arc dc create --profile-name azure-arc-aks-hci --namespace arc --name arc --subscription <subscription id> --resource-group my-resource-group --location eastus --connectivity-mode direct
```

### <a name="known-issues"></a>已知问题

- 在 Azure Kubernetes Service (AKS) 上，不支持 Kubernetes 版本1.19。
- 不支持在 Kubernetes 1.19 上 `containerd` 。
- Azure 中的数据控制器资源当前为 Azure 资源。 任何更新（例如删除）都不会传播回 kubernetes 群集。
- 实例名称不能超过13个字符
- Azure Arc 数据控制器或数据库实例没有就地升级。
- 启用了 Arc 的数据服务容器映像未签名。  你可能需要将 Kubernetes 节点配置为允许拉取未签名的容器映像。  例如，如果使用 Docker 作为容器运行时，则可以设置 DOCKER_CONTENT_TRUST = 0 环境变量并重新启动。  其他容器运行时具有类似的选项，例如在 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 无法从 Azure 门户创建启用了 Azure Arc 的 SQL 托管实例或 PostgreSQL 超大规模服务器组。
- 现在，如果使用的是 NFS，则需要 `allowRunAsRoot` `true` 在创建 Azure Arc 数据控制器之前在部署配置文件中将设置为。
- 仅限 SQL 和 PostgreSQL 登录身份验证。  不支持 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上创建数据控制器需要严格的安全约束。  有关详细信息，请参阅文档。
- 如果使用 azure Kubernetes Service (使用 Azure Arc 数据控制器和数据库实例 Azure Stack 集线器上的 AKS) 引擎，则不支持升级到较新的 Kubernetes 版本。 请先卸载 Azure Arc 数据控制器和所有数据库实例，然后再升级 Kubernetes 群集。
- 支持 Azure Arc 的数据服务目前不支持跨 [多个可用性区域](../../aks/availability-zones.md) 的 AKS 群集。 若要避免此问题，在 Azure 门户中创建 AKS 群集时，如果选择区域可用的区域，请从选择控件中清除所有区域。 参看下图：

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="清除每个区域的复选框以指定 &quot;无&quot;。":::

#### <a name="postgresql"></a>PostgreSQL

- 如果启用了 Azure Arc 的 PostgreSQL 超大规模，则当其无法还原到指示的相对时间点时，它将返回不准确的错误消息。 例如，如果你指定了还原时间比你的备份包含的时间点，则还原将失败并出现如下错误消息：错误： (404) 。 原因：找不到。 HTTP 响应正文： {"代码"：404，"internalStatus"： "NOT_FOUND"，"原因"： "未能还原服务器的备份 ...}"
发生这种情况时，请在指示时间点处于备份的日期范围内时重新启动该命令。 你将通过列出你的备份，并查看其拍摄日期来确定此范围。
- 仅在服务器组上支持时间点还原。 时间点还原操作的目标服务器不能是进行备份所用的服务器。 它必须是其他服务器组。 但是，支持对同一服务器组进行完全还原。
- 执行完整还原时需要备份 id。 默认情况下，如果不指示备份 id，将使用最新的备份。 这在此版本中不起作用。

## <a name="october-2020"></a>2020 年 10 月 

Azure 数据 CLI (`azdata`) 版本号：20.2.3。 下载位置 [https://aka.ms/azdata](https://aka.ms/azdata) 。

### <a name="breaking-changes"></a>中断性变更

此版本引入了以下重大更改： 

* 在 PostgreSQL 自定义资源定义 (.CRD) 中，术语 `shards` 被重命名为 `workers` 。 此术语 (`workers`) 与命令行参数名称匹配。

* `azdata arc postgres server delete` 删除 postgres 实例之前提示确认。  使用 `--force` 跳过提示。

### <a name="additional-changes"></a>其他更改

* 添加了一个名为的新可选参数 `azdata arc postgres server create` `--volume-claim mounts` 。 该值是一个以逗号分隔的卷声明装载列表。 卷声明装载是一对卷类型和 PVC 名称。 目前唯一支持的卷类型是 `backup` 。  在 PostgreSQL 中，当卷类型为时 `backup` ，PVC 将装载到 `/mnt/db-backups` 。  这样就可以在 PostgresSQL 实例之间共享备份，以便在另一个实例中还原一个 PostgresSQL 实例的备份。

* PostgresSQL 自定义资源定义的新短名称： 
  * `pg11` 
  * `pg12`
* 遥测上传为用户提供了：
   * 上载到 Azure 或的点数 
   * 如果没有数据加载到 Azure，则会提示再次尝试。
* `azdata arc dc debug copy-logs` 接下来，还会从 `/var/opt/controller/log` 文件夹读取并收集 Linux 上的 PostgreSQL 引擎日志。
*   当使用 PostgreSQL 超大规模创建和还原备份时，显示工作指示器。
* `azdata arc postrgres backup list` 现在包含备份大小信息。
* 已将 SQL 托管实例 admin name 属性添加到 Azure 门户中 "概述" 边栏选项卡的右侧列。
* Azure Data Studio 支持配置 PostgreSQL 超大规模的辅助角色节点、vCore 和内存设置的数目。 
* 预览版支持 Postgres 版本11和12的备份/还原。

## <a name="september-2020"></a>2020 年 9 月

已为公共预览版发布启用了 Azure Arc 的数据服务。 启用 Arc 的数据服务允许您在任意位置管理数据服务。

- SQL 托管实例
- PostgreSQL 超大规模

有关说明，请参阅 [什么是启用了 Azure Arc 的数据服务？](overview.md)

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 使用 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 快速入门： AKS、AWS 弹性 Kubernetes SERVICE (EKS) 、Google Cloud Kubernetes ENGINE (GKE) 或 Azure VM。

- [安装客户端工具](install-client-tools.md)
- [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
- [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）
- [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
- [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)
