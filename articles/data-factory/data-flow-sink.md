---
title: 映射数据流中的接收器转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在映射数据流中配置接收器转换。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/24/2021
ms.openlocfilehash: 39c8ed3f8d8b11839964ac376ac35badd6546411
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122824621"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>映射数据流中的接收器转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

转换数据后，使用接收器转换将其写入目标存储。 每个数据流至少需要一个接收器转换，但你可根据需要写入任意多个接收器来完成转换流。 若要写入更多接收器，请通过新的分支和有条件拆分创建新数据流。

每个接收器转换只与一个数据集对象或链接服务相关联。 接收器转换决定要写入的数据的形状和位置。

## <a name="inline-datasets"></a>内联数据集

创建接收器转换时，请选择是在数据集对象内还是在接收器转换中定义接收器信息。 大多数格式只适用于其中之一。 若要了解如何使用特定连接器，请参阅相应的连接器文档。

内联和数据集对象同时支持某一格式时，两者都有优势。 数据集对象是可重用的实体，可在其他数据流和复制等活动中使用。 使用强化的架构时，这些可重复使用的实体特别有用。 数据集不基于 Spark。 有时，可能需要在接收器转换中覆盖某些设置或架构投影。

如果使用灵活的架构、一次性接收器实例或参数化接收器，建议使用内联数据集。 如果接收器参数化程度高，则使用内联数据集可以不创建“虚拟”对象。 内联数据集基于 Spark，其属性是数据流原生的。

若要使用内联数据集，请在“接收器类型”选择器中选择所需的格式。 选择要与其连接的链接服务，而不是选择接收器数据集。

![显示已选择“内联”的屏幕截图。](media/data-flow/inline-selector.png "显示已选择“内联”的屏幕截图。")

## <a name="workspace-db-synapse-workspaces-only"></a>工作区 DB（仅 Synapse 工作区）

在 Azure Synapse 工作区中使用数据流时，有一个附加选项可以将数据直接接收到 Synapse 工作区的数据库类型中。 这将缓解为这些数据库添加链接服务或数据集的需求。

> [!NOTE]
> Azure Synapse 工作区 DB 连接器目前为公共预览版，只能与 Spark Lake 数据库配合使用

![显示选定工作区 DB 的屏幕截图。](media/data-flow/syms-sink.png "显示已选择“内联”的屏幕截图。")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> 支持的接收器类型

映射数据流遵循提取、加载和转换 (ELT) 方法，可与全部位于 Azure 中的暂存数据集结合使用。 目前，以下数据集可用于源转换。

