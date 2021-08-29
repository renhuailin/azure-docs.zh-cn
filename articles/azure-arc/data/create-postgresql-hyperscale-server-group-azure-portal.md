---
title: 从 Azure 门户创建已启用 Azure Arc 的 PostgreSQL 超大规模服务器组
description: 从 Azure 门户创建已启用 Azure Arc 的 PostgreSQL 超大规模服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 382e4e855b0a4925cfaae2f7746587a7ab2fd951
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121750317"
---
# <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>从 Azure 门户创建已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

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

要求：在部署从 Azure 门户操作的已启用 Azure Arc 的 PostgreSQL 超大规模服务器组之前，必须先部署已配置为使用“直接”连接模式的 Azure Arc 数据控制器。
若要部署 Arc 数据控制器，请完成以下文章中的说明：
1. [部署数据控制器 - 直接连接模式（先决条件）](create-data-controller-direct-prerequisites.md)
1. [从 Azure 门户以直接连接模式部署 Azure Arc 数据控制器](create-data-controller-direct-azure-portal.md)


## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的预备步骤和临时步骤
在转到下一步骤之前请先执行此步骤。 若要将超大规模 PostgreSQL 服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为要运行超大规模 PostgreSQL 服务器组的服务帐户授予必要的特权。 安全性上下文约束 (SCC) arc-data-scc 是部署 Azure arc 数据控制器时添加的。

