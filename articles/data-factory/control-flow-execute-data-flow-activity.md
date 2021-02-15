---
title: 数据流活动
description: 如何在数据工厂管道中执行数据流。
author: kromerm
ms.service: data-factory
ms.topic: conceptual
ms.author: makromer
ms.date: 01/03/2021
ms.openlocfilehash: 0663690318773ccad3bddfaaa03e456c2f58895e
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/14/2021
ms.locfileid: "100383375"
---
# <a name="data-flow-activity-in-azure-data-factory"></a>Azure 数据工厂中的数据流活动

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

使用数据流活动通过映射数据流来转换和移动数据。 如果你不熟悉数据流，请参阅[映射数据流概述](concepts-data-flow-overview.md)

## <a name="syntax"></a>语法

```json
{
    "name": "MyDataFlowActivity",
    "type": "ExecuteDataFlow",
    "typeProperties": {
      "dataflow": {
         "referenceName": "MyDataFlow",
         "type": "DataFlowReference"
      },
      "compute": {
         "coreCount": 8,
         "computeType": "General"
      },
      "traceLevel": "Fine",
      "runConcurrently": true,
      "continueOnError": true,      
      "staging": {
          "linkedService": {
              "referenceName": "MyStagingLinkedService",
              "type": "LinkedServiceReference"
          },
          "folderPath": "my-container/my-folder"
      },
      "integrationRuntime": {
          "referenceName": "MyDataFlowIntegrationRuntime",
          "type": "IntegrationRuntimeReference"
      }
}

```

## <a name="type-properties"></a>Type 属性

properties | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | --------
数据流 | 对正在执行的数据流的引用 | DataFlowReference | 是
integrationRuntime | 运行数据流的计算环境。 如果未指定，将使用自动解析 Azure Integration Runtime。 | IntegrationRuntimeReference | 否
compute.coreCount | Spark 群集中使用的内核数。 仅当使用自动解析 Azure Integration Runtime 时才能指定 | 8、16、32、48、80、144、272 | 否
compute.computeType | Spark 群集中使用的计算类型。 仅当使用自动解析 Azure Integration Runtime 时才能指定 | "General", "ComputeOptimized", "MemoryOptimized" | 否
staging.linkedService | 如果使用的是 Azure Synapse Analytics 源或接收器，请指定用于 PolyBase 暂存的存储帐户。<br/><br/>如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。 另请了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 的所需配置。<br/> | LinkedServiceReference | 仅当数据流读取或写入 Azure Synapse Analytics 时
staging.folderPath | 如果使用的是 Azure Synapse Analytics 源或接收器，则为 blob 存储帐户中用于 PolyBase 暂存的文件夹路径 | 字符串 | 仅当数据流读取或写入 Azure Synapse Analytics 时
traceLevel | 设置数据流活动执行的日志记录级别 | 精细、粗略、无 | 否

![执行数据流](media/data-flow/activity-data-flow.png "执行数据流")

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>在运行时动态调整数据流大小

可以动态设置“核心计数”和“计算类型”属性，以在运行时调整传入源数据的大小。 使用管道活动（例如“查找”或“获取元数据”），以便查找源数据集数据的大小。 然后，在“数据流”活动属性中使用“添加动态内容”。

![动态数据流](media/data-flow/dyna1.png "动态数据流")

