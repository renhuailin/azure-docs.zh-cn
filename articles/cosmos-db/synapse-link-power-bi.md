---
title: Power BI 和无服务器 SQL 池以通过 Synapse 链接分析 Azure Cosmos DB 数据
description: 了解如何在 Azure Cosmos DB 上构建无服务器 SQL 池数据库和视图 Synapse 链接，查询 Azure Cosmos DB 容器，然后使用 Power BI 在这些视图中生成模型。
author: ArnoMicrosoft
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: acomet
ms.openlocfilehash: a16d22a49e42408271c91d961bcdb004536e3980
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101659743"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>使用 Power BI 和无服务器 Synapse SQL 池通过 Synapse 链接分析 Azure Cosmos DB 数据 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

本文介绍如何为 Azure Cosmos DB 生成无服务器 SQL 池数据库和通过 Synapse 的视图链接。 你将查询 Azure Cosmos DB 容器，然后生成一个模型，该模型的 Power BI 通过这些视图来反映该查询。

在此方案中，您将使用合作伙伴零售商店中有关 Surface product sales 的虚拟数据。 你将基于与大型家庭的邻近度和广告对特定周的影响，分析每个商店的收入。 在本文中，将创建名为 **RetailSales** 和 **StoreDemographics** 的两个视图，并在它们之间进行查询。 可以从此 [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) 存储库中获取示例产品数据。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保创建以下资源：

* [创建类型为 SQL (core) 或 MongoDB 的 Azure Cosmos DB 帐户。](create-cosmosdb-resources-portal.md)

