---
title: 启用了 Azure Arc 的数据服务-已知问题
description: 最新已知问题
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
ms.date: 03/02/2021
ms.topic: conceptual
ms.openlocfilehash: 8100d9e12f107e0c4598876c46453b46c6ee4d0e
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/04/2021
ms.locfileid: "102121994"
---
# <a name="known-issues---azure-arc-enabled-data-services-preview"></a>已知问题-启用了 Azure Arc 的数据服务 (预览) 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="february-2021"></a>2021 年 2 月

- 已禁用连接群集模式

## <a name="introduced-prior-to-february-2021"></a>2021年2月之前引入

### <a name="data-controller"></a>数据控制器

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

### <a name="postgresql"></a>PostgreSQL

- 如果启用了 Azure Arc 的 PostgreSQL 超大规模，则当其无法还原到指示的相对时间点时，它将返回不准确的错误消息。 例如，如果你指定了还原时间比你的备份包含的时间点，则还原将失败并出现如下错误消息： `ERROR: (404). Reason: Not found. HTTP response body: {"code":404, "internalStatus":"NOT_FOUND", "reason":"Failed to restore backup for server...}`
发生这种情况时，请在指示时间点处于备份的日期范围内时重新启动该命令。 你将通过列出你的备份，并查看其拍摄日期来确定此范围。
- 仅在服务器组上支持时间点还原。 时间点还原操作的目标服务器不能是进行备份所用的服务器。 它必须是其他服务器组。 但是，支持对同一服务器组进行完全还原。
- 执行完整还原时需要备份 id。 默认情况下，如果不指示备份 id，将使用最新的备份。 这在此版本中不起作用。

## <a name="next-steps"></a>后续步骤

> 想尝试一下吗？  
> 使用 [Azure Arc Jumpstart](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/) 快速入门： AKS、AWS 弹性 Kubernetes SERVICE (EKS) 、Google Cloud Kubernetes ENGINE (GKE) 或 Azure VM。

- [安装客户端工具](install-client-tools.md)
- [创建 Azure Arc 数据控制器](create-data-controller.md)（首先需要安装客户端工具）
- [在 Azure Arc 上创建 Azure SQL 托管实例](create-sql-managed-instance.md)（首先需要创建 Azure Arc 数据控制器）
- [在 Azure Arc 上创建 Azure Database for PostgreSQL 超大规模服务器组](create-postgresql-hyperscale-server-group.md)（首先需要创建 Azure Arc 数据控制器）
- [Azure 服务的资源提供程序](../../azure-resource-manager/management/azure-services-resource-providers.md)
- [发行说明-启用了 Azure Arc 的数据服务 (预览) ](release-notes.md)
