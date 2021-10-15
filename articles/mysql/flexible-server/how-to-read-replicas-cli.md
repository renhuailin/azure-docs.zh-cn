---
title: 使用 Azure CLI 在 Azure Database for MySQL 灵活服务器中管理只读副本。
description: 了解如何使用 Azure CLI 在 Azure Database for MySQL 灵活服务器中设置和管理只读副本。
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 06/17/2021
ms.custom: devx-track-azurecli
ms.openlocfilehash: 77e9d66af8a03f9d1a55a59d9a355499a3019f29
ms.sourcegitcommit: 1f29603291b885dc2812ef45aed026fbf9dedba0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129233894"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-flexible-server-using-the-azure-cli"></a>如何使用 Azure CLI 在 Azure Database for MySQL 灵活服务器中创建和管理只读副本

[[!INCLUDE[applies-to-mysql-flexible-server](../includes/applies-to-mysql-flexible-server.md)]

> [!IMPORTANT]
> Azure Database for MySQL 灵活服务器中的只读副本现处于预览状态。

本文介绍如何使用 Azure CLI 在 Azure Database for MySQL 灵活服务中创建和管理只读副本。 若要详细了解只读副本，请参阅[概述](concepts-read-replicas.md)。

> [!Note]
>
> * 启用高可用性的服务器不支持副本。 
>* 只读副本功能仅适用于“常规用途”或“内存优化”定价层中的 Azure Database for MySQL 灵活服务器。 请确保源服务器位于其中一个定价层中。
>
> * 如果在主服务器上启用了 GTID (`gtid_mode` = ON)，则新创建的副本也会启用 GTID 并使用基于 GTID 的复制。 若要了解详细信息，请参阅[全局事务标识符 (GTID)](concepts-read-replicas.md#global-transaction-identifier-gtid)

## <a name="azure-cli"></a>Azure CLI

可以使用 Azure CLI 创建和管理只读副本。

### <a name="prerequisites"></a>先决条件

- [安装 Azure CLI 2.0](/cli/azure/install-azure-cli)
- 将用作源服务器的 [Azure Database for MySQL 灵活服务器](quickstart-create-server-cli.md)。

### <a name="create-a-read-replica"></a>创建只读副本

> [!IMPORTANT]
>如果为没有现有副本的源服务器创建副本，源服务器将首先重启，以便为复制做好准备。 请考虑这一点并在非高峰期执行这些操作。

可以使用以下命令创建只读副本服务器：

```azurecli-interactive
az mysql flexible-server replica create --replica-name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
``` 

> [!NOTE]
> 只读副本是使用与源服务器相同的服务器配置创建的。 副本服务器配置在创建后可以更改。 始终会在与源服务器相同的资源组、位置和订阅中创建副本服务器。 如果要将副本服务器创建到不同的资源组或不同的订阅，可以在创建后[移动副本服务器](../../azure-resource-manager/management/move-resource-group-and-subscription.md)。 建议副本服务器的配置应始终采用与源服务器相同或更大的值，以确保副本能够与源服务器保持一致。


### <a name="list-replicas-for-a-source-server"></a>列出源服务器的副本

若要查看给定源服务器的所有副本，请运行以下命令： 

```azurecli-interactive
az mysql flexible-server replica list --server-name mydemoserver --resource-group myresourcegroup
```

### <a name="stop-replication-to-a-replica-server"></a>停止复制到副本服务器

> [!IMPORTANT]
>停止复制到服务器操作不可逆。 一旦源服务器和副本服务器之间的复制停止，将无法撤消它。 然后，副本服务器将成为独立服务器，并且现在支持读取和写入。 此服务器不能再次成为副本服务器。

可以使用以下命令停止复制到只读副本服务器：

```azurecli-interactive
az mysql flexible-server replica stop-replication --replica-name mydemoreplicaserver --resource-group myresourcegroup
```

### <a name="delete-a-replica-server"></a>删除副本服务器

可以通过运行 **[az mysql server delete](/cli/azure/mysql/server)** 命令删除只读副本服务器。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-source-server"></a>删除源服务器

> [!IMPORTANT]
>删除源服务器会停止复制到所有副本服务器，并删除源服务器本身。 副本服务器成为现在支持读取和写入的独立服务器。

若要删除源服务器，可以运行 [az mysql flexible-server delete](/cli/azure/mysql/flexible-server) 命令。

```azurecli-interactive
az mysql flexible-server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="next-steps"></a>后续步骤

- 详细了解[只读副本](concepts-read-replicas.md)