```Console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

Server-group-name 是要在下一步骤中创建的服务器组的名称。

有关 OpenShift 中 SCC 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。 

继续下一步。

## <a name="deploy-an-azure-arc-enabled-postgresql-hyperscale-server-group-from-the-azure-portal"></a>从 Azure 门户部署已启用 Azure Arc 的 PostgreSQL 超大规模服务器组

若要从 Azure 门户部署和操作已启用 Azure Arc 的 Postgres 超大规模服务器组，必须将其部署到已配置为使用直接连接模式的 Arc 数据控制器。 

> [!IMPORTANT]
> 如果已将已启用 Azure Arc 的 PostgreSQL 超大规模服务器组部署到已配置为使用间接连接模式的 Azure Arc 数据控制器，则无法从 Azure 门户对此服务器组进行操作。 

部署为直接连接模式启用的 Arc 数据控制器后，可以选择以下三个选项之一来部署已启用 Azure Arc 的 Postgres 超大规模服务器组：

### <a name="option-1-deploy-from-the-azure-marketplace"></a>选项 1：从 Azure 市场部署
1. 打开浏览器访问以下 URL [https://portal.azure.com](https://portal.azure.com)
2. 在页面顶部的“搜索”窗口中，在 Azure 市场中搜索“azure arc postgres”，然后选择“Azure Database for PostgreSQL 服务器组 - Azure Arc”。
3. 在打开的页面中，单击左上角的“+ 创建”。 
4. 像部署其他 Azure 资源一样填写表单。

### <a name="option-2-deploy-from-the-azure-database-for-postgresql-deployment-option-page"></a>选项 2：从 Azure Database for PostgreSQL 部署选项页进行部署
1. 打开浏览器访问以下 URL https://ms.portal.azure.com/#create/Microsoft.PostgreSQLServer 。
2. 单击右下角的磁贴。 其标题为：已启用 Azure Arc 的 PostgreSQL 超大规模服务器组（预览版）。
3. 像部署其他 Azure 资源一样填写表单。

### <a name="option-3-deploy-from-the-azure-arc-center"></a>选项 3：从 Azure Arc 中心部署
1. 打开浏览器访问以下 URL https://ms.portal.azure.com/#blade/Microsoft_Azure_HybridCompute/AzureArcCenterBlade/overview
1. 在页面中心，单击“部署 Azure 服务”磁贴下的“部署”，然后单击“PostgreSQL 超大规模(预览)”的磁贴中的“部署”。
2. 或者，在页面左侧导航窗格中的“服务”部分，单击“PostgreSQL 超大规模(预览)”，然后单击窗格左上角的“+ 创建”。


#### <a name="important-parameters-you-should-consider"></a>应考虑的重要参数包括：

- 要部署的工作器节点数，以进行横向扩展并可能达到更好的性能。 在继续操作之前，请先阅读[有关超大规模 Postgres 的概念](concepts-distributed-postgres-hyperscale.md)。 下表显示支持值的范围以及可使用它们获得的 Postgres 部署形式。 例如，如果要部署包含 2 个工作器节点的服务器组，则显示 2。 这将创建三个 pod：一个用于协调器节点/实例，两个用于工作器节点/实例（一个工作器一个）。



|需要：   |要部署的服务器组的形状   |要显示的工作器节点数   |注意   |
|---|---|---|---|
|Postgres 的横向扩展形式，可满足应用程序的可伸缩性需求。   |3 个或更多 Postgres 实例，其中 1 个是协调器，n 个工作器，n >= 2。   |n，n >= 2。   |加载了提供超大规模功能的 Citus 扩展。   |
|Postgres 超大规模的基本形式，用于以最低成本对应用程序执行功能验证。 对性能和可伸缩性验证无效。 为此，你需要使用上述部署类型。   |1 个既充当协调器又充当工作器的 Postgres 实例。   |0，将 Citus 添加到要加载的扩展列表中。   |加载了提供超大规模功能的 Citus 扩展。   |
|一个 Postgres 简单实例，可随时根据需要横向扩展。   |1 个 Postgres 实例。 它尚不了解协调器与工作器的语义。 若要在部署后横向扩展，请编辑配置、增加工作器节点数并分发数据。   |0   |提供超大规模功能的 Citus 扩展存在于部署中，但尚未加载。   |
|   |   |   |   |

虽然指示 1 个工作器可用，但我们不建议使用它。 此部署不会带来多大价值。 使用它可以获取 2 个 Postgres 实例：1 个协调器和 1 个工作器。 使用此设置，实际上不会横向扩展数据，因为你只部署了一个工作器。 因此，你不会看到性能和可伸缩性水平有所提高。 我们将在将来的版本中删除对此部署的支持。

- 希望服务器组使用的存储类。 请务必在部署服务器组时设置存储类，因为在部署后便无法更改此设置。 如果要在部署后更改存储类，则需要提取数据、删除服务器组、创建新的服务器组，然后导入数据。 可以指定用于数据、日志和备份的存储类。 默认情况下，如果不指示存储类，则将使用数据控制器的存储类。
    - 要设置数据的存储类，请指示参数 `--storage-class-data` 或 `-scd`，后跟存储类的名称。
    - 要设置日志的存储类，请指示参数 `--storage-class-logs` 或 `-scl`，后跟存储类的名称。
    - 设置备份的存储类：在此已启用 Azure Arc 的 PostgreSQL 超大规模服务器组预览版中，可以通过两种方法设置存储类，具体取决于要执行的备份/还原操作的类型。 我们正在努力简化此体验。 你将指示存储类或卷声明装载。 卷声明装载是一对现有永久性卷声明（相同命名空间中）和卷类型（和可选元数据，具体取决于卷类型），用冒号分隔。 将在 PostgreSQL 服务器组的每个 pod 中装载永久性卷。
        - 如果要计划仅执行完整的数据库还原，请设置参数 `--storage-class-backups` 或 `-scb`，后跟存储类的名称。
        - 如果计划同时执行完整的数据库还原和时间点还原，请设置参数 `--volume-claim-mounts` 或 `--volume-claim-mounts`，后跟卷声明和卷类型的名称。


## <a name="next-steps"></a>后续步骤

- 连接到已启用 Azure Arc 的 PostgreSQL 超大规模服务器组：请阅读[获取连接终结点和连接字符串](get-connection-endpoints-and-connection-strings-postgres-hyperscale.md)
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分布到多个超大规模 PostgreSQL 节点并可能从更好的性能中获益：
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的“Azure Database for PostgreSQL 超大规模(Citus)服务器组”，但文档的其他部分直接适用于已启用 Azure Arc 的 PostgreSQL 超大规模服务器组。

- [横向扩展已启用 Azure Arc 的 PostgreSQL 超大规模服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [扩展永久性卷声明](https://kubernetes.io/docs/concepts/storage/persistent-volumes/#expanding-persistent-volumes-claims)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)


