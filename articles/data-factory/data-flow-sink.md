---
title: 映射数据流中的接收器转换
description: 了解如何在映射数据流中配置接收器转换。
author: kromerm
ms.author: makromer
ms.reviewer: daperlov
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 12/08/2020
ms.openlocfilehash: 242249e3ab7fbedf9f19f3cb9a49fc4a8359f4ae
ms.sourcegitcommit: 1756a8a1485c290c46cc40bc869702b8c8454016
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96929338"
---
# <a name="sink-transformation-in-mapping-data-flow"></a>映射数据流中的接收器转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

转换完数据后，使用接收器转换将数据写入目标存储。 每个数据流都需要至少一个接收器转换，但你可以根据需要写入任意多个接收器来完成转换流。 若要写入其他接收器，请通过新的分支和条件拆分创建新的流。

每个接收器转换只与一个 Azure 数据工厂数据集对象或链接服务相关联。 接收器转换确定要写入的数据的形状和位置。

## <a name="inline-datasets"></a>内联数据集

创建接收器转换时，请选择是否在 dataset 对象内或接收器转换内定义接收器信息。 大多数格式仅可用于其中一种格式。 若要了解如何使用特定连接器，请参阅相应的连接器文档。

当 inline 和 dataset 对象同时支持格式时，这两种方法都有好处。 数据集对象是可重复使用的实体，可用于其他数据流和活动，例如 Copy。 使用强化的架构时，这些可重复使用的实体特别有用。 数据集不基于 Spark。 有时，可能需要在接收器转换中替代某些设置或架构投影。

如果使用灵活的架构、一次性接收器实例或参数化接收器，则建议使用内联数据集。 如果接收器的参数化很高，则内联数据集允许你不创建 "虚拟" 对象。 内联数据集基于 Spark，其属性是在本机上用于数据流。

若要使用内联数据集，请在 **接收器类型** 选择器中选择所需的格式。 选择要连接到的链接服务，而不是选择接收器数据集。

![显示选定的内联的屏幕截图。](media/data-flow/inline-selector.png "显示选定的内联的屏幕截图。")

##  <a name="supported-sink-types"></a><a name="supported-sinks"></a> 支持的接收器类型

映射数据流遵循 (ELT) 方法的提取、加载和转换，并且适用于所有 Azure 中的 *临时* 数据集。 目前，以下数据集可用于源转换。

