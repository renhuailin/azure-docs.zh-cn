---
title: 映射数据流中的源转换
titleSuffix: Azure Data Factory & Azure Synapse
description: 了解如何在 Azure 数据工厂或 Azure Synapse Analytics 管道的映射数据流中设置源转换。
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.subservice: data-flows
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/27/2021
ms.openlocfilehash: ef2db7e11666c104215b29f298882cfe77631310
ms.sourcegitcommit: 7f3ed8b29e63dbe7065afa8597347887a3b866b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122015538"
---
# <a name="source-transformation-in-mapping-data-flow"></a>映射数据流中的源转换

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

源转换为数据流配置数据源。 设计数据流时，第一步始终是配置源转换。 若要添加源，请在数据流画布中选择“添加源”框。

每个数据流至少需要一个源转换，但你可根据需要添加任意多个源来完成数据转换。 可以使用联接、查找或联合转换将这些源联接到一起。

每个源转换只与一个数据集或链接服务相关联。 数据集定义要写入或读取的数据的形状和位置。 如果使用基于文件的数据集，则可在源中使用通配符和文件列表，一次性处理多个文件。

## <a name="inline-datasets"></a>内联数据集

创建源转换时所做的第一次决策是源信息是在数据集对象内定义还是在源转换内定义。 大多数格式只适用于其中之一。 若要了解如何使用特定连接器，请参阅相应的连接器文档。

内联和数据集对象同时支持某一格式时，两者都有优势。 数据集对象是可重用的实体，可在其他数据流和复制等活动中使用。 使用强化的架构时，这些可重复使用的实体特别有用。 数据集不基于 Spark。 有时，可能需要在源转换中替代某些设置或架构投影。

如果使用灵活的架构、一次性源实例或参数化源，建议使用内联数据集。 如果源的参数化程度非常高，则使用内联数据集可以不创建“虚拟”对象。 内联数据集基于 Spark，其属性是数据流中固有的。

若要使用内联数据集，请在“源类型”选择器中选择所需的格式。 选择要与其连接的链接服务，而不是选择源数据集。

![显示已选择“内联”的屏幕截图。](media/data-flow/inline-selector.png "显示已选择“内联”的屏幕截图。")

## <a name="workspace-db-synapse-workspaces-only"></a>工作区 DB（仅 Synapse 工作区）

在 Azure Synapse 工作区的名为“```Workspace DB```”的数据流源转换中存在一个附加选项。 因此，你可以直接选取任何可用类型的工作区数据库作为源数据，无需额外的链接服务或数据集。

> [!NOTE]
> Azure Synapse 工作区 DB 连接器目前为公共预览版，只能与 Spark Lake 数据库配合使用

![显示选定工作区 DB 的屏幕截图。](media/data-flow/syms-source.png "显示选定工作区 DB 的屏幕截图。")

##  <a name="supported-source-types"></a><a name="supported-sources"></a>受支持的源类型

映射数据流遵循提取、加载和转换 (ELT) 方法，可与全部位于 Azure 中的过渡数据集结合使用。 目前，以下数据集可用于源转换。

