---
title: 无服务器 SQL 池的成本管理
description: 本文档介绍如何管理无服务器 SQL 池的成本，以及在 Azure 存储中查询数据时如何计算处理数据。
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491558"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中无服务器 SQL 池的成本管理

本文介绍如何在 Azure Synapse Analytics 中评估和管理无服务器 SQL 池的成本：
- 在发出查询之前估计处理的数据量
- 使用成本控制功能设置预算

了解 Azure Synapse Analytics 中无服务器 SQL 池的成本仅是 Azure 帐单中每月费用的一部分。 如果你正在使用其他 Azure 服务，则需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何在 Azure Synapse 分析中为无服务器 SQL 池规划和管理成本。

## <a name="data-processed"></a>已处理的数据

*处理的数据* 是指系统在运行查询时临时存储的数据量。 处理的数据包括以下数量：

- 从存储中读取的数据量。 此数量包括：
  - 读取数据时读取的数据。
  - 读取包含元数据的文件格式 (（如 Parquet) ）时读取的数据。
- 中间结果中的数据量。 查询运行时，将在节点之间传输此数据。 它将数据以未压缩的格式传输到你的终结点。 
- 写入存储的数据量。 如果使用 CETAS 将结果集导出到存储，则写出的数据量将添加到为 CETAS 的 SELECT 部分处理的数据量。

从存储读取文件经过高度优化。 该过程使用：

- 预提取，这可能会给读取的数据量增加一些开销。 如果查询读取整个文件，则没有开销。 如果文件部分读取（如前 N 个查询中的文件），则通过使用预提取读取更多数据。
-  (CSV) 分析器的优化逗号分隔值。 如果使用 PARSER_VERSION = "2.0" 读取 CSV 文件，则从存储中读取的数据量会略微增加。 经过优化的 CSV 分析器以大小相等的块区并行读取文件。 块区不一定包含整行。 为了确保分析所有行，优化的 CSV 分析器还读取了相邻块区的小片段。 此过程增加了少量开销。

## <a name="statistics"></a>统计信息

无服务器 SQL 池查询优化器根据统计信息生成最佳查询执行计划。 您可以手动创建统计信息。 否则，无服务器 SQL 池会自动创建它们。 无论采用哪种方式，都可以运行单独的查询来创建统计信息，该查询将返回提供的采样率的特定列。 此查询已处理关联的数据量。

如果运行的是相同的或任何其他可从创建的统计信息中受益的查询，则会在可能的情况下重复使用统计信息。 没有为创建统计信息处理额外的数据。

如果为 Parquet 列创建了统计信息，则仅从文件中读取相关列。 为 CSV 列创建统计信息时，将读取和分析整个文件。

## <a name="rounding"></a>舍入

处理的数据量向上舍入为每个查询最接近的 MB。 每个查询至少处理了 10 MB 的数据。

## <a name="what-data-processed-doesnt-include"></a>处理的数据不包括

- 服务器级别的元数据 (例如登录名、角色和服务器级别凭据) 。
- 在终结点中创建的数据库。 这些数据库仅包含元数据 (，如用户、角色、架构、视图、内联表值函数 [Tvf]、存储过程、数据库范围的凭据、外部数据源、外部文件格式和外部表) 。
  - 如果使用架构推理，则读取文件片段以推断列名称和数据类型，并将读取的数据量添加到处理的数据量。
- 除了 CREATE STATISTICS 语句外，数据定义语言 (DDL) 语句，因为它基于指定的样本百分比处理存储中的数据。
- 仅元数据查询。

## <a name="reducing-the-amount-of-data-processed"></a>减少处理的数据量

可以优化每个查询的数据量，并通过将数据分区和转换为基于列的压缩格式（如 Parquet）来提高性能。

## <a name="examples"></a>示例

假设有三个表。

- Population_csv 表支持 5 TB 的 CSV 文件。 这些文件在五个大小相同的列中进行组织。
- Population_parquet 表与 population_csv 表具有相同的数据。 它由 1 TB 的 Parquet 文件支持。 此表小于上一个表，因为数据以 Parquet 格式压缩。
- Very_small_csv 表支持 100 KB 的 CSV 文件。

**查询 1** ：从 population_csv 中选择 (人口) 汇总

此查询将读取并分析整个文件，以获取填充列的值。 节点用于处理此表的片段，每个片段的总体总和在节点之间传输。 最终总和会传输到你的终结点。 

此查询处理 5 TB 的数据，并处理用于传输碎片总和的少量开销。

**查询 2** ：从 population_parquet 中选择 (人口) 汇总

查询压缩的和基于列的格式（如 Parquet）时，读取的数据比查询1中的数据少。 你会看到此结果，因为无服务器 SQL 池读取单个压缩列而不是整个文件。 在这种情况下，将读取 0.2 TB。  (五个大小相同的列分别为 0.2 TB。 ) 节点处理此表的片段，每个片段的总体总和在节点之间传输。 最终总和会传输到你的终结点。 

此查询处理 0.2 TB 以及用于传输碎片总和的少量开销。

**查询 3** ： SELECT * FROM population_parquet

此查询读取所有列并以未压缩的格式传输所有数据。 如果压缩格式为5:1，则查询将处理 6 TB，因为它将读取 1 TB 并传输 5 TB 未压缩的数据。

**查询 4** ：从 very_small_csv 中选择计数 ( * ) 

此查询读取整个文件。 此表的存储中文件的总大小为 100 KB。 节点用于处理此表的片段，每个片段的总和在节点之间传输。 最终总和会传输到你的终结点。 

此查询处理的数据略超过 100 KB。 此查询处理的数据量最大舍入为 10 MB，如本文的 [舍入](#rounding) 部分所指定。

## <a name="cost-control"></a>成本控制

利用无服务器 SQL 池中的成本控制功能，您可以为处理的数据量设置预算。 可以将处理的数据量设置为每日、每周和每月处理的数据量（TB）。 同时，可以设置一个或多个预算。 若要配置无服务器 SQL 池的成本控制，可以使用 Synapse Studio 或 T-sql。

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>在 Synapse Studio 中配置无服务器 SQL 池的成本控制
 
若要为 Synapse Studio 中的无服务器 SQL 池配置成本控制，请在左侧菜单中导航到 "管理项"，而不是选择 "分析池" 下的 "SQL 池项"。 当你悬停到无服务器 SQL 池时，你将看到一个用于成本控制的图标-单击此图标。

![成本控制导航](./media/data-processed/cost-control-menu.png)

单击 "成本控制" 图标后，将显示侧栏：

![成本控制配置](./media/data-processed/cost-control-sidebar.png)

若要设置一个或多个预算，请首先单击要设置的预算的 "启用" 单选按钮，而不是在文本框中输入整数值。 值的单位为 TBs。 配置所需预算后，单击侧栏底部的 "应用" 按钮。 就是这样，现在已设置了预算。

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>为 T-sql 中的无服务器 SQL 池配置成本控制

若要为 T-sql 中的无服务器 SQL 池配置成本控制，您需要执行以下一个或多个存储过程。

```sql
sp_set_data_processed_limit
    @type = N'daily',
    @limit_tb = 1

sp_set_data_processed_limit
    @type= N'weekly',
    @limit_tb = 2

sp_set_data_processed_limit
    @type= N'monthly',
    @limit_tb = 3334
```

若要查看当前配置，请执行以下 T-sql 语句：

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

若要查看当前日、周或月中处理的数据量，请执行以下 T-sql 语句：

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>后续步骤

若要了解如何优化查询的性能，请参阅 [无服务器 SQL 池的最佳实践](best-practices-sql-on-demand.md)。
