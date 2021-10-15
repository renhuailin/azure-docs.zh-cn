---
title: 优化映射数据流中的源性能
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何优化 Azure 数据工厂和 Azure Synapse Analytics 管道的映射数据流中的源性能。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 09/29/2021
ms.openlocfilehash: 7fef72d1ca52ae04106797c8ce7088b259aad033
ms.sourcegitcommit: 613789059b275cfae44f2a983906cca06a8706ad
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129293497"
---
# <a name="optimizing-sources"></a>优化源

对于除 Azure SQL 数据库以外的每个源，建议将“使用当前分区”保留为选定值。 读取其他所有源系统时，数据流会根据数据的大小自动且均匀地对数据进行分区。 大约每 128 MB 数据就会创建一个新分区。 随着数据大小的增加，分区的数量也会增加。

所有自定义分区均在 Spark 读取数据后进行，并且将对数据流性能产生负面影响。 由于数据在读取时会均匀分区，因此不建议这样做。 

> [!NOTE]
> 读取速度可能会受源系统吞吐量的限制。

## <a name="azure-sql-database-sources"></a>Azure SQL 数据库源

Azure SQL 数据库有唯一的分区选项，称为“源”分区。 启用源分区会在源系统上启用并行连接，从而能够增加读取 Azure SQL DB 的次数。 指定分区数以及数据分区方式。 使用具有高基数的分区列。 你也可以输入与源表的分区方案匹配的查询。

> [!TIP]
> 对于源分区，SQL Server 的 I/O 是瓶颈。 添加太多分区可能会导致源数据库饱和。 通常，使用此选项时，最好添加 4-5 个分区。

:::image type="content" source="media/data-flow/sourcepart3.png" alt-text="源分区":::

### <a name="isolation-level"></a>隔离级别

Azure SQL 源系统上的读取隔离级别会影响性能。 如果选择“读取未提交的内容”，则将提供最快的性能，并可防止任何数据库锁定。 若要了解有关 SQL 隔离级别的更多信息，请参阅[了解隔离级别](/sql/connect/jdbc/understanding-isolation-levels)。

### <a name="read-using-query"></a>使用查询进行读取

可以使用表或 SQL 查询读取 Azure SQL 数据库。 如果要执行 SQL 查询，则必须先完成查询，然后才能开始转换。 SQL 查询对下推操作非常有用，可加快执行速度并减少从 SQL Server 读取的数据量，例如 SELECT、WHERE 和 JOIN 语句。 下推操作时，你将无法在数据进入数据流之前跟踪转换的世系和性能。

## <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics 源

使用 Azure Synapse Analytics 时，源选项中有一个名为“启用暂存”的设置。 这允许服务使用 ```Staging``` 从 Synapse 进行读取操作，从而可使用 [Synapse COPY 语句](/sql/t-sql/statements/copy-into-transact-sql)命令大幅提升读取性能，以便获得最高性能的大容量加载功能。 启用 ```Staging``` 需要在数据流活动设置中指定 Azure Blob 存储或 Azure Data Lake Storage Gen2 暂存位置。

:::image type="content" source="media/data-flow/enable-staging.png" alt-text="启用暂存":::

## <a name="file-based-sources"></a>基于文件的源

尽管数据流支持多种文件类型，但建议使用 Spark 原生 Parquet 格式来实现最佳读写次数。

如果要在一组文件中运行同一数据流，建议读取文件夹、使用通配符路径或读取文件列表。 一次数据流活动运行可以批量处理所有文件。 有关如何配置这些设置的详细信息，可参阅 [Azure Blob 存储连接器](connector-azure-blob-storage.md#source-transformation)文档的“源转换”部分。

如果可能，请避免使用 For-Each 活动对一组文件运行数据流。 这会导致 for-each 在每次迭代时启动自己的 Spark 群集，这通常既无必要，又成本昂贵。 

## <a name="next-steps"></a>后续步骤

- [数据流性能概述](concepts-data-flow-performance.md)
- [优化接收器](concepts-data-flow-performance-sinks.md)
- [优化转换](concepts-data-flow-performance-transformations.md)
- [在管道中使用数据流](concepts-data-flow-performance-pipelines.md)

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
- [Integration Runtime 性能](concepts-integration-runtime-performance.md)
