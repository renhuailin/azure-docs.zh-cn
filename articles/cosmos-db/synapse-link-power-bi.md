---
title: 用于分析具有 Synapse Link 的 Azure Cosmos DB 的 Power BI 和无服务器 SQL 池
description: 了解如何在 Azure Cosmos DB 的 Synapse Link 上构建无服务器 SQL 池数据库和视图，查询 Azure Cosmos DB 容器，然后在这些视图中使用 Power BI 来生成模型。
author: Rodrigossz
ms.service: cosmos-db
ms.topic: how-to
ms.date: 11/30/2020
ms.author: rosouz
ms.custom: synapse-cosmos-db
ms.openlocfilehash: ae03907ce75d6df999949ebf418fb8135520008d
ms.sourcegitcommit: c072eefdba1fc1f582005cdd549218863d1e149e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111958912"
---
# <a name="use-power-bi-and-serverless-synapse-sql-pool-to-analyze-azure-cosmos-db-data-with-synapse-link"></a>使用 Power BI 和无服务器 Synapse SQL 池来分析具有 Synapse Link 的 Azure Cosmos DB 
[!INCLUDE[appliesto-sql-mongodb-api](includes/appliesto-sql-mongodb-api.md)]

本文介绍如何在 Azure Cosmos DB 的 Synapse Link 上生成无服务器 SQL 池数据库和视图。 你将查询 Azure Cosmos DB 容器，然后在那些视图中使用 Power BI 生成模型，以反映该查询。

