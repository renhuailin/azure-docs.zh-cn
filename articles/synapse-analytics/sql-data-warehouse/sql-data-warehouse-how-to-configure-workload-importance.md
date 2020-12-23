---
title: 配置专用 SQL 池的工作负荷重要性
description: 了解如何在 Azure Synapse Analytics 中设置请求级别重要性。
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.subservice: sql-dw
ms.topic: conceptual
ms.date: 05/15/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 067551d198f717dd40995cb8bc3e1345e82f078f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/01/2020
ms.locfileid: "96461918"
---
# <a name="configure-workload-importance-in-dedicated-sql-pool-for-azure-synapse-analytics"></a>配置 Azure Synapse Analytics 专用 SQL 池中的工作负荷重要性

在 Azure Synapse 的专用 SQL 池中设置重要性使你可以影响查询计划。 重要性较高的查询将安排在重要性较低的查询之前运行。 若要为查询分配重要性，需要创建工作负荷分类器。

## <a name="create-a-workload-classifier-with-importance"></a>创建具有重要性的工作负载分类器

在数据仓库方案中，用户通常需要在繁忙的系统上快速运行其查询。  用户可能是需要运行报表的公司高管人员，也可能是运行即席查询的分析师。 要分配重要性，请创建一个工作负载分类器，并向查询分配重要性。  下面的示例使用 [create workload classifier](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) 语法创建了两个分类器。 `Membername` 可以是单个用户或组。  若要查找现有的专用 SQL 池用户，请运行：

```sql
Select name from sys.sysusers
```

若要为重要性较高的用户创建工作负荷分类器，请运行：

```sql
CREATE WORKLOAD CLASSIFIER ExecReportsClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = above_normal);
```

若要为重要性较低运行即席查询的用户创建工作负荷分类器，请运行：  

```sql
CREATE WORKLOAD CLASSIFIER AdhocClassifier
    WITH (WORKLOAD_GROUP = 'xlargerc'
         ,MEMBERNAME     = 'name' 
         ,IMPORTANCE     = below_normal);
```

## <a name="next-steps"></a>后续步骤

- 有关工作负荷管理的详细信息，[请参阅工作负荷分类](sql-data-warehouse-workload-classification.md)
- 有关重要性的详细信息，请参阅[工作负荷重要性](sql-data-warehouse-workload-importance.md)

> [!div class="nextstepaction"]
> [开始管理和监视工作负荷重要性](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md)
