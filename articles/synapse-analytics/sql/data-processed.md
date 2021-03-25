---
title: 无服务器 SQL 池的成本管理
description: 本文档介绍如何管理无服务器 SQL 池的成本，以及在 Azure 存储中查询数据时如何计算处理的数据。
services: synapse analytics
author: filippopovic
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: sql
ms.date: 11/05/2020
ms.author: fipopovi
ms.reviewer: jrasnick
ms.openlocfilehash: 8a26f8ced5e91810f8cadff0a27796dc817e6517
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "94491558"
---
# <a name="cost-management-for-serverless-sql-pool-in-azure-synapse-analytics"></a>Azure Synapse Analytics 中无服务器 SQL 池的成本管理

本文介绍如何在 Azure Synapse Analytics 中估算和管理无服务器 SQL 池的成本：
- 在发出查询之前估计处理的数据量
- 使用成本控制功能设置预算

请知悉，Azure Synapse Analytics 中的无服务器 SQL 池的成本只是 Azure 账单中每月成本的一部分。 如果你在使用其他 Azure 服务，则需要为 Azure 订阅中使用的所有 Azure 服务和资源（包括第三方服务）付费。 本文介绍如何在 Azure Synapse Analytics 中规划和管理无服务器 SQL 池的成本。

## <a name="data-processed"></a>已处理的数据

“处理的数据”是指系统在运行查询时临时存储的数据量。 处理的数据包括以下数据量：

- 从存储读取的数据量。 此数据量包括：
  - 读取数据时读取的数据。
  - 读取元数据时读取的数据（适用于包含元数据的文件格式，例如 Parquet）。
- 中间结果中的数据量。 查询运行时在节点之间传输此数据。 它包括以未压缩的格式传输到终结点的数据。 
- 写入到存储的数据量。 如果使用 CETAS 将结果集导出到存储，则写出的数据量将添加到为 CETAS 的 SELECT 部分处理的数据量。

从存储读取文件的流程经过高度优化。 此流程使用：

- 预提取，这可能会给读取的数据量增加一些开销。 如果查询读取整个文件，则没有开销。 如果读取部分文件（例如在 TOP N 查询中就是如此），则会通过预提取读取稍多一些的数据。
- 经过优化的逗号分隔值 (CSV) 分析程序。 如果使用 PARSER_VERSION='2.0' 来读取 CSV 文件，则从存储中读取的数据量会略微增加。 经过优化的 CSV 分析程序按大小相等的区块并行读取文件。 区块不一定包含整行。 为了确保分析所有行，经过优化的 CSV 分析程序还读取相邻区块的小片段。 此流程增加了少量开销。

## <a name="statistics"></a>统计信息

无服务器 SQL 池查询优化器依赖于统计信息来生成最佳查询执行计划。 你可以手动创建统计信息。 否则，无服务器 SQL 池会自动创建它们。 无论采用哪种方式，都可以运行单独的查询来创建统计信息，该查询会按提供的采样率返回特定列。 该查询有一个关联的处理数据量。

如果运行的是相同的查询或任何其他可从创建的统计信息中受益的查询，则会在可能的情况下重复使用此统计信息。 不会因创建统计信息而处理额外的数据。

如果为 Parquet 列创建了统计信息，则仅从文件中读取相关列。 为 CSV 列创建统计信息时，会读取和分析完整的文件。

## <a name="rounding"></a>舍入

每个查询的处理数据量会向上舍入为最接近的 MB。 每个查询的处理数据量至少为 10 MB。

## <a name="what-data-processed-doesnt-include"></a>处理的数据不包括的内容

- 服务器级别的元数据（例如登录名、角色和服务器级别的凭据）。
- 在终结点中创建的数据库。 这些数据库仅包含元数据（例如用户、角色、架构、视图、内联表值函数 [TVF]、存储过程、数据库范围的凭据、外部数据源、外部文件格式和外部表）。
  - 如果使用架构推理，则会读取文件片段以推断列名称和数据类型，并将读取的数据量添加到处理的数据量。
- 除 CREATE STATISTICS 语句外的数据定义语言 (DDL) 语句，因为它基于指定的样本百分比处理存储中的数据。
- 仅限元数据的查询。

## <a name="reducing-the-amount-of-data-processed"></a>减少处理的数据量