| 连接器 | 格式 | 数据集/内联 |
| --------- | ------ | -------------- |
| [Azure Blob 存储](connector-azure-blob-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)<br>[增量](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties) <br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓ |
| [Azure Cosmos DB (SQL API)](connector-azure-cosmos-db.md#mapping-data-flow-properties) | | ✓/- |
| [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓<br/>✓/✓<br>✓/✓ |
| [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md#mapping-data-flow-properties) | [Avro](format-avro.md#mapping-data-flow-properties)<br>[常见数据模型](format-common-data-model.md#source-properties)<br>[带分隔符的文本](format-delimited-text.md#mapping-data-flow-properties)<br>[增量](format-delta.md)<br>[Excel](format-excel.md#mapping-data-flow-properties)<br>[JSON](format-json.md#mapping-data-flow-properties)<br>[ORC](format-orc.md#mapping-data-flow-properties)<br/>[Parquet](format-parquet.md#mapping-data-flow-properties)<br>[XML](format-xml.md#mapping-data-flow-properties) | ✓/✓<br/>-/✓<br>✓/✓<br>✓/✓<br>✓/✓<br>✓/✓<br/>✓/✓<br/>✓/✓<br>✓/✓ |
| [Azure Database for MySQL](connector-azure-database-for-mysql.md) |  | ✓/✓ |
| [Azure Database for PostgreSQL](connector-azure-database-for-postgresql.md) |  | ✓/✓ |
| [Azure SQL 数据库](connector-azure-sql-database.md#mapping-data-flow-properties) | | ✓/✓ |
| [Azure SQL 托管实例](connector-azure-sql-managed-instance.md#mapping-data-flow-properties) | | ✓/✓ |
| [Azure Synapse Analytics](connector-azure-sql-data-warehouse.md#mapping-data-flow-properties) | | ✓/✓ |
| [Hive](connector-hive.md#mapping-data-flow-properties) | | -/✓ |
| [Snowflake](connector-snowflake.md) | | ✓/✓ |
| [SQL Server](connector-sql-server.md) | | ✓/✓ |

特定于这些连接器的设置位于“源选项”选项卡上。有关这些设置的信息和数据流脚本示例位于连接器文档中。

Azure 数据工厂和 Synapse 管道可以访问超过 [90 个原生连接器](connector-overview.md)。 若要将其他这些源中的数据包含到你自己的数据流中，请使用复制活动将该数据加载到其中一个受支持的暂存区域。

## <a name="source-settings"></a>源设置

添加源后，通过“源设置”选项卡进行配置。可在此处选取或创建源指向的数据集。 还可选择数据的架构和采样选项。

可在[调试设置](concepts-data-flow-debug-mode.md)中配置数据集参数的开发值。 （必须打开调试模式。）

![显示“源设置”选项卡的屏幕截图。](media/data-flow/source1.png "显示“源设置”选项卡的屏幕截图。")

**输出流名称**：源转换的名称。

**源类型**：选择是要使用内联数据集还是现有数据集对象。

**测试连接**：测试数据流的 Spark 服务是否可以成功连接到源数据集中使用的链接服务。 要启用此功能，必须打开调试模式。

架构偏差：[架构偏差](concepts-data-flow-schema-drift.md)是指在无需显式定义列更改的情况下，服务在数据流中以原生方式处理灵活架构的能力。

* 如果源列经常更改，请选择“允许架构偏差”复选框。 此设置可让所有传入的源字段通过转换流向接收器。

* 选择“推断偏移列类型”会指示服务检测并定义发现的每个新列的数据类型。 关闭此功能后，所有偏移列都将为字符串类型。

**验证架构：** 如果选择“验证架构”，那么如果传入的源数据与数据集定义的架构不匹配，数据流将无法运行。

**跳过行计数**：“跳过行计数”字段指定在数据集的开头要忽略多少行。

**采样**：启用“采样”来限制源中的行数。 当你测试源中的数据或对其进行采样以进行调试时，请使用此设置。 以调试模式从管道执行数据流时，这非常有用。

若要验证源的配置是否正确，请打开调试模式并提取数据预览。 有关详细信息，请参阅[调试模式](concepts-data-flow-debug-mode.md)。

> [!NOTE]
> 当调试模式处于打开状态时，调试设置中的行限制配置将覆盖数据预览期间源中的采样设置。

## <a name="source-options"></a>源选项

“源选项”选项卡包含特定于所选连接器和格式的设置。 有关详细信息和示例，请参阅相关的[连接器文档](#supported-sources)。

## <a name="projection"></a>投影

与数据集中的架构一样，源中的投影定义源数据中的数据列、类型和格式。 对于大多数数据集类型（例如 SQL 和 Parquet），源中的投影是固定的，用于反映数据集中定义的架构。 如果你的源文件不是强类型（例如，平面 .csv 文件而不是 Parquet 文件），则可在源转换中为每个字段定义数据类型。

![显示“投影”选项卡上的设置的屏幕截图。](media/data-flow/source3.png "显示“投影”选项卡上的设置的屏幕截图。")

如果文本文件没有定义的架构，请选择“检测数据类型”，以便服务采样并推断数据类型。 选择“定义默认格式”以自动检测默认数据格式。

“重置架构”将投影重置为在引用的数据集中定义的投影。

可以在下游派生列转换中修改列数据类型。 使用选择转换来修改列名称。

### <a name="import-schema"></a>导入架构

选择“投影”选项卡上的“导入架构”按钮，以使用活动调试群集来创建架构投影 。 它在每个源类型中都可用。 在此处导入架构将替代数据集中定义的投影。 数据集对象不会更改。

导入架构在支持复杂数据结构的数据集（如 Avro 和 Azure Cosmos DB）中十分有用，这些数据集不需要架构定义存在于数据集中。 对于内联数据集，导入架构是唯一一种引用列元数据而不会发生架构偏差的方法。

## <a name="optimize-the-source-transformation"></a>优化源转换

“优化”选项卡允许在每个转换步骤编辑分区信息。 在大多数情况下，“使用当前分区”会针对源的理想分区结构进行优化。

如果要从 Azure SQL 数据库源读取数据，自定义源分区可能会以最快的速度读取数据。 服务会通过建立与数据库的并行连接来读取大型查询。 此源分区可在列上完成，也可使用查询来完成。

![显示“源”分区设置的屏幕截图。](media/data-flow/sourcepart3.png "显示“源”分区设置的屏幕截图。")

有关映射数据流中的优化的详细信息，请参阅[“优化”选项卡](concepts-data-flow-overview.md#optimize)。

## <a name="next-steps"></a>后续步骤

开始使用[派生列转换](data-flow-derived-column.md)和[选择转换](data-flow-select.md)来构建数据流。
