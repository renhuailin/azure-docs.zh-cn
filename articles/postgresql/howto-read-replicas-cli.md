---
title: 管理只读副本 - Azure CLI、REST API - Azure Database for PostgreSQL（单一服务器）
description: 了解如何通过 Azure CLI 和 REST API 管理 Azure Database for PostgreSQL（单一服务器）中的只读副本
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: how-to
ms.date: 12/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: 7e74a58a14bdcc2a6fe1e9f86305aae415c6abf7
ms.sourcegitcommit: d79513b2589a62c52bddd9c7bd0b4d6498805dbe
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/18/2020
ms.locfileid: "97674508"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>通过 Azure CLI、REST API 创建和管理只读副本

本文介绍如何使用 Azure CLI 和 REST API 在 Azure Database for PostgreSQL 中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-replication-support"></a>Azure 复制支持
[只读副本](concepts-read-replicas.md)和[逻辑解码](concepts-logical.md)都依赖 Postgres 预写日志 (WAL) 来获取信息。 这两个功能需要来自 Postgres 的不同级别的日志记录。 逻辑解码需要的日志记录的级别比只读副本需要的更高。

若要配置正确的日志记录级别，请使用 Azure 复制支持参数。 Azure 复制支持有三个设置选项：

* **关闭** - 在 WAL 中包含最少的信息。 大多数 Azure Database for PostgreSQL 服务器上都不提供此设置。  
* **副本** - 比“关闭”详细。 这是运行[只读副本](concepts-read-replicas.md)所需的最低日志记录级别。 此设置是大多数服务器上的默认设置。
* **逻辑** - 比“副本”详细。 这是运行逻辑解码所需的最低日志记录级别。 使用此设置时，只读副本也可以运行。


> [!NOTE]
> 在为持久的大量写入密集型主工作负载部署读取副本时，复制滞后时间可能会持续增长，并可能永远无法与主工作负载进行追赶。 这也可能会在主副本上增加存储使用量，因为在副本收到 WAL 文件之前，不会删除这些文件。

## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 创建和管理只读副本。

### <a name="prerequisites"></a>先决条件

- [安装 Azure CLI 2.0](/cli/azure/install-azure-cli)
- 将用作主服务器的 [Azure Database for PostgreSQL 服务器](quickstart-create-server-up-azure-cli.md)。


### <a name="prepare-the-primary-server"></a>准备主服务器

1. 检查主服务器的 `azure.replication_support` 值。 该值至少应该是“REPLICA”，只读副本才能正常工作。

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. 如果 `azure.replication_support` 并非至少是“REPLICA”，请将其设置为“REPLICA”。 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. 重启服务器以应用更改。

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>创建只读副本

[az postgres server replica create](/cli/azure/postgres/server/replica#az-postgres-server-replica-create) 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group | myresourcegroup |  要在其中创建副本服务器的资源组。  |
| name | mydemoserver-replica | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 要从中进行复制的现有主服务器的名称或资源 ID。 如果希望副本和主服务器的资源组不同，请使用资源 ID。 |

在下面的 CLI 示例中，副本在主服务器所在的区域中创建。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

若要创建跨区域只读副本，请使用 `--location` 参数。 下面的 CLI 示例在美国西部创建副本。

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

如果尚未在“常规用途”或“内存优化”主服务器上将 `azure.replication_support` 参数设置为“REPLICA”并重启服务器，将会收到错误。 请在创建副本之前完成这两个步骤。

> [!IMPORTANT]
> 查看[“只读副本”概述的注意事项部分](concepts-read-replicas.md#considerations)。
>
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等的或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

### <a name="list-replicas"></a>列出副本
可以使用 [az postgres server replica list](/cli/azure/postgres/server/replica#az-postgres-server-replica-list) 命令查看主服务器的副本列表。

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以使用 [az postgres server replica stop](/cli/azure/postgres/server/replica#az-postgres-server-replica-stop) 命令停止主服务器和只读副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-primary-or-replica-server"></a>删除主服务器或副本服务器
若要删除主服务器或副本服务器，请使用 [az postgres server delete](/cli/azure/postgres/server#az-postgres-server-delete) 命令。

删除主服务器后，将停止复制到所有只读副本的操作。 只读副本将成为支持读取和写入的独立服务器。

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
可以使用 [Azure REST API](/rest/api/azure/) 创建和管理只读副本。

### <a name="prepare-the-primary-server"></a>准备主服务器

1. 检查主服务器的 `azure.replication_support` 值。 该值至少应该是“REPLICA”，只读副本才能正常工作。

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. 如果 `azure.replication_support` 并非至少是“REPLICA”，请将其设置为“REPLICA”。

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [重启服务器](/rest/api/postgresql/servers/restart)以应用更改。

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>创建只读副本
可以使用[创建 API](/rest/api/postgresql/servers/create) 创建只读副本：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

如果尚未在“常规用途”或“内存优化”主服务器上将 `azure.replication_support` 参数设置为“REPLICA”并重启服务器，将会收到错误。 请在创建副本之前完成这两个步骤。

使用与主服务器相同的计算和存储设置创建副本。 创建副本后，可以独立于主服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。


> [!IMPORTANT]
> 将主服务器设置更新为新值之前，请将副本设置更新为一个相等的或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

### <a name="list-replicas"></a>列出副本
可以使用[副本列表 API](/rest/api/postgresql/replicas/listbyserver) 查看主服务器的副本列表：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以使用[更新 API](/rest/api/postgresql/servers/update) 停止主服务器与只读副本之间的复制。

停止复制到主服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-primary-or-replica-server"></a>删除主服务器或副本服务器
若要删除主服务器或副本服务器，请使用[删除 API](/rest/api/postgresql/servers/delete)：

删除主服务器后，将停止复制到所有只读副本的操作。 只读副本将成为支持读取和写入的独立服务器。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>后续步骤
* 详细了解 [Azure Database for PostgreSQL 中的只读副本](concepts-read-replicas.md)。
* 了解如何[在 Azure 门户中创建和管理只读副本](howto-read-replicas-portal.md)。