可以优化每个查询的处理数据量，并提高性能，方法是：将数据分区并转换为基于列的压缩格式（例如 Parquet）。

## <a name="examples"></a>示例

假设有三个表。

- population_csv 表备有 5 TB 的 CSV 文件。 这些文件以五个大小相同的列进行组织。
- population_parquet 表与 population_csv 表有相同的数据。 它备有 1 TB 的 Parquet 文件。 此表比上一个表小，因为数据以 Parquet 格式压缩。
- very_small_csv 表备有 100 KB 的 CSV 文件。

**查询 1**：SELECT SUM(population) FROM population_csv

此查询会读取并分析完整的文件，以获取 population 列的值。 节点会处理此表的片段，每个片段的人口总和在节点之间传输。 最终的总和会传输到你的终结点。 

此查询会处理 5 TB 的数据，此外还有用于传输片段总和的少量开销。

**查询 2**：SELECT SUM(population) FROM population_parquet

查询压缩的和基于列的格式（例如 Parquet）时，读取的数据比查询 1 中读取的数据少。 你看到此结果是因为无服务器 SQL 池读取单个压缩列而不是整个文件。 在此示例中，将读取 0.2 TB 的数据。 （五个大小相同的列每个都是 0.2 TB。）节点会处理此表的片段，每个片段的人口总和在节点之间传输。 最终的总和会传输到你的终结点。 

此查询会处理 0.2 TB 的数据，此外还有用于传输片段总和的少量开销。

**查询 3**：SELECT * FROM population_parquet

此查询读取所有列并以未压缩的格式传输所有数据。 如果压缩格式为 5:1，则查询会处理 6 TB 的数据，因为它会读取 1 TB 并传输 5 TB 未压缩的数据。

**查询 4**：SELECT COUNT(*) FROM very_small_csv

此查询读取完整的文件。 用于此表的存储中文件的总大小为 100 KB。 节点会处理此表的片段，每个片段的总和在节点之间传输。 最终的总和会传输到你的终结点。 

此查询处理的数据稍多于 100 KB。 为此查询处理的数据量会向上舍入为 10 MB，如本文的[舍入](#rounding)部分所述。

## <a name="cost-control"></a>成本控制

利用无服务器 SQL 池中的成本控制功能，你可以针对处理的数据量设置预算。 你可以按天、按周和按月针对处理的数据量（以 TB 为单位）设置预算。 同时，你可以设有一个或多个预算。 若要配置无服务器 SQL 池的成本控制，可以使用 Synapse Studio 或 T-SQL。

## <a name="configure-cost-control-for-serverless-sql-pool-in-synapse-studio"></a>在 Synapse Studio 中配置无服务器 SQL 池的成本控制
 
若要在 Synapse Studio 中为无服务器 SQL 池配置成本控制，请在左侧菜单中导航到“管理”项，然后选择“分析池”下的“SQL 池”项。 悬停到无服务器 SQL 池上时，你会注意到一个用于成本控制的图标 - 单击该图标。

![成本控制导航](./media/data-processed/cost-control-menu.png)

单击成本控制图标后，会显示侧栏：

![成本控制配置](./media/data-processed/cost-control-sidebar.png)

若要设置一项或多项预算，请首先单击与要设置的预算对应的“启用”单选按钮，然后在文本框中输入整数值。 值的单位为 TB。 配置所需预算后，请单击侧栏底部的“应用”按钮。 就是这样。现在你已设置了预算。

## <a name="configure-cost-control-for-serverless-sql-pool-in-t-sql"></a>使用 T-SQL 配置无服务器 SQL 池的成本控制

若要使用 T-SQL 为无服务器 SQL 池配置成本控制，你需要执行下面的一个或多个存储过程。

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

若要查看当前配置，请执行以下 T-SQL 语句：

```sql
SELECT * FROM sys.configurations
WHERE name like 'Data processed %';
```

若要查看当天、当周或当月处理的数据量，请执行以下 T-SQL 语句：

```sql
SELECT * FROM sys.dm_external_data_processed
```

## <a name="next-steps"></a>后续步骤

若要了解如何优化查询的性能，请参阅[无服务器 SQL 池的最佳做法](best-practices-sql-on-demand.md)。