借助 Azure Synapse Link，可以在 Power BI 中生成近乎实时的仪表板，以便分析 Azure Cosmos DB 数据。 对事务工作负载没有任何性能或成本影响，也不存在管理 ETL 管道的复杂性。 可以使用 [DirectQuery](/power-bi/connect-data/service-dataset-modes-understand#directquery-mode) 或 [import](/power-bi/connect-data/service-dataset-modes-understand#import-mode) 模式。 

在此方案中，你将使用合作伙伴零售商店中有关 Surface 产品销售的虚拟数据。 你将根据靠近大型家庭的程度和特定某一周中广告的影响来分析每家店的收入。 在本文中，你会创建名为“RetailSales”和“StoreDemographics”的两个视图，并创建它们之间的查询 。 可以从此 [GitHub](https://github.com/Azure-Samples/Synapse/tree/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/RetailData) 存储库获取示例产品数据。

## <a name="prerequisites"></a>先决条件

在开始之前，请确保创建以下资源：

* [创建 SQL (core) 或 MongoDB 类型的 Azure Cosmos DB 帐户。](create-cosmosdb-resources-portal.md)

* 为 [Azure Cosmos 帐户](configure-synapse-link.md#enable-synapse-link)启用 Azure Synapse Link

* 在 Azure Cosmos 帐户中创建数据库，并且创建两个[启用了分析存储](configure-synapse-link.md#create-analytical-ttl)的容器。

* 按照[批处理数据引入](https://github.com/Azure-Samples/Synapse/blob/main/Notebooks/PySpark/Synapse%20Link%20for%20Cosmos%20DB%20samples/Retail/spark-notebooks/pyspark/1CosmoDBSynapseSparkBatchIngestion.ipynb)笔记本中的说明，将产品数据加载到 Azure Cosmos 容器中。

* 创建名为“SynapseLinkBI”的 [Synapse 工作区](../synapse-analytics/quickstart-create-workspace.md)。

* [将 Azure Cosmos 数据库连接到 Synapse 工作区](../synapse-analytics/synapse-link/how-to-connect-synapse-link-cosmos-db.md?toc=/azure/cosmos-db/toc.json&bc=/azure/cosmos-db/breadcrumb/toc.json)。

## <a name="create-a-database-and-views"></a>创建数据库和视图

从 Synapse 工作区中，转到“开发”选项卡，选择“+”图标，然后选择“SQL 脚本”  。

:::image type="content" source="./media/synapse-link-power-bi/add-sql-script.png" alt-text="将 SQL 脚本添加到 Synapse Analytics 工作区":::

每个工作区都带有无服务器 SQL 终结点。 在创建 SQL 脚本后，从顶部的工具栏连接到“内置”。

:::image type="content" source="./media/synapse-link-power-bi/enable-sql-on-demand-endpoint.png" alt-text="启用该 SQL 脚本，以在工作区中使用无服务器 SQL 终结点":::

建议不要在 master 数据库或 default 数据库中创建视图，此操作也不受支持 。 在启用了 Synapse Link 的容器上创建名为“RetailCosmosDB”的新数据库和 SQL 视图。 以下命令演示如何创建数据库：

```sql
-- Create database
Create database RetailCosmosDB
```

接下来，在启用了 Synapse Link 的不同的 Azure Cosmos 容器中创建多个视图。 通过视图，可以使用 T-SQL 来联接和查询位于不同容器中的 Azure Cosmos DB 数据。  在创建视图时，请确保选择“RetailCosmosDB”数据库。

以下脚本演示如何在每个容器上创建视图。 为了简单起见，让我们在启用了 Synapse Link 的容器上使用无服务器 SQL 池的[自动架构推理](analytical-store-introduction.md#analytical-schema)功能：


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

请确保在前一个 SQL 脚本中插入 Azure Cosmos DB 区域和主键。 区域名称中的所有字符都应该使用小写，不带空格。 与 `OPENROWSET` 命令的其他参数不同，在指定容器名称参数时，应该不带引号。

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

现在，请通过选择“运行”命令来运行该 SQL 脚本。

## <a name="query-the-views"></a>查询视图

现在，已经创建了两个视图，接下来让我们来定义查询，以便联接那两个视图，如下所示：

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

选择“运行”，此操作会提供下表作为结果：

:::image type="content" source="./media/synapse-link-power-bi/join-views-query-results.png" alt-text="在联接 StoreDemographics 和 RetailSales 视图后的查询结果":::

## <a name="model-views-over-containers-with-power-bi"></a>使用 Power BI 的容器上的模型视图

接下来，使通过用以下步骤打开 Power BI Desktop 并连接到无服务器 SQL 终结点：

1. 打开 Power BI Desktop 应用程序。 选择“获取数据”并选择”更多” 。

1. 从连接选项列表中选择“Azure Synapse Analytics (SQL DW)”。

1. 输入数据库所在的 SQL 终结点的名称。 在“服务器”字段中输入“`SynapseLinkBI-ondemand.sql.azuresynapse.net`”。 在此示例中，“SynapseLinkBI”是工作区的名称。 如果为工作区指定了其他不同名称，请替换此名称。 为数据连接模式选择“直接查询”，然后选择“确定” 。

1. 选择首选身份验证方法，如 Azure AD。

1. 选择“RetailCosmosDB”数据库，以及“RetailSales”、“StoreDemographics”视图  。

1. 选择“加载”，以将这两个视图加载到直接查询模式。

1. 选择“模型”，以通过“storeId”列在两个视图之间建立关系 。

1. 从“RetailSales”视图将“StoreId”列拖到“StoreDemographics”视图中的“StoreId”列   。

1. 选择多对一 (*: 1) 关系，因为 RetailSales 视图中存在多个具有相同存储 ID 的行。 StoreDemographics 只有一个存储 ID 行（它是维度表）。

现在，请导航到“报表”窗口并创建报表，以根据分散的收入表示形式和 LargeHH 索引，将家庭规模的相对重要性与每个商店的平均收入进行比较：

1. 选择“散点图”。

1. 从“StoreDemographics”视图将“LargeHH”拖放到 X 轴上 。

1. 从“RetailSales”视图将“Revenue”拖放到 Y 轴上 。 选择“Average”，以获取每周每个商店每种产品的平均销售额。

1. 从“RetailSales”视图将“productCode”拖放到图例中，以选择某个特定产品线 。
在选择这些选项后，应该会看到类似于以下屏幕截图的关系图：

:::image type="content" source="./media/synapse-link-power-bi/household-size-average-revenue-report.png" alt-text="将家庭规模的相对重要性与每个商店的平均收入进行比较的报表":::

## <a name="next-steps"></a>后续步骤

[使用 T-SQL 来查询使用 Azure Synapse Link 的 Azure Cosmos DB 数据](../synapse-analytics/sql/query-cosmos-db-analytical-store.md)

在 Azure Synapse Studio 中使用无服务器 SQL 池[分析 Azure 开放数据集并将结果可视化](../synapse-analytics/sql/tutorial-data-analyst.md)