---
title: 适用于 Azure Cosmos DB 图形数据的第三方数据建模工具
description: 本文介绍用于设计图形数据模型的各种工具。
author: mansha
ms.author: mansha
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/25/2021
ms.reviewer: sngun
ms.openlocfilehash: 9d603de7ae8f011ac2ae2ed96d61efb571bdcf64
ms.sourcegitcommit: c2f0d789f971e11205df9b4b4647816da6856f5b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/23/2021
ms.locfileid: "122662353"
---
# <a name="third-party-data-modeling-tools-for-azure-cosmos-db-graph-data"></a>适用于 Azure Cosmos DB 图形数据的第三方数据建模工具

[!INCLUDE[appliesto-gremlin-api](../includes/appliesto-gremlin-api.md)]

设计数据模型很重要，而维护数据模型则更重要。 下面介绍一组第三方可视化设计工具，这些工具有助于设计和维护图形数据模型。

> [!IMPORTANT] 
> 本文中提到的解决方案仅供参考，所有权属于各个解决方案的所有者。 建议用户进行全面评估，然后选择最适合的解决方案。

## <a name="hackolade"></a>Hackolade

Hackolade 是用于 NoSQL 数据库的数据建模和架构设计工具。 它提供一个数据建模工作室，可帮助管理静态数据和动态数据的架构。

### <a name="how-it-works"></a>工作原理
此工具提供顶点/边缘及其相应属性的数据建模。  它支持几个用例，其中包括：
-   从空白页面开始，考虑不同的选项，以图形方式生成 Cosmos DB Gremlin 模型。  然后在 Azure 实例中对模型执行正向工程，以评估结果并继续改进。  无需编写任何代码行即可实现所有这些有用的功能。
-   对 Azure 上的现有图形进行反向工程，清晰地了解其结构，让你也可以有效地查询图形。  然后，使用说明、元数据和约束来扩充数据模型，以生成文档。 它支持 HTML、Markdown 或 PDF 格式，并向公司数据治理或字典系统提供信息。
-   通过数据结构的去规范化，从关系数据库迁移到 NoSQL。
-   通过命令行接口与 CI/CD 管道集成
-   使用 Git 进行协作和版本控制
-   还有更多功能...

### <a name="sample"></a>示例

图 2 中的动画提供了一个反向工程的演示，从 RDBMS 中提取实体，然后 Hackolade 将从外键关系中发现关系，并进行修改。

[此处](https://github.com/Azure-Samples/northwind-ddl-sample/blob/main/nw.sql)提供了源作为 SQL Server 的示例 DDL   


:::image type="content" source="./media/graph-modeling-tools/hackolade-screenshot.jpg" alt-text="图形图示":::
图 1：图形图示（提取了图形数据模型）

修改数据模型后，该工具可以生成 Gremlin 脚本，其中可能包括自定义 Cosmos DB 索引脚本以确保创建最佳索引。有关完整流程，请参阅图 2。

下图演示了实操中 RDBMS 和 Hackolade 的反向工程：:::image type="content" source="./media/graph-modeling-tools/cosmos-db-gremlin-hackolade.gif" alt-text="Hackolade 实操演练":::

图 2：Hackolade 实操演练（演示 SQL 如何转换为 Gremlin 数据模型）
### <a name="useful-links"></a>有用的链接 
-   [下载 14 天免费试用版](https://hackolade.com/download.html)
-   [计划演示](https://c.x.ai/pdesmarets)
-  [获取更多数据模型](https://hackolade.com/samplemodels.html#cosmosdb)。
-  [Hackolade 文档](https://hackolade.com/help/CosmosDBGremlin.html)

## <a name="next-steps"></a>后续步骤
- [可视化数据](/azure/cosmos-db/graph/graph-visualization-partners)
