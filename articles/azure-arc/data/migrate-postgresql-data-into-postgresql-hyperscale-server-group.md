---
title: 将数据从 PostgreSQL 数据库迁移到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
titleSuffix: Azure Arc-enabled database services
description: 将数据从 PostgreSQL 数据库迁移到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: TheJY
ms.author: jeanyd
ms.reviewer: mikeray
ms.date: 07/30/2021
ms.topic: how-to
ms.openlocfilehash: 25e19ac7512c26e9e6985d033ec46d76b4c5233a
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121729381"
---
# <a name="migrate-postgresql-database-to-azure-arc-enabled-postgresql-hyperscale-server-group"></a>从 PostgreSQL 数据库迁移到已启用 Azure Arc 的超大规模 PostgreSQL 服务器组

本文档介绍将现有的 PostgreSQL 数据库（不在已启用 Azure Arc 的数据服务中托管的数据库）放入已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中的步骤。

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="considerations"></a>注意事项

已启用 Azure Arc 的超大规模 PostgreSQL 服务器组是 PostgreSQL 的社区版本，并在已启用 CitusData 扩展的情况下运行。 因此，任何适用于 PostgreSQL（Azure Arc 之外）的工具都应该适用于已启用 Azure Arc 的超大规模 PostgreSQL 服务器组。


因此，使用现在用于 Postgres 的一组工具，应该能够执行以下操作：
1. 从托管在 Azure Arc 之外的实例备份 Postgres 数据库
2. 在已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中还原它

你要执行的只剩：
- 重置服务器参数
- 重置安全性上下文：重新创建用户、角色和重置权限...

若要执行此备份/还原操作，可以使用任何能够针对 Postgres 进行备份/还原的工具。 例如：
- Azure Data Studio 及其 Postgres 扩展
- `pgcli`
- `pgAdmin`
- `pg_dump`
- `pg_restore`
- `psql`
- ...

   [!INCLUDE [use-insider-azure-data-studio](includes/use-insider-azure-data-studio.md)]

## <a name="example"></a>示例
让我们使用 `pgAdmin` 工具来说明这些步骤。
请考虑以下设置：
- **Source：**  
    在裸机服务器上本地运行的 Postgres 服务器，名为 JEANYDSRV。 它的版本为 12，并托管一个名为 MyOnPremPostgresDB 的数据库，该数据库具有一个包含 1 行的表 T1 :::image type="content" source="media/postgres-hyperscale/migrate-pg-source.jpg" alt-text="Migrate-source":::

- **目标：**  
    在 Azure Arc 环境中运行的 Postgres 服务器，名为 postgres01。 它的版本为 12。 除了标准 Postgres 数据库，它没有任何其他数据库。  
    :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination.jpg" alt-text="Migrate-destination":::


### <a name="take-a-backup-of-the-source-database-on-premises"></a>在本地备份源数据库

:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup.jpg" alt-text="Migrate-source-backup":::

对其进行配置：
1. 为它指定文件名：MySourceBackup
2. 将格式设置为“自定义”
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup2.jpg" alt-text="Migrate-source-backup-configure":::

备份成功完成：  
:::image type="content" source="media/postgres-hyperscale/Migrate-PG-Source-Backup3.jpg" alt-text="Migrate-source-backup-completed":::

### <a name="create-an-empty-database-on-the-destination-system-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>在已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中的目标系统上创建空数据库

> [!NOTE]
> 若要在 `pgAdmin` 工具中注册 Postgres 实例，需要使用 Kubernetes 群集中实例的公共 IP，并相应地设置端口和安全性上下文。 运行以下命令后，你将在 `psql` 终结点行上找到这些详细信息：

```azurecli
az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
```
该命令返回如下输出：
```console
[
  {
    "Description": "PostgreSQL Instance",
    "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
  },
  {
    "Description": "Log Search Dashboard",
    "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
  },
  {
    "Description": "Metrics Dashboard",
    "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
  }
]
```

让我们将目标数据库命名为 RESTORED_MyOnPremPostgresDB。

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg" alt-text="Migrate-destination-db-create" lightbox="media/postgres-hyperscale/migrate-pg-destination-dbcreate.jpg":::

### <a name="restore-the-database-in-your-arc-setup"></a>在 Arc 设置中还原数据库

:::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore.jpg" alt-text="Migratre-db-restore":::

配置还原：
1. 指向包含要还原的备份的文件：MySourceBackup
2. 将格式设置保留为“Custom or tar”
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore2.jpg" alt-text="Migrate-db-restore-configure":::

3. 请单击“还原”。  

   还原成功。  
   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestore3.jpg" alt-text="Migrate-db-restore-completed":::

### <a name="verify-that-the-database-was-successfully-restored-in-your-azure-arc-enabled-postgresql-hyperscale-server-group"></a>验证该数据库是否在已启用 Azure Arc 的超大规模 PostgreSQL 服务器组中成功还原

