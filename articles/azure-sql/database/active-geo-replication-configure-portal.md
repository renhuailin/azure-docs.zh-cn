---
title: 教程：门户中的异地复制和故障转移
description: 了解如何使用 Azure 门户或 Azure CLI 为 SQL 数据库配置异地复制，以及如何启动故障转移。
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: BustosMSFT
ms.author: robustos
ms.reviewer: mathoma
ms.date: 08/20/2021
ms.openlocfilehash: e94b9cb0628fca112f3bb9181035d6d60bbd7b74
ms.sourcegitcommit: 7854045df93e28949e79765a638ec86f83d28ebc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122866693"
---
# <a name="tutorial-configure-active-geo-replication-and-failover-azure-sql-database"></a>教程：配置活动异地复制和故障转移（Azure SQL 数据库）

[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

本文介绍如何使用 [Azure 门户](https://portal.azure.com)或 Azure CLI [为 Azure SQL 数据库配置活动异地复制](active-geo-replication-overview.md#active-geo-replication-terminology-and-capabilities)，以及如何启动故障转移。

有关使用自动故障转移组的最佳做法，请参阅 [Azure SQL 数据库最佳做法](auto-failover-group-overview.md#best-practices-for-sql-database)和 [Azure SQL 托管实例最佳做法](auto-failover-group-overview.md#best-practices-for-sql-managed-instance)。 



## <a name="prerequisites"></a>先决条件

# <a name="portal"></a>[Portal](#tab/portal)

若要使用 Azure 门户配置活动异地复制，需要以下资源：

* Azure SQL 数据库中的数据库：要复制到其他地理区域的主数据库。

> [!Note]
> 如果使用 Azure 门户，仅可在与主要数据库相同的订阅内创建辅助数据库。 如果辅助数据库需要位于其他订阅中，请使用 [Create Database REST API](/rest/api/sql/databases/createorupdate) 或 [ALTER DATABASE Transact-SQL API](/sql/t-sql/statements/alter-database-transact-sql)。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

若要配置活动异地复制，需要 Azure SQL 数据库中的数据库。 它是要复制到其他地理区域的主数据库。

为 Azure CLI 准备环境。

[!INCLUDE [azure-cli-prepare-your-environment-no-header](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

---

## <a name="add-a-secondary-database"></a>添加辅助数据库

以下步骤在异地复制合作关系中创建新的辅助数据库。  

只有订阅所有者或共有者才能添加辅助数据库。

辅助数据库具有与主数据库相同的名称，并默认使用相同的服务层级和计算大小。 辅助数据库可以是单一数据库，也可以是共用数据库。 有关详细信息，请参阅[基于 DTU 的购买模型](service-tiers-dtu.md)和[基于 vCore 的购买模型](service-tiers-vcore.md)。
创建辅助数据库并设定种子后，会开始将数据从主数据库复制到新的辅助数据库。

> [!NOTE]
> 如果合作伙伴数据库已存在（例如，在终止之前的异地复制关系的情况下），命令会失败。

# <a name="portal"></a>[Portal](#tab/portal)

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到需要设置以便进行异地复制的数据库。
2. 在 SQL 数据库页上，选择数据库，滚动到“数据管理”，选择“副本”，然后选择“创建副本”。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-cli-create-geo-replica.png" alt-text="配置异地复制":::

3. 为辅助数据库选择或创建服务器，并根据需要配置“计算 + 存储”选项。 你可以为辅助服务器选择任何区域，但我们建议使用[配对区域](../../best-practices-availability-paired-regions.md)。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-create-and-configure-replica.png" alt-text="{alt-text}":::

    可以选择性地将辅助数据库添加到弹性池。 若要在池中创建辅助数据库，请选择“想要使用 SQL 弹性池?”旁边的“是” ，并在目标服务器上选择一个池。 池必须已在目标服务器上存在。 此工作流不会创建池。

4. 单击“查看 + 创建”，查看信息，然后单击“创建” 。
5. 此时会创建辅助数据库，部署过程开始。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-geo-replica-deployment.png" alt-text="屏幕截图显示辅助数据库的部署状态。":::

6. 完成部署时，辅助数据库会显示其状态。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-sql-database-secondary-status.png" alt-text="屏幕截图显示部署后的辅助数据库状态。":::

7. 返回主数据库页，然后选择“副本”。 你的辅助数据库列于“异地副本”下。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-sql-db-geo-replica-list.png" alt-text="屏幕截图显示 SQL 数据库的主副本和异地副本。":::

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

选择要为异地复制设置的数据库。 你需要以下信息：
- 原始 Azure SQL 数据库名称。
- Azure SQL Server 名称。
- 资源组名称。
- 要创建新副本的服务器的名称。

> [!NOTE]
> 辅助数据库的服务层级必须与主数据库相同。

你可以为辅助服务器选择任何区域，但我们建议使用[配对区域](../../best-practices-availability-paired-regions.md)。

运行 [az sql db replica create](/cli/azure/sql/db/replica#az_sql_db_replica_create) 命令。

```azurecli
az sql db replica create --resource-group ContosoHotel --server contosoeast --name guestlist --partner-server contosowest --family Gen5 --capacity 2 --secondary-type Geo
```

可以选择性地将辅助数据库添加到弹性池。 若要在池中创建辅助数据库，请使用 `--elastic-pool` 参数。 池必须已在目标服务器上存在。 此工作流不会创建池。

此时会创建辅助数据库，部署过程开始。

部署完成后，可以通过运行 [az sql db replica list-links](/cli/azure/sql/db/replica#az_sql_db_replica_list-links) 命令来检查辅助数据库的状态：
    
```azurecli
az sql db replica list-links --name guestlist --resource-group ContosoHotel --server contosowest
```

---

## <a name="initiate-a-failover"></a>启动故障转移

辅助数据库可以通过切换变为主数据库。

# <a name="portal"></a>[Portal](#tab/portal)  

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到异地复制合作关系中的主数据库。
2. 滚动到“数据管理”，然后选择“副本”。
3. 在“异地副本”列表中，选择要成为新主数据库的数据库，选择省略号，然后选择“强制故障转移”。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-forced-failover.png" alt-text="屏幕截图显示从下拉列表中选择“强制故障转移”。":::
4. 选择“是”开始故障转移。

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

运行 [az sql db replica set-primary](/cli/azure/sql/db/replica#az_sql_db_replica_set-primary) 命令。

```azurecli
az sql db replica set-primary --name guestlist --resource-group ContosoHotel --server contosowest
```

---

该命令会立即将辅助数据库切换为主数据库角色。 此过程通常会在 30 秒或更短的时间内完成。

切换角色时，有一小段时间无法使用这两个数据库（大约 0 到 25 秒）。 如果主数据库具有多个辅助数据库，则该命令自动重新配置其他辅助数据库以连接到新的主数据库。 在正常情况下，完成整个操作所需的时间应该少于一分钟。

> [!NOTE]
> 此命令旨在服务中断时快速恢复数据库。 它将触发故障转移但不进行数据同步，即强制故障转移。  如果发出命令时主数据库处于在线状态且正在提交事务，则可能会丢失某些数据。

## <a name="remove-secondary-database"></a>删除辅助数据库

此操作会永久停止到辅助数据库的复制，并将辅助数据库的角色更改为常规的读写数据库。 如果与辅助数据库的连接断开，命令会成功，但辅助数据库必须等到连接恢复后才会变为可读写。

# <a name="portal"></a>[Portal](#tab/portal)  

1. 在 [Azure 门户](https://portal.azure.com)中，浏览到异地复制合作关系中的主数据库。
2. 选择“副本”。
3. 在“异地副本”列表中，选择要从异地复制伙伴关系中删除的数据库，选择省略号，然后选择“停止复制”。

    :::image type="content" source="./media/active-geo-replication-configure-portal/azure-portal-select-stop-replication.png" alt-text="屏幕截图显示从下拉列表中选择“停止复制”。":::
5. 确认窗口随即打开。 单击“是”从异地复制合作关系中删除数据库。 （将其设置为不属于任何复制的读写数据库。）
 
# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

运行 [az sql db replica delete-link](/cli/azure/sql/db/replica#az_sql_db_replica_delete-link) 命令。

```azurecli
az sql db replica delete-link --name guestlist --resource-group ContosoHotel --server contosoeast --partner-server contosowest
```

确认你想执行该操作。

---

## <a name="next-steps"></a>后续步骤

* 有关活动异地复制的详细信息，请参阅[活动异地复制](active-geo-replication-overview.md)。
* 若要了解自动故障转移组，请参阅[自动故障转移组](auto-failover-group-overview.md)
* 有关业务连续性概述和应用场景，请参阅[业务连续性概述](business-continuity-high-availability-disaster-recover-hadr-overview.md)。