| 连接器 | 格式 | 数据集/内联 |
| --------- | ------ | -------------- |
| [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>-/✓ <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>✓/- <br>✓/- <br>✓/✓<br>✓/- |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties) <br/>[常见数据模型](format-common-data-model.md#sink-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties) <br>[Delta](format-delta.md) <br>[JSON](format-json.md#mapping-data-flow-properties) <br/>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties) | ✓/- <br>-/✓ <br>✓/- <br>-/✓ <br>✓/-<br>✓/✓ <br>✓/- |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/- |
| [Azure SQL 托管实例 (预览版) ](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/- |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |

特定于这些连接器的设置位于 " **设置** " 选项卡上。有关这些设置的信息和数据流脚本示例位于连接器文档中。

Azure 数据工厂可以访问90多个 [本机连接器](connector-overview.md)。 若要将数据从数据流写入其他源，请使用复制活动从支持的接收器加载数据。

## <a name="sink-settings"></a>接收器设置

添加接收器后，通过 " **接收器** " 选项卡进行配置。可在此处选取或创建接收器写入的数据集。 数据集参数的开发值可以在 [调试设置](concepts-data-flow-debug-mode.md)中进行配置。 必须打开 (调试模式。 ) 

以下视频介绍了用于文本分隔文件类型的多个不同的接收器选项。

> [!VIDEO https://www.microsoft.com/videoplayer/embed/RE4tf7T]

![显示接收器设置的屏幕截图。](media/data-flow/sink-settings.png "显示接收器设置的屏幕截图。")

**架构偏差**： [架构偏差](concepts-data-flow-schema-drift.md) 是指在无需显式定义列更改的情况下，数据工厂以本机方式处理数据流中的灵活架构的能力。 启用 " **允许架构偏移** " 可以在接收器数据架构中定义的内容的基础上写入其他列。

**验证架构**：如果选择 "验证架构"，则在源投影中找不到传入源架构的任何列或数据类型不匹配时，数据流将失败。 使用此设置可强制源数据满足定义投影的协定。 这在数据库源方案中非常有用，用来指示列名称或类型已更改。

## <a name="cache-sink"></a>缓存接收器

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4HKt1]

当数据流将数据写入 Spark 缓存而不是数据存储区时， *缓存接收器* 。 在映射数据流时，可以使用 *缓存查找* 多次引用同一流中的这些数据。 当您希望将数据作为表达式的一部分进行引用，但不希望将这些列显式加入列时，这非常有用。 常见示例：缓存接收器可以帮助查找数据存储的最大值，并将错误代码与错误消息数据库匹配。 

若要写入缓存接收器，请添加接收器转换并选择 **缓存** 作为接收器类型。 与其他接收器类型不同，你不需要选择数据集或链接的服务，因为你不会写入外部存储区。 

![选择缓存接收器](media/data-flow/select-cache-sink.png "选择缓存接收器")

在接收器设置中，可以选择指定缓存接收器的键列。 当在缓存查找中使用函数时，这些将用作匹配条件 `lookup()` 。 如果指定键列，则不能 `outputs()` 在缓存查找中使用函数。 若要了解有关缓存查找语法的详细信息，请参阅 [缓存查找](concepts-data-flow-expression-builder.md#cached-lookup)。

![缓存接收器键列](media/data-flow/cache-sink-key-columns.png "缓存接收器键列")

例如，如果在名为的缓存接收器中指定的单个键列 `column1` `cacheExample` ，则调用 `cacheExample#lookup()` 将有一个参数指定要在缓存接收器中匹配哪一行。 函数将为每个映射的列输出一个包含个子列的复杂列。

> [!NOTE]
> 缓存接收器必须与通过缓存查找引用它的任何转换处于完全独立的数据流中。 缓存接收器还必须写入第一个接收器。 

## <a name="field-mapping"></a>字段映射

与选择转换类似，在接收器的 " **映射** " 选项卡上，您可以决定写入哪些传入列。 默认情况下，映射所有输入列，包括偏移列。 此行为称为 *automapping*。

关闭 automapping 时，可以添加基于列的固定映射或基于规则的映射。 通过基于规则的映射，可以编写具有模式匹配的表达式。 固定映射映射逻辑列名和物理列名。 有关基于规则的映射的详细信息，请参阅 [映射数据流中的列模式](concepts-data-flow-column-pattern.md#rule-based-mapping-in-select-and-sink)。

## <a name="custom-sink-ordering"></a>自定义接收器排序

默认情况下，数据按非确定性顺序写入多个接收器。 当转换逻辑完成时，执行引擎会并行写入数据，并且每次运行的接收器排序可能会有所不同。 若要指定确切的接收顺序，请在数据流的 "**常规**" 选项卡上启用 **自定义接收器排序**。 启用后，按顺序按递增顺序写入接收器。

![显示自定义接收器排序的屏幕截图。](media/data-flow/custom-sink-ordering.png "显示自定义接收器排序的屏幕截图。")

> [!NOTE]
> 使用 [缓存的查找](./concepts-data-flow-expression-builder.md#cached-lookup)时，请确保接收器排序将缓存接收器设置为1、最低 (或首次) 排序。

![自定义接收器排序](media/data-flow/cache-2.png "自定义接收器排序")

### <a name="sink-groups"></a>接收器组

可以通过对一系列接收器应用相同的序号，将接收器组合在一起。 ADF 会将这些接收器视为可并行执行的组。 并行执行的选项将显示在 "管道数据流" 活动中。

## <a name="error-row-handling"></a>行处理时出错

写入数据库时，某些数据行可能因目标设置的约束而失败。 默认情况下，数据流运行在它获取的第一个错误时将失败。 在某些连接器中，可以选择 **"出错时继续"** ，即使单个行出现错误，也可以使数据流完成。 目前，此功能仅适用于 Azure SQL 数据库。 有关详细信息，请参阅 [AZURE SQL DB 中的错误行处理](connector-azure-sql-database.md#error-row-handling)。

下面是有关如何在接收器转换中自动使用数据库错误行处理的视频教程。

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4IWne]

## <a name="data-preview-in-sink"></a>接收器中的数据预览

在调试群集上提取数据预览时，不会将任何数据写入接收器。 将返回数据的外观的快照，但不会向目标写入任何内容。 若要测试将数据写入接收器，请从管道画布运行管道调试。

## <a name="next-steps"></a>后续步骤

创建数据流后，请将数据流 [活动添加到管道](concepts-data-flow-overview.md)。