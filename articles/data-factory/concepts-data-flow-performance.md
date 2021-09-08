---
title: 映射数据流性能和优化指南
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解影响 Azure 数据工厂和 Azure Synapse Analytics 管道中映射数据流性能的关键因素。
author: kromerm
ms.topic: conceptual
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.date: 08/24/2021
ms.openlocfilehash: 1595d2984c4130fa89c52aec615941051fa1bb82
ms.sourcegitcommit: dcf1defb393104f8afc6b707fc748e0ff4c81830
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123099349"
---
# <a name="mapping-data-flows-performance-and-tuning-guide"></a>映射数据流性能和优化指南

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure 数据工厂和 Synapse 管道中的映射数据流提供一个无代码界面，用于大规模设计和运行数据转换。 如果你不熟悉映射数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)。 本文重点介绍调整和优化数据流以使它们达到性能基准的各种方法。

观看下面的视频，看看显示了用数据流转换数据的一些示例计时。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4rNxM]

## <a name="monitoring-data-flow-performance"></a>监视数据流性能

使用调试模式验证转换逻辑后，以管道中活动的形式端到端地运行数据流。 数据流使用[执行数据流活动](control-flow-execute-data-flow-activity.md)在管道内进行操作化。 相较于其他活动，数据流活动具有独特的监视体验，其中显示了转换逻辑的详细执行计划和性能配置文件。 若要查看数据流的详细监视信息，请单击管道的活动运行输出中的眼镜图标。 有关详细信息，请参阅[监视映射数据流](concepts-data-flow-monitoring.md)。

![数据流监视器](media/data-flow/monitoring-details.png "数据流监视器 2")

监视数据流性能时，需注意四个潜在瓶颈：

* 群集启动时间
* 从源中读取
* 转换时间
* 写入接收器 

![数据流监视](media/data-flow/monitoring-performance.png "数据流监视器 3")

