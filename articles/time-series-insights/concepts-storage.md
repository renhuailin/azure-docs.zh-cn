---
title: 存储概述 - Azure 时序见解第 2 代 | Microsoft Docs
description: 了解 Azure 时序见解第 2 代中的数据存储。
author: tedvilutis
ms.author: tvilutis
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 9c0bbf9224f8864428d46e38487f614e0c3f61f0
ms.sourcegitcommit: 5da0bf89a039290326033f2aff26249bcac1fe17
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/10/2021
ms.locfileid: "109715225"
---
# <a name="data-storage"></a>数据存储

本文介绍 Azure 时序见解第 2 代中的数据存储。 它涵盖了暖存储和冷存储、数据可用性和最佳做法。

## <a name="provisioning"></a>设置

创建 Azure 时序见解第 2 代环境时，可以使用以下选项：

* 冷数据存储：
  * 在为环境选择的订阅和区域中创建新的 Azure 存储资源。
  * 附加预先存在的 Azure 存储帐户。 此选项仅在从 Azure 资源管理器[模板](/azure/templates/microsoft.timeseriesinsights/allversions)部署时可用，在 Azure 门户中不可见。
* 暖数据存储：
  * 暖存储是可选项，可在预配期间或之后启用或禁用。 如果你决定稍后启用暖存储并且冷存储中已有数据，请参阅下面的[此部分](concepts-storage.md#warm-store-behavior)，了解预期行为。 暖存储数据保留时间可以配置为 7 到 31 天，也可以根据需要进行调整。

引入一个事件时，会在暖存储（如果启用）和冷存储中为其建立索引。

[![存储概述](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> 冷存储数据所在 Azure Blob 存储帐户的所有者对该帐户中的所有数据拥有完全访问权限。 此访问权限包括“写入”和“删除”权限。 请不要编辑或删除 Azure 时序见解第 2 代写入的数据，否则可能导致数据丢失。

## <a name="data-availability"></a>数据可用性

为了实现最佳查询性能，Azure 时序见解第 2 代会将数据分区并为其编制索引。 为数据编制索引后，就可以从暖存储（如果已启用）和冷存储中查询数据。 所引入的数据量和每个分区的吞吐率会影响可用性。 请查看事件源[吞吐量限制](./concepts-streaming-ingress-throughput-limits.md)和[最佳做法](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices)，以获取最佳性能。 你还可以配置一个当环境在处理数据的过程中遇到问题时要发送的延迟[警报](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts)。

> [!IMPORTANT]
> 最长可能需要经过 60 秒之后，才可以通过[时序查询 API](./concepts-query-overview.md) 获得数据。 如果出现超过 60 秒的明显延迟，请通过 Azure 门户提交支持票证。
> 
> 最长可能需要经过 5 分钟之后，在 Azure 时序见解第 2 代外部直接访问 Parquet 文件时才可以获得数据。 有关详细信息，请参阅 [Parquet 文件格式](#parquet-file-format-and-folder-structure)部分。

## <a name="warm-store"></a>暖存储

只能通过[时序查询 API](./concepts-query-overview.md) 和 [Azure 时序见解 TSI Explorer](./concepts-ux-panels.md) 或 [Power BI 连接器](./how-to-connect-power-bi.md)使用暖存储中的数据。 暖存储查询免费且没有配额，但有一个 [30 个并发请求的限制](/rest/api/time-series-insights/reference-api-limits#query-apis---limits)。

### <a name="warm-store-behavior"></a>暖存储行为

* 启用后，流式传输到你的环境的所有数据都将路由到你的暖存储，而不考虑事件时间戳。 请注意，流式引入管道专为近实时流式处理生成，[不支持](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)引入历史事件。
* 保留期将基于事件在暖存储中建立索引的时间（而不是事件时间戳）进行计算。 这意味着，在保留期过后，即使事件时间戳适用于将来，数据在暖存储中也不再可用。
  * 示例：将包含 10 天天气预报的事件引入一个配置了 7 天保持期的暖存储容器中，并为该事件编制索引。 7 天后，将再也不能在暖存储中访问预测，但可以从冷存储中查询它。
* 请注意，如果在现有环境中启用了暖存储，而该环境已在冷存储中将最近的数据编制索引，则不会使用此数据回填暖存储。
* 如果你刚启用了暖存储，并且在 Explorer 中查看最近的数据时遇到问题，可以暂时关闭暖存储查询：

   [![禁用暖查询](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>冷存储

此部分介绍与 Azure 时序见解第 2 代相关的 Azure 存储详细信息。

有关 Azure Blob 存储的全面介绍，请阅读[存储 Blob 简介](../storage/blobs/storage-blobs-introduction.md)。

### <a name="your-cold-storage-account"></a>冷存储帐户

Azure 时序见解第 2 代在 Azure 存储帐户中为每个事件保留最多两个副本。 一个副本存储按引入时间排序的事件，始终允许按时序访问事件。 随着时间的推移，Azure 时序见解第 2 代还会创建数据的重新分区的副本，通过优化实现高性能查询。

你的所有数据无限期存储在 Azure 存储帐户中。

> [!WARNING]
> 不要限制通过公共 Internet 访问时序见解使用的存储帐户，否则所需的连接将会断开。

#### <a name="writing-and-editing-blobs"></a>写入和编辑 blob

为了确保查询性能和数据可用性，请不要编辑或删除 Azure 时序见解第 2 代创建的任何 Blob。

#### <a name="accessing-cold-store-data"></a>访问冷存储数据

除了从 [Azure 时序见解资源管理器](./concepts-ux-panels.md)和[时序查询 API](./concepts-query-overview.md) 访问数据外，还可以直接从冷存储中存储的 Parquet 文件访问数据。 例如，可以在 Jupyter 笔记本中读取、转换和清理数据，然后使用它来训练同一 Spark 工作流中的 Azure 机器学习模型。

若要直接从 Azure 存储帐户访问数据，你需要具有用于存储 Azure 时序见解第 2 代数据的帐户的读取访问权限。 然后，可以根据 Parquet 文件的创建时间读取选定的数据，该文件位于下面的 [Parquet 文件格式](#parquet-file-format-and-folder-structure)部分所述的 `PT=Time` 文件夹中。  若要详细了解如何启用对存储帐户的读取访问权限，请参阅[管理对存储帐户资源的访问权限](../storage/blobs/anonymous-read-access-configure.md)。

#### <a name="data-deletion"></a>数据删除

不要删除你的 Azure 时序见解第 2 代文件。 请只在 Azure 时序见解第 2 代内部管理相关数据。

### <a name="parquet-file-format-and-folder-structure"></a>Parquet 文件格式和文件夹结构

Parquet 是一种开源的列式文件格式，旨在提高存储效率和性能。 Azure 时序见解第 2 代使用 Parquet 来大规模启用基于时序 ID 的查询的性能。

有关 Parquet 文件类型的详细信息，请参阅 [Parquet 文档](https://parquet.apache.org/documentation/latest/)。

Azure 时序见解第 2 代按如下方式存储数据的副本：

* `PT=Time` 文件夹按引入时间进行分区，并按到达顺序存储数据。 此数据会不断保留，你可以直接从 Azure 时序见解第 2 代外部（例如 Spark 笔记本）访问它。 时间戳 `<YYYYMMDDHHMMSSfff>` 对应于数据引入时间。 `<MinEventTimeStamp>` 和 `<MaxEventTimeStamp>` 对应于文件中包含的事件时间戳范围。 路径和文件名的格式为：

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<BlobCreationTimestamp>_<MinEventTimestamp>_<MaxEventTimestamp>_<TsiInternalSuffix>.parquet`

* `PT=Live` 和 `PT=Tsid` 文件夹包含数据的另一个副本，并且已大规模重新分区以提高时序查询的性能。 此数据会不断优化，因此不是静态的。 在重新分区期间，某些事件可能会出现在多个 Blob 中，并且 Blob 名称可能会更改。  这些文件夹由 Azure 时序见解第 2 代使用，不应直接访问它们；只能出于这种目的使用 `PT=Time`。

> [!NOTE]
>
> `PT=Time` 文件夹中包含的 2021 年 6 月之前的数据可能采用文件名格式，但不包含事件时间范围：`V=1/PT=Time/Y=<YYYY>/M=<MM>/<BlobCreationTimestamp>_<TsiInternalSuffix>.parquet`。  内部文件格式相同，使用这两种命名方案的文件可以一起使用。 
>
> * `<YYYY>` 映射到 4 位数的年份表示形式。
> * `<MM>` 映射到两位数的月份表示形式。
> * 时间戳的 `<YYYYMMDDHHMMSSfff>` 格式映射到四位数年份 (`YYYY`)、两位数月份 (`MM`)、两位数日 (`DD`)、两位数小时 (`HH`)、两位数分钟 (`MM`)、两位数秒 (`SS`) 和三位数毫秒 (`fff`)。

Azure 时序见解第 2 代事件映射到 Parquet 文件内容，如下所示：

* 每个事件映射到单个行。
* 每一行包含带有事件时间戳的 **时间戳** 列。 “时间戳”属性永远不会为 null。 如果未在事件源中指定时间戳属性，该属性默认为 **事件排队时间**。 存储的时间戳始终为 UTC 时间。
* 每一行包含创建 Azure 时序见解第 2 代环境时定义的时序 ID (TSID) 列。 TSID 属性名称包含 `_string` 后缀。
* 作为遥测数据发送的所有其他属性映射到以 `_bool`（布尔值）、`_datetime`（时间戳）、`_long` (long)、`_double` (double)、`_string`（字符串）或 `_dynamic` (dynamic) 结尾的列名称，具体取决于属性类型。  有关详细信息，请阅读[支持的数据类型](./concepts-supported-data-types.md)。
* 此映射架构适用于文件格式的第一个版本，作为 V=1 引用并存储在具有相同名称的基文件夹中。 随着此功能的演变，此映射架构可能会更改，引用名称的数字会递增。

## <a name="next-steps"></a>后续步骤

* 阅读[数据建模](./concepts-model-overview.md)。

* 规划 [Azure 时序见解第 2 代环境](./how-to-plan-your-environment.md)。
