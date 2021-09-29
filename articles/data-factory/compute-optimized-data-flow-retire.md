---
title: 计算优化停用
description: 即将停用数据流计算优化选项
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: tutorial
ms.date: 06/29/2021
ms.openlocfilehash: 2ef5e68f9629feab42a4417b4e08697038947495
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128661823"
---
# <a name="retirement-of-data-flow-compute-optimized-option"></a>停用数据流计算优化选项

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂和 Azure Synapse Analytics 数据流提供了一种代码较少的机制，用于使用图形设计范例大规模转换 ETL 作业中的数据。 数据流在 Azure 数据工厂和 Azure Synapse Analytics 无服务器 Integration Runtime 设备上执行。 Azure 数据工厂和 Azure Synapse Analytics Integration Runtime 的可缩放性质为用于大规模执行数据流的 Azure Databricks Spark 环境实现了三个不同的计算选项：内存优化、常规用途和计算优化。 内存优化和常规用途是推荐数据流计算类别，与 Integration Runtime 一起用于实现生产工作负载。 由于计算优化通常不足以满足具备数据流的常见用例，因此建议在生产工作负载中使用常规用途或内存优化数据流。

## <a name="migration-steps"></a>迁移步骤

从现在到 2024 年 8 月 31 日，计算优化数据流会继续在现有管道中工作。 若要避免服务中断，请在 2024 年 8 月 31 日之前删除现有计算优化数据流，并按照以下步骤创建新 Azure Integration Runtime 和数据流活动。 创建新数据流活动时：

1. 创建将“常规用途”或“内存优化”作为计算类型的新 Azure Integration Runtime 实例。
2. 使用其中任一计算类型设置数据流活动。

   :::image type="content" source="media/data-flow/compute-types.png" alt-text="计算类型":::

## <a name="comparison-between-different-compute-options"></a>不同计算选项之间的比较 

| 计算选项              | 性能                                                  |
| :-------------------- | :----------------------------------------------------------- |
| 常规用途数据流（基本） | 适用于生产工作负载中的常规用例 |
| 内存优化数据流（标准） | 处理大型数据集和许多计算时，最好对数据流执行运行时 |
| 计算优化数据流（已弃用） | 不建议用于生产工作负载 |

* [访问 Azure 数据工厂 定价页，了解可用于常规用途和内存优化数据流的最新更新定价](https://azure.microsoft.com/pricing/details/data-factory/data-pipeline/)
* [在此处可找到数据流常见问题解答中的更多详细信息](/azure/data-factory/frequently-asked-questions#mapping-data-flows)  
* [在 Microsoft Q&A 上发布有关数据流的问题并查找答案](https://aka.ms/datafactoryqa)