* 为[Azure Cosmos 帐户](configure-synapse-link.md#enable-synapse-link)启用 Azure Synapse 链接

* 在 Azure Cosmos 帐户和两个已[启用分析存储](configure-synapse-link.md#create-analytical-ttl)的容器中创建一个数据库。

* 将产品数据加载到 Azure Cosmos 容器中，如此 [批处理数据引入](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb) 笔记本中所述。

* 创建一个名为 **SynapseLinkBI**[的 Synapse 工作区](../synapse-analytics/quickstart-create-workspace.md)。

* [将 Azure Cosmos 数据库连接到 Synapse 工作区](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)。

## <a name="create-a-database-and-views"></a>创建数据库和视图

在 Synapse 工作区中，请执行 " **开发** " 选项卡，选择 **+** 图标，然后选择 " **SQL 脚本**"。

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="将 SQL 脚本添加到 Synapse 分析工作区":::

每个工作区都附带一个无服务器的 SQL 终结点。 创建 SQL 脚本后，从顶部的工具栏中，连接到 **内置**。

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="启用 SQL 脚本以在工作区中使用无服务器 SQL 终结点":::

不建议也不支持在 **master** 数据库或 **默认** 数据库中创建视图。 创建一个名为 **RetailCosmosDB** 的新数据库，并在 Synapse 链接启用的容器上创建一个 SQL 视图。 以下命令显示了如何创建数据库：

```sql
-- Create database
Create database RetailCosmosDB
```

接下来，在启用了不同 Synapse 链接的 Azure Cosmos 容器中创建多个视图。 视图将允许你使用 T-sql 来联接和查询不同容器中的 Azure Cosmos DB 数据。  创建视图时，请确保选择 " **RetailCosmosDB** " 数据库。

以下脚本演示如何在每个容器上创建视图。 为简单起见，让我们使用 "无服务器 Synapse 链接" 容器上的 [自动架构推理](analytical-store-introduction.md#analytical-schema) 功能：


### <a name="retailsales-view"></a>RetailSales 视图：

```sql
-- Create view for RetailSales container
CREATE VIEW  RetailSales
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>',RetailSales)
AS q1
```

请确保在前面的 SQL 脚本中插入 Azure Cosmos DB 区域和主键。 区域名称中的所有字符都应该用小写形式分隔。 不同于命令的其他参数 `OPENROWSET` ，应指定容器名称参数而不指定引号。

### <a name="storedemographics-view"></a>StoreDemographics 视图：

```sql
-- Create view for StoreDemographics container
CREATE VIEW StoreDemographics
AS  
SELECT  *
FROM OPENROWSET (
    'CosmosDB', N'account=<Your Azure Cosmos account name>;database=<Your Azure Cosmos database name>;region=<Your Azure Cosmos DB Region>;key=<Your Azure Cosmos DB key here>', StoreDemographics)
AS q1
```

现在，通过选择 " **运行** " 命令运行 SQL 脚本。

## <a name="query-the-views"></a>查询视图

现在创建了两个视图，接下来，让我们定义查询来联接这两个视图，如下所示：

```sql
SELECT 
sum(p.[revenue]) as revenue
,p.[advertising]
,p.[storeId]
,p.[weekStarting]
,q.[largeHH]
 FROM [dbo].[RetailSales] as p
INNER JOIN [dbo].[StoreDemographics] as q ON q.[storeId] = p.[storeId]
GROUP BY p.[advertising], p.[storeId], p.[weekStarting], q.[largeHH]
```

选择 " **运行** "，为下表提供结果：

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="联接 StoreDemographics 和 RetailSales 视图后的查询结果":::

## <a name="model-views-over-containers-with-power-bi"></a>具有 Power BI 的容器的模型视图

接下来，使用以下步骤打开 Power BI 桌面并连接到无服务器 SQL 终结点：

1. 打开 Power BI Desktop 应用程序。 选择 " **获取数据** " 并选择 " **更多**"。

1. 从连接选项列表中选择 " **Azure Synapse Analytics (SQL DW)** 。

1. 输入数据库所在的 SQL 终结点的名称。 在 `SynapseLinkBI-ondemand.sql.azuresynapse.net` " **服务器** " 字段中输入。 在此示例中，  **SynapseLinkBI** 是工作区的名称。 如果为工作区指定了不同的名称，请替换此名称。 选择 "数据连接模式的 **直接查询** "，然后选择 **"确定"**。

1. 选择首选的身份验证方法，如 Azure AD。

1. 选择 **RetailCosmosDB** 数据库和 **RetailSales** **StoreDemographics** 视图。

1. 选择 " **加载** " 以将两个视图加载到直接查询模式。

1. 选择 " **模型** "，以通过 " **storeId** " 列在两个视图之间建立关系。

1. 从 " **RetailSales** " 视图中将 " **StoreId** " 列拖到 " **StoreDemographics** " 视图中的 **StoreId** 列。

1. 选择 "多对一 ( *： 1) 关系，因为 **RetailSales** 视图中存在多个具有相同存储 ID 的行。 **StoreDemographics** 只有一个存储 ID 行 (它是) 维度表。

现在，导航到 **报表** 窗口，创建一个报表，以根据分散的收入和 LargeHH 指数表示形式，将家庭规模的相对重要性与每个商店的平均收入进行比较：

1. 选择 **散点图**。

1. 将 **LargeHH** 从 **StoreDemographics** 视图拖放到 X 轴。

1. 将 **RetailSales** 视图中的 **收入** 拖放到 Y 轴。 选择 " **平均** " 可获取每个商店和每周的平均销售额。

1. 将 " **productCode** from **RetailSales** " 视图拖放到图例中，以选择特定的产品线。
选择这些选项后，应会看到类似于以下屏幕截图的关系图：

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="将家庭规模相对重要性与每个商店平均收入进行比较的报表":::

## <a name="next-steps"></a>后续步骤

[使用 T-sql 查询使用 Azure Synapse 链接 Azure Cosmos DB 数据](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

使用无服务器 SQL 池 [分析 Azure 开放数据集，并在 Azure Synapse Studio 中可视化结果](../synapse-analytics/sql/tutorial-data-analyst.md)
