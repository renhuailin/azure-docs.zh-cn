---
title: 为 Azure Cosmos DB 分析存储配置专用终结点。
description: 了解如何为 Azure Cosmos DB 分析存储设置托管的专用终结点以限制网络访问。
author: AnithaAdusumilli
ms.service: cosmos-db
ms.topic: how-to
ms.date: 03/02/2021
ms.author: anithaa
ms.openlocfilehash: 457182e2f8bf24e911fa2b5e0514b0dba48d6ae0
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123111964"
---
# <a name="configure-azure-private-link-for-azure-cosmos-db-analytical-store"></a>为 Azure Cosmos DB 分析存储配置 Azure 专用链接
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

在文本中，你将了解如何为 Azure Cosmos DB 分析存储设置托管的专用终结点。 如果要使用事务存储，请参阅[事务存储的专用终结点](how-to-configure-private-endpoints.md)一文。 使用[托管的专用终结点](../synapse-analytics/security/synapse-workspace-managed-private-endpoints.md)，你可以限制 Azure Cosmos DB 分析存储对与 Azure Synapse 工作区关联的托管虚拟网络的网络访问权限。 托管的专用终结点建立与分析存储的专用链接。

> [!NOTE]
> 如果你正在对 Cosmos DB 使用专用 DNS 区域，并且希望为分析存储子资源创建一个 Synapse 托管的专用终结点，那么你必须先为链接到 Cosmos DB 的虚拟网络的分析存储 (`privatelink.analytics.cosmos.azure.com`) 创建一个 DNS 区域。

## <a name="enable-a-private-endpoint-for-the-analytical-store"></a>启用分析存储的专用终结点

### <a name="set-up-azure-synapse-analytics-workspace-with-a-managed-virtual-network"></a>设置包含托管虚拟网络的 Azure Synapse Analytics 工作区

[在 Azure Synapse Analytics 中创建工作区并启用数据外泄保护。](../synapse-analytics/security/how-to-create-a-workspace-with-data-exfiltration-protection.md) 通过[数据外泄保护](../synapse-analytics/security/workspace-data-exfiltration-protection.md)，可以确保恶意用户无法将数据从 Azure 资源复制或传输到组织范围以外的位置。

为 Azure Synapse Analytics 工作区启用数据外泄保护时，以下访问限制适用：

* 如果要使用 Azure Spark for Azure Synapse Analytics，则只允许访问 Azure Cosmos DB 分析存储已批准的托管专用终结点。

* 如果要使用 Synapse 无服务器 SQL 池，则可以使用 Azure Synapse 链接查询任何 Azure Cosmos DB 帐户。 但是，只允许对工作区虚拟网络中已批准的托管专用终结点发出 [create external tables as select (CETAS)](../synapse-analytics/sql/develop-tables-cetas.md) 写入请求。

> [!NOTE]
> 创建工作区后，无法更改托管虚拟网络和数据外泄配置。

### <a name="add-a-managed-private-endpoint-for-azure-cosmos-db-analytical-store"></a>为 Azure Cosmos DB 分析存储添加托管的专用终结点