使用以下方法之一：

**从 `pgAdmin`：**  

展开在 Azure Arc 设置中托管的 Postgres 实例。 你将看到已还原的数据库中的表，当选择数据时，它会显示与本地实例中拥有的相同的行：

   :::image type="content" source="media/postgres-hyperscale/migrate-pg-destination-dbrestoreverif.jpg" alt-text="Migrate-db-restore-verification":::

**从 Azure Arc 设置中的 `psql`：**  

在 Arc 设置中，可以使用 `psql` 连接到 Postgres 实例、将数据库上下文设置为 `RESTORED_MyOnPremPostgresDB` 以及查询数据：

1. 列出 `psql` 连接字符串中的终结点，以提供帮助：

   ```azurecli
   az postgres arc-server endpoint list -n postgres01 --k8s-namespace <namespace> --use-k8s
   [
     {
       "Description": "PostgreSQL Instance",
       "Endpoint": "postgresql://postgres:<replace with password>@12.345.123.456:1234"
     },
     {
       "Description": "Log Search Dashboard",
       "Endpoint": "https://12.345.123.456:12345/kibana/app/kibana#/discover?_a=(query:(language:kuery,query:'custom_resource_name:\"postgres01\"'))"
     },
     {
       "Description": "Metrics Dashboard",
       "Endpoint": "https://12.345.123.456:12345/grafana/d/postgres-metrics?var-Namespace=arc3&var-Name=postgres01"
     }
   ]
   ```

1. 从 `psql` 连接字符串，使用 `-d` 参数指示数据库名称。 使用下面的命令时，系统将提示你输入密码：

   ```console
   psql -d RESTORED_MyOnPremPostgresDB -U postgres -h 10.0.0.4 -p 32639
   ```

   `psql` 连接。

   ```output
   Password for user postgres:
   psql (10.12 (Ubuntu 10.12-0ubuntu0.18.04.1), server 12.3 (Debian 12.3-1.pgdg100+1))
   WARNING: psql major version 10, server major version 12.
         Some psql features might not work.
   SSL connection (protocol: TLSv1.3, cipher: TLS_AES_256_GCM_SHA384, bits: 256, compression: off)
   Type "help" for help.

   RESTORED_MyOnPremPostgresDB=#   
   ```

1. 选择该表，你将看到你从本地 Postgres 实例还原的数据：

   ```console
   RESTORED_MyOnPremPostgresDB=# select * from t1;
   ```

   ```output
    col1 |    col2
   ------+-------------
       1 | BobbyIsADog
   (1 row)
   ```

> [!NOTE]
> - 在已启用 Azure Arc 的超大规模 PostgreSQL 上运行时，在进行横向扩展并在超大规模 PostgreSQL 服务器组的工作器节点上分片/分布数据之前，你将看不到如此多的性能优势。 请参阅[后续步骤](#next-steps)。
>
> - 目前，无法将现有的会在本地或任何其他云中运行的 Postgres 实例“加入到 Azure Arc”。 换句话说，无法在现有 Postgres 实例上安装某种“Azure Arc 代理”来使其成为 Azure Arc 启用的 Postgres 设置。转而，你需要创建新的 Postgres 实例，并将数据传输到该实例。 你可以使用以上所示的方法来实现此目的，也可以使用所选的任何 ETL 工具。

## <a name="next-steps"></a>后续步骤

- 阅读 Azure Database for PostgreSQL 超大规模的概念和操作方法指南，以将数据分布到多个 PostgreSQL 超大规模节点并利用 Azure Database for PostgreSQL 超大规模的所有功能：
    * [节点和表](../../postgresql/concepts-hyperscale-nodes.md)
    * [确定应用程序类型](../../postgresql/concepts-hyperscale-app-type.md)
    * [选择分布列](../../postgresql/concepts-hyperscale-choose-distribution-column.md)
    * [表共置](../../postgresql/concepts-hyperscale-colocation.md)
    * [分发和修改表](../../postgresql/howto-hyperscale-modify-distributed-tables.md)
    * [设计多租户数据库](../../postgresql/tutorial-design-database-hyperscale-multi-tenant.md)*
    * [设计实时分析仪表板](../../postgresql/tutorial-design-database-hyperscale-realtime.md)*

> *在这些文档中，跳过“登录到 Azure 门户”和“创建 Azure Database for Postgres - 超大规模 (Citus)”部分 。 在 Azure Arc 部署中执行剩余步骤。 这些部分特定于在 Azure 云中作为 PaaS 服务提供的 Azure Database for PostgreSQL 超大规模 (Citus)，但文档的其他部分直接适用于已启用 Azure Arc 的超大规模 PostgreSQL。

- [横向扩展 Azure Database for PostgreSQL 超大规模服务器组](scale-out-in-postgresql-hyperscale-server-group.md)
