---
title: 管理只读副本 - Azure CLI、REST API - Azure Database for MySQL
description: 了解如何使用 Azure CLI 或 REST API 在 Azure Database for MySQL 中设置和管理只读副本。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2020
ms.custom: devx-track-azurecli
ms.openlocfilehash: c7f33156394b3dfde100014ace6d8b7f1cbc8caf
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121778226"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-cli-and-rest-api"></a>如何使用 Azure CLI 和 REST API 在 Azure Database for MySQL 中创建和管理只读副本

[!INCLUDE[applies-to-mysql-single-server](includes/applies-to-mysql-single-server.md)]

本文介绍如何使用 Azure CLI 和 REST API 在 Azure Database for MySQL 服务中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

## <a name="azure-cli"></a>Azure CLI
可以使用 Azure CLI 创建和管理只读副本。

### <a name="prerequisites"></a>先决条件

- [安装 Azure CLI 2.0](/cli/azure/install-azure-cli)
- 将用作源服务器的 [Azure Database for MySQL 服务器](quickstart-create-mysql-server-database-using-azure-portal.md)。 

> [!IMPORTANT]
> 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 服务器。 请确保源服务器位于其中一个定价层中。

### <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
> 如果为没有现有副本的源服务器创建副本，源服务器将首先重启，以便为复制做好准备。 请考虑这一点并在非高峰期执行这些操作。
>
>如果在主服务器上启用了 GTID (`gtid_mode` = ON)，则新创建的副本也会启用 GTID 并使用基于 GTID 的复制。 若要了解详细信息，请参阅[全局事务标识符 (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

可以使用以下命令创建只读副本服务器：

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

`az mysql server replica create` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| name | mydemoreplicaserver | 所创建的新副本服务器的名称。 |
| source-server | mydemoserver | 要从中进行复制的现有源服务器的名称或 ID。 |

若要创建跨区域只读副本，请使用 `--location` 参数。 下面的 CLI 示例在美国西部创建副本。

```azurecli-interactive
az mysql server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

> [!NOTE]
> * `az mysql server replica create` 命令具有 `--sku-name` 参数，通过该参数可以在使用 Azure CLI 创建副本时指定 sku (`{pricing_tier}_{compute generation}_{vCores}`)。 </br>
> * 主服务器和只读副本应位于相同的定价层上（常规用途或内存优化）。 </br>
> * 副本服务器配置在创建后也可以更改。 建议副本服务器的配置应始终采用与源服务器相同或更大的值，以确保副本能够与主服务器保持一致。


### <a name="list-replicas-for-a-source-server"></a>列出源服务器的副本

若要查看给定源服务器的所有副本，请运行以下命令： 

```azurecli-interactive
az mysql server replica list --server-name mydemoserver --resource-group myresourcegroup
```

`az mysql server replica list` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  要在其中创建副本服务器的资源组。  |
| server-name | mydemoserver | 源服务器的名称或 ID。 |

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器

> [!IMPORTANT]
> 停止复制到服务器操作不可逆。 一旦源服务器和副本服务器之间的复制停止，将无法撤消它。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

可以使用以下命令停止复制到只读副本服务器：

```azurecli-interactive
az mysql server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

`az mysql server replica stop` 命令需要以下参数：

| 设置 | 示例值 | 说明  |
| --- | --- | --- |
| resource-group |  myresourcegroup |  副本服务器所在的资源组。  |
| name | mydemoreplicaserver | 要停止在其上进行复制的副本服务器的名称。 |

### <a name="delete-a-replica-server"></a>删除副本服务器

可以通过运行 **[az mysql server delete](/cli/azure/mysql/server)** 命令删除只读副本服务器。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
> 删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要删除源服务器，可以运行 **[az mysql server delete](/cli/azure/mysql/server)** 命令。

```azurecli-interactive
az mysql server delete --resource-group myresourcegroup --name mydemoserver
```


## <a name="rest-api"></a>REST API
可以使用 [Azure REST API](/rest/api/azure/) 创建和管理只读副本。

### <a name="create-a-read-replica"></a>创建只读副本
可以使用[创建 API](/rest/api/mysql/flexibleserver(preview)/servers/create) 创建只读副本：

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> 若要详细了解可以在哪些区域中创建副本，请访问[只读副本概念文章](concepts-read-replicas.md)。 

如果尚未在“常规用途”或“内存优化”源服务器上将 `azure.replication_support` 参数设置为“REPLICA”并重启服务器，将会收到错误。 请在创建副本之前完成这两个步骤。

使用与主服务器相同的计算和存储设置创建副本。 创建副本后，可以独立于源服务器更改多项设置：计算代系、vCore 数、存储和备份保留期。 定价层也可以独立更改，但“基本”层除外。


> [!IMPORTANT]
> 将源服务器设置更新为新值之前，请将副本设置更新为一个相等或更大的值。 此操作可帮助副本与主服务器发生的任何更改保持同步。

### <a name="list-replicas"></a>列出副本
可以使用[副本列表 API](/rest/api/mysql/flexibleserver(preview)/replicas/listbyserver) 查看源服务器的副本列表：

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器
可以使用[更新 API](/rest/api/mysql/flexibleserver(preview)/servers/update) 停止源服务器与只读副本之间的复制。

停止复制到源服务器和只读副本后，无法撤消该操作。 只读副本将成为支持读取和写入的独立服务器。 独立服务器不能再次成为副本。

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-source-or-replica-server"></a>删除源服务器或副本服务器
若要删除源服务器或副本服务器，请使用[删除 API](/rest/api/mysql/flexibleserver(preview)/servers/delete)：

删除源服务器后，将停止复制到所有只读副本的操作。 只读副本将成为支持读取和写入的独立服务器。

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMySQL/servers/{serverName}?api-version=2017-12-01
```

### <a name="known-issue"></a>已知问题

常规用途和内存优化层中的服务器使用两代存储，常规用途存储 v1（支持最高 4 TB）和常规用途存储 v2（支持最高 16 TB 存储）。
源服务器和副本服务器应该具有相同的存储类型。 由于[常规用途存储 v2](./concepts-pricing-tiers.md#general-purpose-storage-v2-supports-up-to-16-tb-storage) 在所有区域中均不可用，因此请确保在将位置与 CLI 或 REST API 配合使用来创建只读副本时选择正确的副本区域。 有关如何确定源服务器的存储类型，请参阅链接[如何确定服务器上运行的存储类型](./concepts-pricing-tiers.md#how-can-i-determine-which-storage-type-my-server-is-running-on)。 

如果选择无法为源服务器创建只读副本的区域，则会遇到这样的问题：部署将持续运行（如下图所示），然后超时并收到错误“资源预配操作未在允许的超时时间内完成。”

[:::image type="content" source="media/howto-read-replicas-cli/replcia-cli-known-issue.png" alt-text="只读副本 cli 错误":::](media/howto-read-replicas-cli/replcia-cli-known-issue.png#lightbox)

## <a name="next-steps"></a>后续步骤

- 详细了解[只读副本](concepts-read-replicas.md)
