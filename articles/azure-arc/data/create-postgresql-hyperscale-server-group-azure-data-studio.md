---
title: 使用 Azure Data Studio 创建已启用 Azure Arc 的超大规模 PostgreSQL
description: 使用 Azure Data Studio 创建已启用 Azure Arc 的超大规模 PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 3e71dc89ccf94462f83ce07e69bc57df86ae0a72
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121752376"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>使用 Azure Data Studio 创建已启用 Azure Arc 的超大规模 PostgreSQL

本文档指导你完成使用 Azure Data Studio 预配已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的预备步骤和临时步骤

在转到下一步骤之前请先执行此步骤。 若要将超大规模 PostgreSQL 服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为要运行超大规模 PostgreSQL 服务器组的服务帐户授予必要的特权。 安全上下文约束 (SCC) arc-data-scc 是部署 Azure arc 数据控制器时添加的。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

Server-group-name 是要在下一步骤中部署的服务器组的名称。
   
有关 OpenShift 中 SCC 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。
现在可以执行下一步骤。

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

1. 启动 Azure Data Studio
1. 在“连接”选项卡上，单击左上方的省略号图标并选择“新建部署”
1. 在部署选项中，选择“超大规模 PostgreSQL 服务器组 - Azure Arc”
    >[!NOTE]
    > 此时系统可能会提示安装 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]（如果当前未安装）。
1. 接受隐私和许可条款，然后单击底部的“选择”
1. 在“部署超大规模 PostgreSQL 服务器组 - Azure Arc”边栏选项卡中输入以下信息：
   - 为服务器组输入一个名称
   - 工作器节点数
   - 输入并确认服务器组的 postgres 管理员用户的密码
   - 为数据选择适当的存储类
   - 为日志选择适当的存储类
   - 为备份选择适当的存储类
   - 选择要预配的工作器节点数
1. 单击“部署”按钮

随即开始在数据控制器上创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。

几分钟后，创建过程应会成功完成。

### <a name="important-parameters-you-should-consider"></a>应考虑的重要参数包括：

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
    - 要设置备份的存储类：在此已启用 Azure Arc 的超大规模 PostgreSQL 预览版中，可以通过两种方法设置存储类，具体取决于要执行的备份/还原操作的类型。 我们正在努力简化此体验。 你将指示存储类或卷声明装载。 卷声明装载是一对现有永久性卷声明（相同命名空间中）和卷类型（和可选元数据，具体取决于卷类型），用冒号分隔。 将在 PostgreSQL 服务器组的每个 pod 中装载永久性卷。
        - 如果要计划仅执行完整的数据库还原，请设置参数 `--storage-class-backups` 或 `-scb`，后跟存储类的名称。
        - 如果计划同时执行完整的数据库还原和时间点还原，请设置参数 `--volume-claim-mounts` 或 `--volume-claim-mounts`，后跟卷声明和卷类型的名称。


## <a name="next-steps"></a>后续步骤
- [使用 Azure Data Studio 管理服务器组](manage-postgresql-hyperscale-server-group-with-azure-data-studio.md)
- [监视服务器组](monitor-grafana-kibana.md)
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作指南，以将数据分布到多个超大规模 PostgreSQL 节点，并受益于 Azure Database for Postgres 超大规模的所有强大功能。 :
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

    > 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

