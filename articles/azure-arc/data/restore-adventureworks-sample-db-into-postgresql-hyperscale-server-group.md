---
title: 将 AdventureWorks 示例数据库导入到已启用 Azure Arc 的超大规模 PostgreSQL
description: 将 AdventureWorks 示例数据库还原到已启用 Azure Arc 的超大规模 PostgreSQL
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 06/02/2021
ms.topic: how-to
ms.openlocfilehash: f802bb5d681e9e77d258d6071def53d4c5e4958f
ms.sourcegitcommit: ee8ce2c752d45968a822acc0866ff8111d0d4c7f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/14/2021
ms.locfileid: "113731083"
---
# <a name="import-the-adventureworks-sample-database-to-azure-arc-enabled-postgresql-hyperscale"></a>将 AdventureWorks 示例数据库导入到已启用 Azure Arc 的超大规模 PostgreSQL

[AdventureWorks](/sql/samples/adventureworks-install-configure) 是一个示例数据库，其中包含教程和示例中使用的 OLTP 数据库。 它由 Microsoft 提供并维护，是 [SQL Server 示例 GitHub 存储库](https://github.com/microsoft/sql-server-samples/tree/master/samples/databases)的一部分。

开源项目已转换 AdventureWorks 数据库，使其与已启用 Azure Arc 的超大规模 PostgreSQL 兼容。
- [原始项目](https://github.com/lorint/AdventureWorks-for-Postgres)
- [继续处理预转换 CSV 文件使其与 PostgreSQL 兼容的项目](https://github.com/NorfolkDataSci/adventure-works-postgres)

本文档介绍将 AdventureWorks 示例数据库导入到超大规模 PostgreSQL 服务器组的简单过程。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="download-the-adventureworks-backup-file"></a>下载 AdventureWorks 备份文件

将 AdventureWorks .sql 文件下载到超大规模 PostgreSQL 服务器组容器。 在此示例中，我们将使用 `kubectl exec` 命令远程执行超大规模 PostgreSQL 服务器组容器中的命令，将文件下载到容器中。 可以使用 `curl` 从可以访问的任何位置下载此文件。 如果有希望在超大规模 PostgreSQL 服务器组容器中拉取的其他数据库备份文件，可以使用相同的方法。 在超大规模 PostgreSQL 服务器组容器中时，可以轻松创建数据库、架构并填充数据。

运行类似于下面的命令来下载文件，并在运行之前替换 pod 名称和命名空间名称的值：

> [!NOTE]
>  容器需要通过 443 的 Internet 连接才能从 GitHub 下载文件。

> [!NOTE]
>  使用超大规模 Postgres 服务器组的协调器节点的 pod 名称。 其名称为 <server group name>c-0（例如 postgres01c-0，其中 c 表示协调器节点）。  如果你不确定 pod 名称，请运行命令 `kubectl get pod`

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres  -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"

#Example:
#kubectl exec postgres02-0 -n arc -c postgres -- /bin/bash -c "cd /tmp && curl -k -O https://raw.githubusercontent.com/microsoft/azure_arc/main/azure_arc_data_jumpstart/aks/arm_template/postgres_hs/AdventureWorks.sql"
```

## <a name="step-2-import-the-adventureworks-database"></a>步骤 2：导入 AdventureWorks 数据库

同样，你可以运行 kubectl exec 命令来使用超大规模 PostgreSQL 服务器组容器中包含的 psql CLI 工具来创建和加载数据库。

运行类似于下面的命令，创建空数据库，首先替换 pod 名称和命名空间名称的值，然后再运行它。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -c 'CREATE DATABASE "adventureworks";'
```

然后运行类似于下面的命令，导入数据库，替换 pod 名称和命名空间名称的值，然后再运行它。

```console
kubectl exec <PostgreSQL pod name> -n <namespace name> -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql

#Example
#kubectl exec postgres02-0 -n arc -c postgres -- psql --username postgres -d adventureworks -f /tmp/AdventureWorks.sql
```


> 注意：在已启用 Azure Arc 的超大规模 PostgreSQL 上运行时，在进行横向扩展并在超大规模 PostgreSQL 服务器组的工作器节点上分片/分布数据之前，你将看不到如此多的性能优势。请参阅[建议的后续步骤](#suggested-next-steps)。

## <a name="suggested-next-steps"></a>建议执行的后续步骤
- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分布到多个 PostgreSQL 超大规模节点并利用 Azure Database for PostgreSQL 超大规模的所有功能。 :
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

   > 在上述文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for PostgreSQL - 超大规模 (Citus)”部分\* 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
