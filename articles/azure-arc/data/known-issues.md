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
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121994"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>已知问题 - 已启用 Azure Arc 的数据服务（预览版）

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 年 2 月

- 连接群集模式已禁用

## <a name="introduced-prior-to-february-2021"></a>2021 年 2 月之前引入

### <a name="data-controller"></a>数据控制器

- 在 Azure Kubernetes 服务 (AKS) 上，不支持 Kubernetes 版本 1.19.x。
- 在 Kubernetes 1.19 上，不支持 `containerd`。
- Azure 中的数据控制器资源当前为 Azure 资源。 任何更新（例如删除）都不会传回 kubernetes 群集。
- 实例名称不能超出 13 个字符
- Azure Arc 数据控制器或数据库实例不支持就地升级。
- 启用了 Arc 的数据服务容器映像未签名。  你可能需要将 Kubernetes 节点配置为允许拉取未签名的容器映像。  例如，使用 Docker 作为容器运行时，可以设置 DOCKER_CONTENT_TRUST=0 环境变量，然后重启。  其他容器运行时具有类似的选项，例如在 [OpenShift](https://docs.openshift.com/container-platform/4.5/openshift_images/image-configuration.html#images-configuration-file_image-configuration) 中就是如此。
- 无法从 Azure 门户创建已启用 Azure Arc 的 SQL 托管实例或超大规模 PostgreSQL 服务器组。
- 现阶段，如果使用的是 NFS，则需要在创建 Azure Arc 数据控制器之前，先在部署配置文件中将 `allowRunAsRoot` 设置为 `true`。
- 仅限 SQL 和 PostgreSQL 登录身份验证。  不支持 Azure Active Directory 或 Active Directory。
- 在 OpenShift 上创建数据控制器时，需要放宽安全性约束。  有关详细信息，请参阅文档。
- 如果在 Azure Stack Hub 上使用的 Azure Kubernetes 服务 (AKS) 引擎中包含 Azure Arc 数据控制器和数据库实例，则不支持升级到较新的 Kubernetes 版本。 请先卸载 Azure Arc 数据控制器和所有数据库实例，然后再升级 Kubernetes 群集。
- 已启用 Azure Arc 的数据服务目前不支持跨[多个可用性区域](../../aks/availability-zones.md)的 AKS 群集。 为了避免此问题，在 Azure 门户中创建 AKS 群集时，如果选择多个区域可用的区域，请从选择控件中清除所有区域。 参看下图：

   :::image type="content" source="media/release-notes/aks-zone-selector.png" alt-text="取消选中每个区域对应的复选框，以指定为“无”。":::

### <a name="postgresql"></a>PostgreSQL

- 如果已启用 Azure Arc 的超大规模 PostgreSQL 无法还原到指示的相对时间点，将返回不准确的错误消息。 例如，当指定的还原时间点早于备份包含的时间点时，还原将失败并出现如下错误消息：`ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
发生这种情况时，请指示一个处于备份对应的日期范围内的时间点，然后重启该命令。 通过列出备份并查看进行备份的日期，即可确定此范围。
- 仅在服务器组上支持时间点还原。 时间点还原操作的目标服务器不能是备份时所用的服务器。 必须选择其他服务器组。 但是，支持对同一服务器组进行完全还原。
- 执行完整还原时，需要备份 ID。 默认情况下，如果未指示备份 ID，则将使用最新的备份。 这不适用于此版本。

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 在 AKS、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 上或在 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。

- [安装客户端工具](install-client-tools.md)
- [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
- [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）
- [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
- [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [发行说明 - 已启用 Azure Arc 的数据服务（预览版）](release-notes.md)
