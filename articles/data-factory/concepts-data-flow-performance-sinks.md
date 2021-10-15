---
title: 优化映射数据流中的接收器性能
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何优化 Azure 数据工厂和 Azure Synapse Analytics 管道的映射数据流中的接收器性能。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 54c03cc8b4c34be02d3dee608ce4a759e75f2200
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293501"
---
# <a name="optimizing-sinks"></a>优化接收器

当数据流写入接收器时，所有自定义分区都会在写入前一刻进行。 与源相同，大多数情况下建议将“使用当前分区”保留为选定的分区选项。 即使目标未分区，已分区数据的写入速度也将比未分区数据的写入速度快得多。 以下是各接收器类型的相应注意事项。 

## <a name="azure-sql-database-sinks"></a>Azure SQL 数据库接收器

在 Azure SQL 数据库中，大多数情况下默认分区均可正常运行。 接收器的分区可能过多，SQL 数据库无法处理。 如果遇到这种情况，请减少 SQL 数据库接收器输出的分区数。

### <a name="impact-of-error-row-handling-to-performance"></a>错误行处理对性能的影响

在接收器转换中启用错误行处理（“出现错误时继续运行”）时，服务将执行额外的步骤，然后再将兼容行写入目标表。 该额外步骤将对性能造成 <5% 的负面影响，如果将选项设置为“再将不兼容的行添加到日志文件”，则还会对性能再造成一些影响。

### <a name="disabling-indexes-using-a-sql-script"></a>使用 SQL 脚本禁用索引

在将数据加载到 SQL 数据库中之前兼用索引可以显著优化写入表的性能。 在写入 SQL 接收器之前，运行以下命令。

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

写入完成后，使用以下命令重新生成索引：

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

可以在映射数据流中的 Azure SQL DB 或 Synapse 接收器内使用 Pre-SQL 和 Post-SQL 脚本来原生完成此操作。

:::image type="content" source="media/data-flow/disable-indexes-sql.png" alt-text="禁用索引":::

> [!WARNING]
> 禁用索引时，数据流可有效地控制数据库，此时查询不太可能成功。 因此，大量 ETL 作业会在半夜触发，以避免此冲突。 有关详细信息，请了解[禁用 SQL 索引的约束](/sql/relational-databases/indexes/disable-indexes-and-constraints)

### <a name="scaling-up-your-database"></a>纵向扩展数据库

在管道运行的前面，计划重设源和接收器 Azure SQL 数据库与数据仓库的大小，以提高吞吐量，并在达到 DTU 限制后尽量减小 Azure 限制。 管道执行完成后，根据数据库的正常运行速率重设其大小。

## <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics 接收器

写入 Azure Synapse Analytics 时，请确保将“启用暂存”设置为 true。 这使得服务可以使用 [SQL COPY 命令](/sql/t-sql/statements/copy-into-transact-sql)进行写入操作，从而有效地批量加载数据。 使用暂存时，需引用 Azure Data Lake Storage Gen2 或 Azure Blob 存储帐户来暂存数据。

除暂存以外，Azure Synapse Analytics 和 Azure SQL 数据库的最佳做法相同。

## <a name="file-based-sinks"></a>基于文件的接收器 

尽管数据流支持多种文件类型，但建议使用 Spark 原生 Parquet 格式来实现最佳读写次数。

如果数据均匀分布，则“使用当前分区”将是写入文件最快的分区选项。

### <a name="file-name-options"></a>文件名选项

写入文件时，可以选择命名选项，每个选项都会对性能产生影响。

:::image type="content" source="media/data-flow/file-sink-settings.png" alt-text="接收器选项":::

如果选择“默认”选项，则写入速度最快。 每个分区将等同于具有 Spark 默认名称的文件。 如果只读取文件夹中的数据，这会很有用。

设置命名模式会将每个分区文件重命名为更便于用户理解的名称。 此操作在写入后进行，相较于选择默认选项要稍慢一些。 每个分区均允许手动命名每个单独的分区。

如果某列与数据的输出方式相符，则可以选择“作为列中的数据”。 如果列分布不均匀，则会重新组合数据并会影响性能。

“输出到单个文件”会将所有数据合并到一个分区中。 这会导致写入时间过长，尤其是对于大型数据集。 除非有明确的业务原因要使用此选项，否则极不建议使用它。

## <a name="cosmosdb-sinks"></a>CosmosDB 接收器

写入 CosmosDB 时，在数据流执行期间更改吞吐量和批大小可以优化性能。 这些更改仅在数据流活动运行期间生效，并且将在完成后返回到原始集合设置。 

**批大小：** 通常，从默认批大小开始就足够了。 若要进一步优化此值，请计算数据大概的对象大小，并确保对象大小 * 批大小小于 2MB。 如果小于 2MB，增大批大小可获得更好的吞吐量。

**吞吐量：** 在此处设置较高的吞吐量能够以更快的速度将文档写入 CosmosDB。 请记住，吞吐量设置越高，RU 成本越高。

**写入吞吐量预算：** 使用小于每分钟 RU 总数的值。 如果某个数据流包含大量的 Spark 分区，则设置预算吞吐量可以在这些分区之间实现更好的平衡。

## <a name="next-steps"></a>后续步骤

- [数据流性能概述](concepts-data-flow-performance.md)
- [优化源](concepts-data-flow-performance-sources.md)
- [优化转换](concepts-data-flow-performance-transformations.md)
- [在管道中使用数据流](concepts-data-flow-performance-pipelines.md)

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
- [Integration Runtime 性能](concepts-integration-runtime-performance.md)
