---
title: 使用 Azure Data Studio 创建已启用 Azure Arc 的超大规模 PostgreSQL
description: 使用 Azure Data Studio 创建已启用 Azure Arc 的超大规模 PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: e2007d8f0c558d35c0507b6e12bce6d6777fad52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/29/2021
ms.locfileid: "92310902"
---
# <a name="create-azure-arc-enabled-postgresql-hyperscale-using-azure-data-studio"></a>使用 Azure Data Studio 创建已启用 Azure Arc 的超大规模 PostgreSQL

本文档指导你完成使用 Azure Data Studio 预配已启用 Azure Arc 的超大规模 PostgreSQL 服务器组的步骤。

[!INCLUDE [azure-arc-common-prerequisites](../../../includes/azure-arc-common-prerequisites.md)]

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="connect-to-the-azure-arc-data-controller"></a>连接到 Azure Arc 数据控制器

在创建实例之前，如果你尚未登录到 Azure Arc 数据控制器，请先登录。

```console
azdata login
```

然后，系统将提示输入要在其中创建数据控制器的命名空间，以及用于登录到控制器的用户名和密码。

> 如果需要验证命名空间，可以运行 ```kubectl get pods -A``` 来获取群集上所有命名空间的列表。

```console
Username: arcadmin
Password:
Namespace: arc
Logged in successfully to `https://10.0.0.4:30080` in namespace `arc`. Setting active context to `arc`
```

## <a name="preliminary-and-temporary-step-for-openshift-users-only"></a>仅适用于 OpenShift 用户的预备步骤和临时步骤

在转到下一步骤之前请先执行此步骤。 若要将超大规模 PostgreSQL 服务器组部署到非默认项目中的 Red Hat OpenShift，需要针对群集执行以下命令，以更新安全约束。 此命令为要运行超大规模 PostgreSQL 服务器组的服务帐户授予必要的特权。 安全上下文约束 (SCC) arc-data-scc 是部署 Azure arc 数据控制器时添加的。

```console
oc adm policy add-scc-to-user arc-data-scc -z <server-group-name> -n <namespace name>
```

Server-group-name 是要在下一步骤中部署的服务器组的名称。
   
有关 OpenShift 中 SCC 的更多详细信息，请参阅 [OpenShift 文档](https://docs.openshift.com/container-platform/4.2/authentication/managing-security-context-constraints.html)。
现在可以执行下一步骤。

## <a name="create-an-azure-arc-enabled-postgresql-hyperscale-server-group"></a>创建启用了 Azure Arc 的 PostgreSQL 超大规模服务器组

1. 启动 Azure Data Studio
1. 在“连接”选项卡上，单击左上方的省略号图标并选择“新建部署”
1. 在部署选项中，选择“超大规模 PostgreSQL 服务器组 - Azure Arc”
    >[!NOTE]
    > 此时系统可能会提示安装 [!INCLUDE [azure-data-cli-azdata](../../../includes/azure-data-cli-azdata.md)]（如果当前未安装）。
1. 接受隐私和许可条款，然后单击底部的“选择”
1. 在“部署超大规模 PostgreSQL 服务器组 - Azure Arc”边栏选项卡中输入以下信息：
   - 为服务器组输入一个名称
   - 输入并确认服务器组的 postgres 管理员用户的密码
   - 为数据选择适当的存储类
   - 为日志选择适当的存储类
   - 为备份选择适当的存储类
   - 选择要预配的工作器节点数
1. 单击“部署”按钮

随即开始在数据控制器上创建已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。

几分钟后，创建过程应会成功完成。

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

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-postgresql-hyperscale-server-group.md)
- [存储配置和 Kubernetes 存储概念](storage-configuration.md)
- [Kubernetes 资源模型](https://github.com/kubernetes/community/blob/master/contributors/design-proposals/scheduling/resources.md#resource-quantities)