[下面是介绍此方法的简短视频教程](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>数据流集成运行时

选择用于数据流活动执行的 Integration Runtime。 默认情况下，数据工厂将使用具有四个辅助角色核心且没有生存时间 (TTL) 的自动解析 Azure Integration Runtime。 此 IR 具有常规用途计算类型，并在与工厂相同的区域中运行。 可以创建自己的 Azure Integration Runtime，使其用于定义数据流活动执行所需的特定区域、计算类型、核心数和 TTL。

对于管道执行，群集是一个作业群集，在执行开始之前需要花费几分钟来启动。 如果未指定 TTL，则每次管道运行都需要此启动时间。 如果指定 TTL，暖群集池将在上一次执行后指定的时间内保持活动状态，从而缩短启动时间。 例如，如果 TTL 为 60 分钟，并且每小时运行一次数据流，则群集池将保持活动状态。 有关详细信息，请参阅 [Azure Integration Runtime](concepts-integration-runtime.md)。

![Azure Integration Runtime](media/data-flow/ir-new.png "Azure Integration Runtime")

> [!IMPORTANT]
> 数据流活动中的 Integration Runtime 选择仅适用于管道的已触发执行。 如果使用数据流调试管道，则可在群集（在调试会话中指定）上运行。

### <a name="polybase"></a>PolyBase

如果使用 Azure Synapse Analytics 作为接收器或源，则必须为 PolyBase 批量加载选择一个暂存位置。 PolyBase 允许批量加载而不是逐行加载数据。 PolyBase 可大大减少 Azure Synapse Analytics 的加载时间。

## <a name="logging-level"></a>日志记录级别

如果不需要数据流活动的每个管道执行完整地记录所有详细的遥测日志，则可根据需要将日志记录级别设置为“基本”或“无”。 在“详细”模式（默认）下执行数据流时，要求 ADF 在数据转换期间完整地记录每个分区级别的活动。 该操作成本昂贵，因此仅在进行故障排除时启用“详细”模式可优化整体数据流和管道性能。 “基本”模式仅记录转换持续时间，而“无”模式仅提供持续时间的摘要。

![日志记录级别](media/data-flow/logging.png "设置日志记录级别")

## <a name="sink-properties"></a>接收器属性

使用数据流中的分组功能，既可以设置接收器的执行顺序，又可以使用相同的组号将接收器分组在一起。 为了帮助管理组，可以要求 ADF 并行运行同一组中的接收器。 还可以将接收器组设置为继续，即使其中一个接收器遇到错误仍可这样设置。

数据流接收器的默认行为是以串行方式顺序执行每个接收器，并且在接收器中遇到错误时使数据流失败。 此外，除非进入数据流属性并为接收器设置不同的优先级，否则所有接收器均默认为同一组。

![接收器属性](media/data-flow/sink-properties.png "设置接收器属性")

## <a name="parameterizing-data-flows"></a>将数据流参数化

### <a name="parameterized-datasets"></a>参数化数据集

如果数据流使用参数化数据集，请在“设置”选项卡中设置参数值。

![执行数据流参数](media/data-flow/params.png "参数")

### <a name="parameterized-data-flows"></a>参数化数据流

如果数据流已参数化，请在“参数”选项卡中设置数据流参数的动态值。可以使用 ADF 管道表达式语言或数据流表达式语言来分配动态或文字参数值。 有关详细信息，请参阅[数据流参数](parameters-data-flow.md)。

### <a name="parameterized-compute-properties"></a>参数化计算属性。

如果使用自动解析 Azure Integration Runtime 并为 compute.coreCount 和 compute.computeType 指定值，则可以将核心数或计算类型参数化。

![执行数据流参数示例](media/data-flow/parameterize-compute.png "参数示例")

## <a name="pipeline-debug-of-data-flow-activity"></a>数据流活动的管道调试

若要执行通过数据流活动运行的调试管道，则必须通过顶部栏中的“数据流调试”滑块来打开数据流调试模式。 借助调试模式，可以针对活动的 Spark 群集运行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

![“调试”按钮](media/data-flow/debugbutton.png "“调试”按钮")

调试管道针对活动的调试群集运行，而不是针对“数据流”活动设置中指定的集成运行时环境运行。 在启动调试模式时，可以选择调试计算环境。

## <a name="monitoring-the-data-flow-activity"></a>监视数据流活动

数据流活动具有特殊的监视体验，你可以在其中查看分区、暂存时间和数据世系信息。 通过“操作”下的眼镜图标打开监视窗格。 有关详细信息，请参阅[监视数据流](concepts-data-flow-monitoring.md)。

### <a name="use-data-flow-activity-results-in-a-subsequent-activity"></a>使用数据流活动会导致后续活动

数据流活动输出有关写入每个接收器的行数和从每个源读取的行数的指标。 这些结果将在活动运行结果的 `output` 部分中返回。 返回的指标采用以下 json 格式。

``` json
{
    "runStatus": {
        "metrics": {
            "<your sink name1>": {
                "rowsWritten": <number of rows written>,
                "sinkProcessingTime": <sink processing time in ms>,
                "sources": {
                    "<your source name1>": {
                        "rowsRead": <number of rows read>
                    },
                    "<your source name2>": {
                        "rowsRead": <number of rows read>
                    },
                    ...
                }
            },
            "<your sink name2>": {
                ...
            },
            ...
        }
    }
}
```

例如，要获取在名为“dataflowActivity”的活动中写入接收器“sink1”的行数，可使用 `@activity('dataflowActivity').output.runStatus.metrics.sink1.rowsWritten`。

要获取从该接收器中使用的源“source1”读取的行数，可使用 `@activity('dataflowActivity').output.runStatus.metrics.sink1.sources.source1.rowsRead`。

> [!NOTE]
> 如果接收器写入的行数为零，则它将不会显示在指标中。 可以使用 `contains` 函数来验证行是否存在。 例如，`contains(activity('dataflowActivity').output.runStatus.metrics, 'sink1')` 将检查是否存在写入 sink1 的任意行。

## <a name="next-steps"></a>后续步骤

请参阅数据工厂支持的控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