群集启动时间是指启动 Apache Spark 群集所需的时间。 该值位于监视屏幕的右上角。 数据流在实时模型上运行，其中每个作业都使用独立的群集。 该启动时间通常为 3-5 分钟。 对于顺序作业，可以通过启用生存时间值来缩短该时间。 有关详细信息，请参阅 [Integration Runtime 性能](concepts-integration-runtime-performance.md#time-to-live)中的“生存时间”部分。

数据流利用 Spark 优化器对“阶段”中的业务逻辑进行重新排序并运行，从而尽可能快地执行。 对于数据流写入的每个接收器，监视输出列出每个转换阶段的持续时间，以及将数据写入接收器所需的时间。 最长的持续时间可能就是数据流的瓶颈。 如果耗时最久的转换阶段包含源，则建议考虑进一步优化读取时间。 如果转换耗时久，则可能需要重新分区或增加集成运行时的大小。 如果接收器处理时间耗时久，则可能需要扩展数据库或验证是否未输出到单个文件。

确定数据流的瓶颈后，请使用以下优化策略来优化性能。

## <a name="testing-data-flow-logic"></a>测试数据流逻辑

从 UI 设计和测试数据流时，使用调试模式可以针对实时 Spark 群集进行交互式测试。 这样，无需等待群集预热即可预览数据和执行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

## <a name="optimize-tab"></a>“优化”选项卡

“优化”选项卡包含用于配置 Spark 群集的分区方案的设置。 此选项卡存在于数据流的每个转换中，可指定是否要在转换完成后对数据进行重新分区。 调整分区可对跨计算节点的数据分布以及数据局部优化提供控制度，但会对数据流的总体性能同时造成正面影响和负面影响。

![屏幕截图显示“优化”选项卡，其中包含“分区选项”、“分区类型”和“分区数”。](media/data-flow/optimize.png)

默认选择“使用当前分区”，它指示服务保留转换的当前输出分区。 由于对数据进行重新分区需要时间，因此在大多数情况下建议选择“使用当前分区”。 建议对数据进行重新分区的情况包括：“进行会严重倾斜数据的聚合和联接后”或“在 SQL DB 上使用源分区时”。

若要更改任何转换的分区，请选择“优化”选项卡并选择“设置分区”单选按钮 。 你会看到一系列用于分区的选项。 最佳的分区方法根据数据量、候选键、null 值和基数而异。 

> [!IMPORTANT]
> 单一分区将所有分布式数据合并到一个分区。 此操作非常慢，还会严重影响所有下游转换和写入。 强烈建议不要使用此选项，除非有明确的业务原因要使用此选项。

以下分区选项在每个转换中都可用：

### <a name="round-robin&quot;></a>轮循机制 

轮循机制将数据平均分布到各个分区。 如果没有适当的候选键用于实施可靠、智能的分区策略，请使用轮循机制。 可以设置物理分区数目。

### <a name=&quot;hash&quot;></a>哈希

此服务将生成列的哈希来生成统一的分区，使包含类似值的行划归到同一分区。 使用“哈希”选项时，请测试是否存在分区倾斜。 可以设置物理分区数目。

### <a name=&quot;dynamic-range&quot;></a>动态范围

动态范围基于提供的列或表达式使用 Spark 动态范围。 可以设置物理分区数目。 

### <a name=&quot;fixed-range&quot;></a>固定范围

生成一个表达式用于在分区的数据列中提供值的固定范围。 使用此选项之前，应该充分了解你的数据，以避免分区倾斜。 为表达式输入的值将用作分区函数的一部分。 可以设置物理分区数目。

### <a name=&quot;key&quot;></a>键

如果你已充分了解数据的基数，则键分区可能是不错的策略。 键分区为列中的每个唯一值创建分区。 无法设置分区数目，因为该数目基于数据中的唯一值。

> [!TIP]
> 手动设置分区方案会重新组合数据，可能会抵消 Spark 优化器的优势。 最佳做法是不要手动设置分区，除非有需要。

## <a name=&quot;logging-level&quot;></a>日志记录级别

如果不需要数据流活动的每个管道执行完整地记录所有详细的遥测日志，则可根据需要将日志记录级别设置为“基本”或“无”。 在“详细”模式（默认）下执行数据流时，要求此服务在数据转换期间完整地记录每个分区级别的活动。 该操作成本昂贵，因此仅在进行故障排除时启用“详细”模式可优化整体数据流和管道性能。 “基本”模式仅记录转换持续时间，而“无”模式仅提供持续时间的摘要。

![日志记录级别](media/data-flow/logging.png &quot;设置日志记录级别")

## <a name="optimizing-sources"></a>优化源

对于除 Azure SQL 数据库以外的每个源，建议将“使用当前分区”保留为选定值。 读取其他所有源系统时，数据流会根据数据的大小自动且均匀地对数据进行分区。 大约每 128 MB 数据就会创建一个新分区。 随着数据大小的增加，分区的数量也会增加。

所有自定义分区均在 Spark 读取数据后进行，并且将对数据流性能产生负面影响。 由于数据在读取时会均匀分区，因此不建议这样做。 

> [!NOTE]
> 读取速度可能会受源系统吞吐量的限制。

### <a name="azure-sql-database-sources"></a>Azure SQL 数据库源

Azure SQL 数据库有唯一的分区选项，称为“源”分区。 启用源分区会在源系统上启用并行连接，从而能够增加读取 Azure SQL DB 的次数。 指定分区数以及数据分区方式。 使用具有高基数的分区列。 你也可以输入与源表的分区方案匹配的查询。

> [!TIP]
> 对于源分区，SQL Server 的 I/O 是瓶颈。 添加太多分区可能会导致源数据库饱和。 通常，使用此选项时，最好添加 4-5 个分区。

![源分区](media/data-flow/sourcepart3.png "源分区")

#### <a name="isolation-level"></a>隔离级别

Azure SQL 源系统上的读取隔离级别会影响性能。 如果选择“读取未提交的内容”，则将提供最快的性能，并可防止任何数据库锁定。 若要了解有关 SQL 隔离级别的更多信息，请参阅[了解隔离级别](/sql/connect/jdbc/understanding-isolation-levels)。

#### <a name="read-using-query"></a>使用查询进行读取

可以使用表或 SQL 查询读取 Azure SQL 数据库。 如果要执行 SQL 查询，则必须先完成查询，然后才能开始转换。 SQL 查询对下推操作非常有用，可加快执行速度并减少从 SQL Server 读取的数据量，例如 SELECT、WHERE 和 JOIN 语句。 下推操作时，你将无法在数据进入数据流之前跟踪转换的世系和性能。

### <a name="azure-synapse-analytics-sources"></a>Azure Synapse Analytics 源

使用 Azure Synapse Analytics 时，源选项中有一个名为“启用暂存”的设置。 这允许服务使用 ```Staging``` 从 Synapse 进行读取操作，从而可使用 [Synapse COPY 语句](/sql/t-sql/statements/copy-into-transact-sql.md)命令大幅提升读取性能，以便获得最高性能的大容量加载功能。 启用 ```Staging``` 需要在数据流活动设置中指定 Azure Blob 存储或 Azure Data Lake Storage Gen2 暂存位置。

![启用暂存](media/data-flow/enable-staging.png "启用暂存")

### <a name="file-based-sources"></a>基于文件的源

尽管数据流支持多种文件类型，但建议使用 Spark 原生 Parquet 格式来实现最佳读写次数。

如果要在一组文件中运行同一数据流，建议读取文件夹、使用通配符路径或读取文件列表。 一次数据流活动运行可以批量处理所有文件。 有关如何配置这些设置的详细信息，可参阅 [Azure Blob 存储连接器](connector-azure-blob-storage.md#source-transformation)文档的“源转换”部分。

如果可能，请避免使用 For-Each 活动对一组文件运行数据流。 这会导致 for-each 在每次迭代时启动自己的 Spark 群集，这通常既无必要，又成本昂贵。 

## <a name="optimizing-sinks"></a>优化接收器

当数据流写入接收器时，所有自定义分区都会在写入前一刻进行。 与源相同，大多数情况下建议将“使用当前分区”保留为选定的分区选项。 即使目标未分区，已分区数据的写入速度也将比未分区数据的写入速度快得多。 以下是各接收器类型的相应注意事项。 

### <a name="azure-sql-database-sinks"></a>Azure SQL 数据库接收器

在 Azure SQL 数据库中，大多数情况下默认分区均可正常运行。 接收器的分区可能过多，SQL 数据库无法处理。 如果遇到这种情况，请减少 SQL 数据库接收器输出的分区数。

#### <a name="impact-of-error-row-handling-to-performance"></a>错误行处理对性能的影响

在接收器转换中启用错误行处理（“出错时继续”）时，服务将先执行额外的步骤，然后再将兼容行写入目标表。 该额外步骤将对性能造成 <5% 的负面影响，如果将选项设置为“再将不兼容的行添加到日志文件”，则还会对性能再造成一些影响。

#### <a name="disabling-indexes-using-a-sql-script"></a>使用 SQL 脚本禁用索引

在将数据加载到 SQL 数据库中之前兼用索引可以显著优化写入表的性能。 在写入 SQL 接收器之前，运行以下命令。

`ALTER INDEX ALL ON dbo.[Table Name] DISABLE`

写入完成后，使用以下命令重新生成索引：

`ALTER INDEX ALL ON dbo.[Table Name] REBUILD`

可以在映射数据流中的 Azure SQL DB 或 Synapse 接收器内使用 Pre-SQL 和 Post-SQL 脚本来原生完成此操作。

![禁用索引](media/data-flow/disable-indexes-sql.png "禁用索引")

> [!WARNING]
> 禁用索引时，数据流可有效地控制数据库，此时查询不太可能成功。 因此，大量 ETL 作业会在半夜触发，以避免此冲突。 有关详细信息，请了解[禁用 SQL 索引的约束](/sql/relational-databases/indexes/disable-indexes-and-constraints)

#### <a name="scaling-up-your-database"></a>纵向扩展数据库

在管道运行的前面，计划重设源和接收器 Azure SQL 数据库与数据仓库的大小，以提高吞吐量，并在达到 DTU 限制后尽量减小 Azure 限制。 管道执行完成后，根据数据库的正常运行速率重设其大小。

### <a name="azure-synapse-analytics-sinks"></a>Azure Synapse Analytics 接收器

写入 Azure Synapse Analytics 时，请确保将“启用暂存”设置为 true。 这使得服务可以使用 [SQL COPY 命令](/sql/t-sql/statements/copy-into-transact-sql)进行写入操作，从而有效地批量加载数据。 使用暂存时，需引用 Azure Data Lake Storage Gen2 或 Azure Blob 存储帐户来暂存数据。

除暂存以外，Azure Synapse Analytics 和 Azure SQL 数据库的最佳做法相同。

### <a name="file-based-sinks"></a>基于文件的接收器 

尽管数据流支持多种文件类型，但建议使用 Spark 原生 Parquet 格式来实现最佳读写次数。

如果数据均匀分布，则“使用当前分区”将是写入文件最快的分区选项。

#### <a name="file-name-options"></a>文件名选项

写入文件时，可以选择命名选项，每个选项都会对性能产生影响。

![接收器选项](media/data-flow/file-sink-settings.png "接收器选项​​")

如果选择“默认”选项，则写入速度最快。 每个分区将等同于具有 Spark 默认名称的文件。 如果只读取文件夹中的数据，这会很有用。

设置命名模式会将每个分区文件重命名为更便于用户理解的名称。 此操作在写入后进行，相较于选择默认选项要稍慢一些。 每个分区均允许手动命名每个单独的分区。

如果某列与数据的输出方式相符，则可以选择“作为列中的数据”。 如果列分布不均匀，则会重新组合数据并会影响性能。

“输出到单个文件”会将所有数据合并到一个分区中。 这会导致写入时间过长，尤其是对于大型数据集。 强烈建议不要使用此选项，除非有明确的业务原因要使用此选项。

### <a name="cosmosdb-sinks"></a>CosmosDB 接收器

写入 CosmosDB 时，在数据流执行期间更改吞吐量和批大小可以优化性能。 这些更改仅在数据流活动运行期间生效，并且将在完成后返回到原始集合设置。 

**批大小：** 通常，从默认批大小开始就足够了。 若要进一步优化此值，请计算数据大概的对象大小，并确保对象大小 * 批大小小于 2MB。 如果小于 2MB，增大批大小可获得更好的吞吐量。

**吞吐量：** 在此处设置较高的吞吐量能够以更快的速度将文档写入 CosmosDB。 请记住，吞吐量设置越高，RU 成本越高。

**写入吞吐量预算：** 使用小于每分钟 RU 总数的值。 如果某个数据流包含大量的 Spark 分区，则设置预算吞吐量可以在这些分区之间实现更好的平衡。

## <a name="optimizing-transformations"></a>优化转换

### <a name="optimizing-joins-exists-and-lookups"></a>优化联接、存在和查找

#### <a name="broadcasting"></a>广播

在联接、查找和存在转换中，如果数据流足够小，工作器节点内存可容纳一个数据流或同时容纳两个数据流，则可以通过启用“广播”来优化性能。 广播是指将较小的数据帧发送到群集中的所有节点。 这样，Spark 引擎即可在不重新组合大型流中的数据的情况下执行联接。 默认情况下，Spark 引擎将自动决定是否广播联接的一侧。 如果你熟悉传入的数据并且知道一个流将显著小于另一个流，则可以选择固定广播。 固定广播会强制 Spark 广播选定的流。 

如果广播数据的大小对于 Spark 节点而言太大，则可能会发生内存不足错误。 为避免内存不足错误，请使用内存优化群集。 如果在数据流执行期间遇到广播超时，可以关闭广播优化。 但是，这会导致数据流执行速度变慢。

当处理可能需要较长时间才能进行查询的数据源时（如大型数据库查询），建议关闭联接的广播。 当群集尝试广播到计算节点时，查询时间较长的源可能会导致 Spark 超时。 关闭广播的另一种不错的选择是：当数据流中有一个流时，该流正在聚合要在以后的查找转换中使用的值。 此模式可能会迷惑 Spark 优化器并导致超时。

![联接转换优化](media/data-flow/joinoptimize.png "联接优化")

#### <a name="cross-joins"></a>交叉联接

如果在联接条件中使用文本值，或者在联接的两侧具有多个匹配，Spark 会将联接作为交叉联接运行。 交叉联接是完全笛卡尔积，可筛选掉联接的值。 它比其他联接类型要慢得多。 确保在联接条件的两侧都具有列引用，以避免影响性能。

#### <a name="sorting-before-joins"></a>联接前排序

与 SSIS 等工具中的合并联接不同，联接转换不是强制性的合并联接操作。 联接键在转换前无需排序。 不建议在映射数据流中使用排序转换。

### <a name="window-transformation-performance"></a>窗口转换性能

[映射数据流中的窗口转换](data-flow-window.md)根据你在转换设置的 ```over()``` 子句中选择的列值对数据进行分区。 窗口转换中公开了大量非常常用的聚合和分析函数。 但如果要为排名 ```rank()``` 或行号 ```rowNumber()``` 在整个数据集上生成窗口，则建议改用[排名转换](data-flow-rank.md)和[代理键转换](data-flow-surrogate-key.md)。 这些转换将再次使用这些函数更好地执行完整数据集操作。

### <a name="repartitioning-skewed-data"></a>对倾斜的数据重新分区

某些转换（例如联接和聚合）会重新组合数据分区，有时可能会导致数据倾斜。 数据倾斜意味着数据未均匀地分布于各个分区。 数据严重倾斜会导致下游转换和接收器写入变慢。 可以通过单击监视显示器中的转换在数据流运行的任何时间点检查数据的倾斜度。

![倾斜度和峰度](media/data-flow/skewness-kurtosis.png "倾斜度和峰度")

监视显示器将显示数据如何分布到每个分区，以及倾斜度和峰度两个指标。 倾斜度是数据不对称程度的度量，可以是正、零、负或未定义的值。 负倾斜表示左尾比右尾长。 峰度是表示数据是重尾分布还是轻尾分布的度量。 高峰度值表示不理想。 理想的倾斜度范围在 -3 到 3 之间，而理想的峰度范围则是小于 10。 查看分区图并查看 1 bar 是否明显大于其余部分可轻松理解这些数字。

如果转换后数据未均匀分区，可以使用[优化选项卡](#optimize-tab)进行重新分区。 重新组合数据需要时间，并且可能无法优化数据流性能。

> [!TIP]
> 如果对数据进行了重新分区，但下游转换重新组合了数据，请在用作联接键的列上使用哈希分区。

## <a name="using-data-flows-in-pipelines"></a>在管道中使用数据流 

生成具有多个数据流的复杂管道时，逻辑流可能会对时序和成本产生重大影响。 本部分介绍各体系结构策略的影响。

### <a name="executing-data-flows-in-parallel"></a>并行执行数据流

如果并行执行多个数据流，则服务会为每个活动启动单独的 Spark 群集。 这样，每个作业将是独立的，并且将并行运行，但这会导致多个群集同时运行。

如果数据流并行执行，建议不启用 Azure IR 生存时间属性，因为该属性会导致多个暖池处于空闲状态。

> [!TIP]
> 你无需在 for each 活动中多次运行同一数据流，而可将数据暂存到数据湖中并使用通配符路径处理单个数据流中的数据。

### <a name="execute-data-flows-sequentially"></a>按顺序执行数据流

如果按顺序执行数据流活动，则建议在 Azure IR 配置中设置 TTL。 服务将重新使用计算资源，从而缩短群集启动时间。 每个活动将仍处于独立状态，并将为每个执行接收新的 Spark 上下文。 若要进一步减少连续活动之间的时间，请在 Azure IR 上设置“快速重用”复选框，让服务重新使用现有群集。

### <a name="overloading-a-single-data-flow"></a>重载单一数据流

如果将所有逻辑放入单一数据流，则服务将在单个 Spark 实例上执行整个作业。 尽管这似乎可以降低成本，但它混合了各种逻辑流，可能难以监视和调试。 如果一个组件失败，则该作业的所有其他部分也会失败。 建议按业务逻辑的独立流来组织数据流。 如果数据流太大，将其拆分为单独的组件即可更轻松地进行监视和调试。 尽管对数据流中的转换数量没有硬性限制，但转换过多会导致作业变得复杂。

### <a name="execute-sinks-in-parallel"></a>并行执行接收器

数据流接收器默认以串行方式顺序执行每个接收器，并且会在接收器中遇到错误时使数据流失败。 此外，除非进入数据流属性并为接收器设置不同的优先级，否则所有接收器均默认为同一组。

数据流允许你在 UI 设计器的“数据流属性”选项卡中将接收器组合在一起。 可以设置接收器的执行顺序，也可以使用同一组号将接收器组合在一起。 为帮助管理组，可以要求服务在同一组中运行接收器，以并行运行。

在管道上，“接收器属性”部分下的“执行数据流”活动是一个开启并行接收器加载的选项。 启用“并行运行”即指示数据流同时（而不是按顺序）写入连接的接收器。 为了使用并行选项，接收器必须组合在一起，并且必须通过“新建分支”或“条件拆分”连接到同一流。

## <a name="next-steps"></a>后续步骤

请参阅与性能相关的其他数据流文章：

- [数据流活动](control-flow-execute-data-flow-activity.md)
- [监视数据流性能](concepts-data-flow-monitoring.md)
- [Integration Runtime 性能](concepts-integration-runtime-performance.md)
