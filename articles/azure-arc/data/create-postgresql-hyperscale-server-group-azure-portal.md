---
title: 从 Azure 门户创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
description: 从 Azure 门户创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 04/28/2021
ms.topic: how-to
ms.openlocfilehash: 5cc99acf303b99e138edd0418d2d49824bbf27fe
ms.sourcegitcommit: fc9fd6e72297de6e87c9cf0d58edd632a8fb2552
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/30/2021
ms.locfileid: "108293940"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>从 Azure 门户创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

本文档介绍从 Azure 门户在 Azure Arc 上创建超大规模 PostgreSQL 服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="getting-started"></a>入门
如果你已熟悉以下主题，则可以跳过此段落。
在继续创建之前，你可能需要先阅读一些重要的主题：
- [已启用 Azure Arc 的数据服务概述](overview.md)
- [连接模式和要求](connectivity.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

如果你想要在不自行预配完整环境的情况下进行试用，请在 Azure Kubernetes 服务 (AKS)、AWS Elastic Kubernetes 服务 (EKS)、Google Cloud Kubernetes Engine (GKE) 或 Azure VM 中，通过 [Azure Arc 快速入门](https://azurearcjumpstart.io/azure_arc_jumpstart/azure_arc_data/)快速开始操作。


## <a name="deploy-an-arc-data-controller-configured-to-use-the-direct-connectivity-mode"></a>部署配置为使用直接连接模式的 Arc 数据控制器

要求：在从 Azure 门户操作和部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组之前，必须首先部署配置为使用直接连接模式的 Azure Arc 数据控制器。
若要部署 Arc 数据控制器，请完成以下文章中的说明：
1. [部署数据控制器 - 直接连接模式（先决条件）](deploy-data-controller-direct-mode-prerequisites.md)
1. [部署 Azure Arc 数据控制器 | 直接连接模式](deploy-data-controller-direct-mode.md)


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的预备步骤和临时步骤
在转到下一步骤之前请先执行此步骤。 若要将超大规模 PostgreSQL 服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为要运行超大规模 PostgreSQL 服务器组的服务帐户授予必要的特权。 安全性上下文约束 (SCC) arc-data-scc 是部署 Azure arc 数据控制器时添加的。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

Server-group-name 是要在下一步骤中创建的服务器组的名称。

有关 OpenShift 中 SCC 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。 

继续下一步。

## <a name="deploy-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>从 Azure 门户部署已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

若要从 Azure 门户部署和操作已启用 Azure Arc 的超大规模 Postgres 服务器组，必须将其部署到配置为使用直接连接模式的 Arc 数据控制器。 

> [!IMPORTANT]
> 如果已将已启用 Azure Arc 的超大规模 PostgreSQL 服务器组部署到配置为使用间接连接模式的 Azure Arc 数据控制器，则无法从 Azure 门户对此服务器组进行操作。 

部署启用了直接连接模式的 Arc 数据控制器后：
1. 在浏览器中，转到 URL [https://portal.azure.com](https://portal.azure.com)
2. 在页面顶部的“搜索”窗口中，在 Azure 市场中搜索“azure arc postgres”，然后选择“Azure Database for PostgreSQL 服务器组 - Azure Arc”。
3. 在打开的页面中，单击左上角的“+ 创建”。 
4. 像部署其他 Azure 资源一样填写表单。


### <a name="important-parameters-you-should-consider-are"></a>应考虑的重要参数包括：

- 要部署的工作器节点数，以进行横向扩展并可能达到更好的性能。 在继续操作之前，请阅读[有关超大规模 Postgres 的概念](concepts-distributed-postgres-hyperscale.md)。 例如，如果部署具有两个工作器节点的服务器组，则部署将创建三个 pod，一个用于协调器节点/实例，两个用于工作器节点/实例（每个对应于一个工作器）。

## <a name="next-steps"></a>后续步骤

- 连接到已启用 Azure Arc 的超大规模 PostgreSQL：阅读[获取连接终结点和连接字符串](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分布到多个超大规模 PostgreSQL 节点并可能从更好的性能中获益：
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

- [横向扩展已启用 Azure Arc 的超大规模 PostgreSQL 服务器组](scale-out-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


