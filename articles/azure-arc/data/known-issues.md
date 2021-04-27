---
title: 已启用 Azure Arc 的数据服务 - 已知问题
description: 最新的已知问题
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: ee652047a33d73ece2d7648905fa590d90b1fb2f
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029499"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>已知问题 - 已启用 Azure Arc 的数据服务（预览版）

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="march-2021"></a>2021 年 3 月

### <a name="data-controller"></a>数据控制器

- 你可以使用 Azure 门户在直接连接模式下创建数据控制器。 不支持通过其他已启用 Azure Arc 的数据服务工具进行部署。 具体而言，在此版本中，你不能使用以下任何工具在直接连接模式下部署数据控制器。
   - Azure Data Studio
   - Azure Data CLI (`azdata`)
   - Kubernetes 本机工具

   [部署 Azure Arc 数据控制器 | 直接连接模式](deploy-data-controller-direct-mode.md)说明了如何在门户中创建数据控制器。 

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- 不支持在直接连接模式下启用的 Arc 数据控制器中部署已启用 Azure Arc 的 Postgres 超大规模服务器组。
- 在编辑服务器组的配置以启用其他扩展时，向 `--extensions` 参数传递无效值会错误地将启用的扩展列表重置为创建服务器组时的列表，并阻止用户创建其他扩展。 如发生该情况，唯一的解决方法是删除服务器组，然后重新部署。

## <a name="february-2021"></a>2021 年 2 月

### <a name="data-controller"></a>数据控制器

- 直接连接群集模式已禁用

### <a name="azure-arc-enabled-postgresql-hyperscale"></a>已启用 Azure Arc 的超大规模 PostgreSQL

- 目前不支持在 NFS 存储进行时间点还原。
- 不能同时启用和配置 pg_cron 扩展。 为此，你需要使用两个命令，分别是 启用命令和配置命令。 

   例如：
   ```console
   § azdata arc postgres server edit -n myservergroup --extensions pg_cron 
   § azdata arc postgres server edit -n myservergroup --engine-settings cron.database_name='postgres'
   ```

   第一个命令需要重启服务器组。 因此，在执行第二个命令之前，请确保服务器组的状态已从“正在更新”转换为“就绪”。 如果在重启之前执行第二个命令，该命令将失败。 如果命令失败，只需等待一段时间，然后再次执行第二个命令。

## <a name="introduced-prior-to-february-2021"></a>2021 年 2 月之前引入

### <a name="data-controller"></a>数据控制器

- 在 Azure Kubernetes 服务 (AKS) 上，不支持 Kubernetes 版本 1.19.x。
- 在 Kubernetes 1.19 上，不支持 `containerd`。
- Azure 中的数据控制器资源当前为 Azure 资源。 任何更新（例如删除）都不会传回 Kubernetes 群集。
- 实例名称不能超出 13 个字符
- Azure Arc 数据控制器或数据库实例不支持就地升级。
- 启用了 Arc 的数据服务容器映像未签名。  你可能需要将 Kubernetes 节点配置为允许拉取未签名的容器映像。  例如，如果使用 Docker 作为容器运行时，则可以设置 DOCKER_CONTENT_TRUST=0 环境变量，然后重启。  其他容器运行时具有类似的选项，例如在 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 无法从 Azure 门户创建已启用 Azure Arc 的 SQL 托管实例或超大规模 PostgreSQL 服务器组。
- 现阶段，如果使用的是 NFS，则需要在创建 Azure Arc 数据控制器之前，先在部署配置文件中将 `allowRunAsRoot` 设置为 `true`。
- 仅限 SQL 和 PostgreSQL 登录身份验证。  不支持 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上创建数据控制器时，需要放宽安全性约束。  有关详细信息，请参阅文档。
- 如果在 Azure Stack Hub 上使用的 Azure Kubernetes 服务 (AKS) 引擎中包含 Azure Arc 数据控制器和数据库实例，则不支持升级到较新的 Kubernetes 版本。 请先卸载 Azure Arc 数据控制器和所有数据库实例，然后再升级 Kubernetes 群集。
- 已启用 Azure Arc 的数据服务目前不支持跨[多个可用性区域](../../aks/availability-zones.md)的 AKS 群集。 为了避免此问题，在 Azure 门户中创建 AKS 群集时，如果选择多个区域可用的区域，请从选择控件中清除所有区域。 参看下图：

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="取消选中每个区域对应的复选框，以指定为“无”。":::


## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 在 AKS、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 上或在 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。

- [安装客户端工具](install-client-tools.md)
- [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
- [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）
- [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
- [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [发行说明 - 已启用 Azure Arc 的数据服务（预览版）](release-notes.md)
