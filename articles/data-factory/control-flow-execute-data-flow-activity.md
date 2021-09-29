---
title: 数据流活动
titleSuffix: Azure Data Factory & Azure Synapse
description: 如何从 Azure 数据工厂或 Azure Synapse Analytics 管道内部执行数据流。
author: kromerm
ms.service: data-factory
ms.subservice: data-flows
ms.custom: synapse
ms.topic: conceptual
ms.author: makromer
ms.date: 09/09/2021
ms.openlocfilehash: 14fa6484a3cba97602d33cf2b302d62bf6ef2871
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124743878"
---
# <a name="data-flow-activity-in-azure-data-factory-and-azure-synapse-analytics"></a>Azure 数据工厂和 Azure Synapse Analytics 中的数据流活动

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

属性 | 说明 | 允许的值 | 必须
-------- | ----------- | -------------- | --------
数据流 | 对正在执行的数据流的引用 | DataFlowReference | 是
integrationRuntime | 运行数据流的计算环境。 如果未指定，将使用自动解析 Azure Integration Runtime。 | IntegrationRuntimeReference | 否
compute.coreCount | Spark 群集中使用的内核数。 仅当使用自动解析 Azure Integration Runtime 时才能指定 | 8、16、32、48、80、144、272 | 否
compute.computeType | Spark 群集中使用的计算类型。 仅当使用自动解析 Azure Integration Runtime 时才能指定 | "General", "ComputeOptimized", "MemoryOptimized" | 否
staging.linkedService | 如果使用的是 Azure Synapse Analytics 源或接收器，请指定用于 PolyBase 暂存的存储帐户。<br/><br/>如果 Azure 存储配置了 VNet 服务终结点，则必须在存储帐户上启用“允许受信任的 Microsoft 服务”并使用托管标识身份验证，详见[将 VNet 服务终结点与 Azure 存储配合使用的影响](../azure-sql/database/vnet-service-endpoint-rule-overview.md#impact-of-using-virtual-network-service-endpoints-with-azure-storage)。 另请了解 [Azure Blob](connector-azure-blob-storage.md#managed-identity) 和 [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#managed-identity) 的所需配置。<br/> | LinkedServiceReference | 仅当数据流读取或写入 Azure Synapse Analytics 时
staging.folderPath | 如果使用的是 Azure Synapse Analytics 源或接收器，则为 blob 存储帐户中用于 PolyBase 暂存的文件夹路径 | 字符串 | 仅当数据流读取或写入 Azure Synapse Analytics 时
traceLevel | 设置数据流活动执行的日志记录级别 | 精细、粗略、无 | 否

:::image type="content" source="media/data-flow/activity-data-flow.png" alt-text="执行数据流":::

### <a name="dynamically-size-data-flow-compute-at-runtime"></a>在运行时动态调整数据流大小

可以动态设置“核心计数”和“计算类型”属性，以在运行时调整传入源数据的大小。 使用管道活动（例如“查找”或“获取元数据”），以便查找源数据集数据的大小。 然后，在“数据流”活动属性中使用“添加动态内容”。

> [!NOTE]
> 在 Azure Synapse 数据流中选择驱动程序和工作器节点核心时，将会始终使用至少 3 个节点。

:::image type="content" source="media/data-flow/dyna1.png" alt-text="动态数据流":::

[下面是介绍此方法的简短视频教程](https://www.youtube.com/watch?v=jWSkJdtiJNM)

### <a name="data-flow-integration-runtime"></a>数据流集成运行时

选择用于数据流活动执行的 Integration Runtime。 在默认情况下，服务会将自动解析 Azure Integration Runtime 与四个工作器核心配合使用。 此 IR 具有常规用途计算类型，并在与服务实例相同的区域中运行。 对于已运营化的管道，强烈建议创建你自己的 Azure Integration Runtime，用于定义执行数据流活动所需的特定区域、计算类型、核心计数和 TTL。

对于大多数生产工作负荷，建议至少使用一个采用 8+8（总计 16）个 v-Core 和 10 分钟配置的“常规用途”计算类型（对于大型工作负荷，建议不要使用“计算优化”计算类型）。 通过设置小型 TTL，Azure IR 可以维护一个热群集，它不会出现冷群集那种需要几分钟时间才能启动的情况。 通过在 Azure IR 数据流配置上选择“快速重复使用”，你还可以进一步加快数据流的执行速度。 有关详细信息，请参阅 [Azure Integration Runtime](concepts-integration-runtime.md)。

:::image type="content" source="media/data-flow/ir-new.png" alt-text="Azure Integration Runtime":::

> [!IMPORTANT]
> 数据流活动中的 Integration Runtime 选择仅适用于管道的已触发执行。 如果使用数据流调试管道，则可在群集（在调试会话中指定）上运行。

### <a name="polybase"></a>PolyBase

如果使用 Azure Synapse Analytics 作为接收器或源，则必须为 PolyBase 批量加载选择一个暂存位置。 PolyBase 允许批量加载而不是逐行加载数据。 PolyBase 可大大减少 Azure Synapse Analytics 的加载时间。

## <a name="logging-level"></a>日志记录级别

如果不需要数据流活动的每个管道执行完整地记录所有详细的遥测日志，则可根据需要将日志记录级别设置为“基本”或“无”。 在“详细”模式（默认）下执行数据流时，要求此服务在数据转换期间完整地记录每个分区级别的活动。 该操作成本昂贵，因此仅在进行故障排除时启用“详细”模式可优化整体数据流和管道性能。 “基本”模式仅记录转换持续时间，而“无”模式仅提供持续时间的摘要。

:::image type="content" source="media/data-flow/logging.png" alt-text="日志记录级别":::

## <a name="sink-properties"></a>接收器属性

使用数据流中的分组功能，既可以设置接收器的执行顺序，又可以使用相同的组号将接收器分组在一起。 为帮助管理组，可以要求此服务在同一组中运行接收器，以并行运行。 还可以将接收器组设置为继续，即使其中一个接收器遇到错误仍可这样设置。

数据流接收器的默认行为是以串行方式顺序执行每个接收器，并且在接收器中遇到错误时使数据流失败。 此外，除非进入数据流属性并为接收器设置不同的优先级，否则所有接收器均默认为同一组。

### <a name="first-row-only"></a>仅第一行

此选项仅适用于已为“输出到活动”启用了缓存接收器的数据流。 直接注入到管道中的数据流的输出限制为 2MB。 设置“仅第一行”有助于在将数据流活动输出直接注入到管道时限制来自数据流的数据输出。

:::image type="content" source="media/data-flow/sink-properties.png" alt-text="接收器属性":::

## <a name="parameterizing-data-flows"></a>将数据流参数化

### <a name="parameterized-datasets"></a>参数化数据集

如果数据流使用参数化数据集，请在“设置”选项卡中设置参数值。

:::image type="content" source="media/data-flow/params.png" alt-text="执行数据流参数":::

### <a name="parameterized-data-flows"></a>参数化数据流

如果数据流已参数化，请在“参数”选项卡中设置数据流参数的动态值。可以使用管道表达式语言或数据流表达式语言来分配动态或文字参数值。 有关详细信息，请参阅[数据流参数](parameters-data-flow.md)。

### <a name="parameterized-compute-properties"></a>参数化计算属性。

如果使用自动解析 Azure Integration Runtime 并为 compute.coreCount 和 compute.computeType 指定值，则可以将核心数或计算类型参数化。

:::image type="content" source="media/data-flow/parameterize-compute.png" alt-text="执行数据流参数示例":::

## <a name="pipeline-debug-of-data-flow-activity"></a>数据流活动的管道调试

若要执行通过数据流活动运行的调试管道，则必须通过顶部栏中的“数据流调试”滑块来打开数据流调试模式。 借助调试模式，可以针对活动的 Spark 群集运行数据流。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

:::image type="content" source="media/data-flow/debug-button-3.png" alt-text="显示“调试”按钮所在位置的屏幕截图":::

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

参阅支持的控制流活动： 

- [If Condition 活动](control-flow-if-condition-activity.md)
- [Execute Pipeline 活动](control-flow-execute-pipeline-activity.md)
- [For Each 活动](control-flow-for-each-activity.md)
- [Get Metadata 活动](control-flow-get-metadata-activity.md)
- [Lookup 活动](control-flow-lookup-activity.md)
- [Web 活动](control-flow-web-activity.md)
- [Until 活动](control-flow-until-activity.md)