1. 登录到 [Azure 门户](https://portal.azure.com/)。

1. 在 Azure 门户中，导航到 Synapse Analytics 工作区，并打开“概述”窗格。

1. 导航到“入门”窗格并在“打开 Synapse Studio”下选择“打开” ，启动 Synapse studio。

1. 在 Synapse Studio 中，打开“管理”选项卡。

1. 导航到“托管的专用终结点”并选择“新建”

   :::image type="content" source="./media/analytical-store-private-endpoints/create-new-private-endpoint.png" alt-text="为分析存储创建新的专用终结点。" border="true":::

1. 选择“Azure Cosmos DB(SQL API)”帐户类型 >“继续” 。

   :::image type="content" source="./media/analytical-store-private-endpoints/select-private-endpoint.png" alt-text="选择 Azure Cosmos DB SQL API 创建专用终结点。" border="true":::

1. 使用以下详细信息填写“新建托管的专用终结点”窗体：

   * **名称** - 托管专用终结点的名称。 此名称在创建后无法更新。
   * **说明** - 提供用于标识专用终结点的易记说明。
   * **Azure 订阅** - 可以从 Azure 订阅的可用帐户列表中选择 Azure Cosmos DB 帐户。
   * **Azure Cosmos DB 帐户名称** - 选择类型为 SQL 或 MongoDB 的现有 Azure Cosmos DB 帐户。
   * **目标子资源** - 选择这些选项之一：分析：若要为 Azure Cosmos DB 分析存储添加专用终结点，则选择此选项。
     Sql（或 MongoDB）：若要添加 OLTP 或事务帐户终结点，则选择此选项。

   > [!NOTE]
   > 可以将事务存储和分析存储专用终结点同时添加到 Azure Synapse Analytics 工作区中的同一 Azure Cosmos DB 帐户。 如果只想运行分析查询，则可能只需要映射“分析”专用终结点。

   :::image type="content" source="./media/analytical-store-private-endpoints/choose-analytical-private-endpoint.png" alt-text="为目标子资源选择“分析”。" border="true":::

1. 创建后，请转到专用终结点名称，并选择“在 Azure 门户中管理审批”。

1. 导航到 Azure Cosmos DB 帐户，选择专用终结点，然后选择“批准”。

1. 导航回 Synapse Analytics 工作区，然后在“托管的专用终结点”窗格中单击“刷新”。 验证专用终结点是否处于“已批准”状态。

   :::image type="content" source="./media/analytical-store-private-endpoints/approved-private-endpoint.png" alt-text="验证专用终结点是否已批准。" border="true":::

## <a name="use-apache-spark-for-azure-synapse-analytics"></a>使用 Apache Spark for Azure Synapse Analytics

如果创建的 Azure Synapse 工作区启用了数据外泄保护，则默认情况下，将阻止从 Synapse Spark 到 Azure Cosmos DB 帐户的出站访问。 此外，如果 Azure Cosmos DB 存在现有的专用终结点，则也会阻止 Synapse Spark 对其进行访问。

若要允许访问 Azure Cosmos DB 数据：

* 如果要使用 Azure Synapse 链接查询 Azure Cosmos DB 数据，请为 Azure Cosmos DB 帐户添加托管的“分析”专用终结点。

* 如果要批写入/读取和/或流式写入/读取事务存储，请为 Azure Cosmos DB 帐户添加托管“SQL”或“MongoDB”专用终结点。 此外，还应将“connectionMode”设置为“网关”，如以下代码片段所示：

  ```python
  # Write a Spark DataFrame into an Azure Cosmos DB container
  # To select a preferred lis of regions in a multi-region account, add .option("spark.cosmos.preferredRegions", "<Region1>, <Region2>")
  
  YOURDATAFRAME.write\
    .format("cosmos.oltp")\
    .option("spark.synapse.linkedService", "<your-Cosmos-DB-linked-service-name>")\
    .option("spark.cosmos.container","<your-Cosmos-DB-container-name>")\
    .option("spark.cosmos.write.upsertEnabled", "true")\
    .option("spark.cosmos.connection.mode", "Gateway")\
    .mode('append')\
    .save()
  
  ```

## <a name="using-synapse-serverless-sql-pools"></a>使用 Synapse 无服务器 SQL 池

Synapse 无服务器 SQL 池使用未部署到托管虚拟网络中的多租户功能。 如果 Azure Cosmos DB 帐户存在现有的专用终结点，则将阻止 Synapse 无服务器 SQL 池访问该帐户，因为该帐户上存在网络隔离检查。

若要从 Synapse 工作区为此帐户配置网络隔离，请执行以下操作：

1. 通过指定 Azure Cosmos DB 帐户的 `NetworkAclBypassResourceId` 设置，允许 Synapse 工作区访问该帐户。

   **使用 PowerShell**

   ```powershell-interactive
   Update-AzCosmosDBAccount -Name MyCosmosDBDatabaseAccount -ResourceGroupName MyResourceGroup -NetworkAclBypass AzureServices -NetworkAclBypassResourceId "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   **使用 Azure CLI**

   ```azurecli-interactive
   az cosmosdb update --name MyCosmosDBDatabaseAccount --resource-group MyResourceGroup --network-acl-bypass AzureServices --network-acl-bypass-resource-ids "/subscriptions/subId/resourceGroups/rgName/providers/Microsoft.Synapse/workspaces/wsName"
   ```

   > [!NOTE]
   > Azure Cosmos DB 帐户和 Azure Synapse Analytics 工作区应在同一 Azure Active Directory (AD) 租户下。

2. 现在，你可以通过 Azure Synapse 链接使用 T-SQL 查询从无服务器 SQL 池访问该帐户。 但是，若要确保对分析存储中的数据进行网络隔离，必须为此帐户添加一个“分析”托管专用终结点。 否则，将不会阻止对分析存储数据的公共访问。

> [!IMPORTANT]
> 如果要使用 Azure Synapse 链接且需要对分析存储中的数据进行网络隔离，则必须使用“分析”托管专用终结点将 Azure Cosmos DB 帐户映射到 Synapse 工作区。

## <a name="next-steps"></a>后续步骤

* [使用 Azure Synapse Spark 3 查询分析存储](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark-3.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)入门
* [使用 Azure Synapse Spark 2 查询分析存储](../synapse-analytics/synapse-link/how-to-query-analytical-store-spark.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)入门
* [使用 Azure Synapse 无服务器 SQL 池查询分析存储](../synapse-analytics/sql/query-cosmos-db-analytical-store.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)入门