| 连接器 | 格式 | 数据集/内联 |
| --------- | ------ | -------------- |
| [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br>[增量](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[常见数据模型](format-common-data-model.md#sink-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br>[增量](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/✓ |
| [Azure SQL 托管实例](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |
| [SQL Server](connector-sql-server.md) | | ✓/✓ |

特定于这些连接器的设置位于“设置”选项卡上。有关这些设置的信息和数据流脚本示例位于连接器文档中。

服务可以访问超过 [90 个原生连接器](connector-overview.md)。 若要将数据从数据流写入其他源，请使用复制活动从受支持的接收器加载该数据。

## <a name="sink-settings"></a>接收器设置

添加接收器后，请通过“接收器”选项卡进行配置。可在此处选择或创建接收器所写入的数据集。 可在[调试设置](concepts-data-flow-debug-mode.md)中配置数据集参数的开发值。 （必须打开调试模式。）

以下视频介绍了文本分隔文件类型的多个不同的接收器选项。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![显示“接收器”设置的屏幕截图。](media/data-flow/sink-settings.png "显示“接收器”设置的屏幕截图。")

架构偏差：[架构偏差](concepts-data-flow-schema-drift.md)是指在无需显式定义列更改的情况下，服务在数据流中以原生方式处理灵活架构的能力。 启用“允许架构偏差”在接收器数据架构中定义的内容之上写入额外的列。

**验证架构**：如果选择了“验证架构”，但在源投影中未找到传入源架构的任何列，或者数据类型不匹配，则数据流将失败。 使用此设置可强制要求源数据满足定义的投影的协定。 非常适用于在数据库源方案中指示列名称或类型已发生更改。

## <a name="cache-sink"></a>缓存接收器

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

缓存接收器是指数据流将数据写入 Spark 缓存，而不是数据存储。 在映射数据流中，可以使用缓存查找在同一流中多次引用此数据。 想要引用数据作为表达式的一部分，但又不想显式地将列联接到其中时，这很有用。 缓存接收器的常见示例：查找数据存储中的最大值，将错误代码与错误消息数据库进行匹配。 

若要写入缓存接收器，请添加接收器转换，然后选择“缓存”作为接收器类型。 与其他接收器类型不同，由于不写入外部存储，因此无需选择数据集或链接服务。 

![选择缓存接收器](media/data-flow/select-cache-sink.png "选择缓存接收器")

在接收器设置中，可以根据需要指定缓存接收器的键列。 在缓存查找中使用 `lookup()` 函数时，这些用作匹配条件。 如果指定键列，则不能在缓存查找中使用 `outputs()` 函数。 若要了解有关缓存查找语法的详细信息，请参阅[缓存查找](concepts-data-flow-expression-builder.md#cached-lookup)。

![缓存接收器键列](media/data-flow/cache-sink-key-columns.png "缓存接收器键列")

例如，如果在名为 `cacheExample` 的缓存接收器中指定一个键列 `column1`，则调用 `cacheExample#lookup()` 时将有一个参数指定匹配缓存接收器中的哪一行。 函数输出一个复杂列，其中每个映射的列都有子列。

> [!NOTE]
> 缓存接收器必须位于数据流中，且该数据流必须完全独立于通过缓存查找对其进行引用的任意转换。 缓存接收器还必须是第一个写入的接收器。 

写入活动输出：缓存接收器可以选择将输出数据写入到下一个管道活动的输入。 这样便可以快速轻松地将数据传入数据流活动，而无需将数据保存在数据存储中。

## <a name="field-mapping"></a>字段映射

与选择转换类似，可以在接收器的“映射”选项卡上决定要写入哪些传入列。 默认情况下，将映射所有输入列，包括偏移列。 此行为称为自动映射。

关闭自动映射时，可以添加基于固定列的映射或基于规则的映射。 使用基于规则的映射，可以编写具有模式匹配的表达式。 固定的映射会映射逻辑和物理列名称。 有关基于规则的映射的详细信息，请参阅[映射数据流中的列模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自定义接收器排序

默认情况下，数据以不确定的顺序写入多个接收器。 转换逻辑完成时，执行引擎并行写入数据，并且接收器排序可能会在每次运行时发生变化。 若要指定确切的接收器排序，请在数据流的“常规”选项卡上启用“自定义接收器排序” 。 启用后，接收器按递增顺序写入。

![显示自定义接收器排序的屏幕截图。](media/data-flow/custom-sink-ordering.png "显示自定义接收器排序的屏幕截图。")

> [!NOTE]
> 使用[缓存查询](./concepts-data-flow-expression-builder.md#cached-lookup)时，请确保接收器排序已将缓存接收器设置为 1，即排序中最低的一个（或第一个）。

![自定义接收器排序](media/data-flow/cache-2.png "自定义接收器排序")

### <a name="sink-groups"></a>接收器组

可以对一系列接收器使用同一顺序编号来将接收器组合在一起。 服务会将这些接收器视为可并行执行的组。 管道数据流活动中将显示并行执行的选项。

## <a name="error-row-handling"></a>行处理时出错

写入数据库时，某些数据行可能会由于目标设置的约束而失败。 默认情况下，遇到第一个错误时，数据流运行将失败。 在某些连接器中，可以选择“出错时继续运行”，确保即使单个行存在错误，也可以完成数据流。 目前，此功能仅在 Azure SQL 数据库和 Azure Synapse 中可用。 有关详细信息，请参阅 [Azure SQL DB 中的错误行处理](connector-azure-sql-database.md#error-row-handling)。

下面是有关如何在接收器转换中自动使用数据库错误行处理的视频教程。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>接收器中的数据预览

在调试群集中获取数据预览时，不会将任何数据写入接收器。 将返回数据的快照，但不会将任何内容写入目标。 要测试将数据写入接收器，请从管道画布运行管道调试。

## <a name="data-flow-script"></a>数据流脚本

### <a name="example"></a>示例

下面是接收器转换及其数据流脚本的一个示例：

```
sink(input(
        movie as integer,
        title as string,
        genres as string,
        year as integer,
        Rating as integer
    ),
    allowSchemaDrift: true,
    validateSchema: false,
    deletable:false,
    insertable:false,
    updateable:true,
    upsertable:false,
    keys:['movie'],
    format: 'table',
    skipDuplicateMapInputs: true,
    skipDuplicateMapOutputs: true,
    saveOrder: 1,
    errorHandlingOption: 'stopOnFirstError') ~> sink1
```

## <a name="next-steps"></a>后续步骤

创建数据流后，请[将数据流活动添加到管道](concepts-data-flow-overview.md)。
