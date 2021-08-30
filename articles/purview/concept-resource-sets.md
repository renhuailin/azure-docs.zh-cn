---
title: 了解资源集
description: 本文介绍什么是资源集，以及 Azure Purview 如何创建资源集。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 07/23/2021
ms.openlocfilehash: 6c51a118b0581759f456b243b6dde25890b36f39
ms.sourcegitcommit: d9a2b122a6fb7c406e19e2af30a47643122c04da
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/24/2021
ms.locfileid: "114668469"
---
# <a name="understanding-resource-sets"></a>了解资源集

本文帮助你了解 Azure Purview 如何使用资源集将数据资产映射到逻辑资源。
## <a name="background-info"></a>背景信息

大规模数据处理系统通常将单个表作为多个文件存储在磁盘上。 此概念在 Azure Purview 中使用资源集来表示。 资源集是目录中的单个对象，表示存储中的大量资产。

例如，假设 Spark 群集在 Azure Data Lake Storage (ADLS) Gen2 数据源中持久保存某个数据帧。 虽然 Spark 中的表看似是单个逻辑资源，但在磁盘上可能有数千个 Parquet 文件，其中每个文件代表数据帧总体内容的一个分区。 IoT 数据和 Web 日志数据也存在相同的难题。 假设某个传感器每秒会多次输出日志文件。 不久之后，这一个传感器就会输出几十万个日志文件。

为了解决将大量数据资产映射到单个逻辑资源的难题，Azure Purview 使用了资源集。

## <a name="how-azure-purview-detects-resource-sets"></a>Azure Purview 如何检测资源集

Azure Purview 支持检测 Azure Blob 存储、ADLS Gen1、ADLS Gen2、Azure 文件存储和 Amazon S3 中的资源集。

扫描时，Azure Purview 会自动检测资源集。 此功能通过扫描查看引入的所有数据，并将其与一组定义的模式进行比较。

例如，假设你在扫描一个 URL 为 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` 的数据源。 Azure Purview 将查看路径段，并确定它们是否与任何内置模式相匹配。 Azure Purview 包含针对 GUID、数字、日期格式、本地化代码（例如 en-us）等的内置模式。 在本例中，数字模式匹配 *23*。 Azure Purview 假设此文件是名为 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` 的资源集的一部分。

或者，对于类似于 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` 的 URL，Azure Purview 将匹配本地化模式和数字模式，并生成名为 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` 的资源集。

Azure Purview 使用此策略将以下资源映射到同一资源集 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json`：

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure Purview 不会检测为资源集的文件类型

Purview 有意地不会尝试将 Word、Excel 或 PDF 等大多数文档文件类型分类为资源集。 例外的格式是 CSV，因为它是常用的分区文件格式。

## <a name="how-azure-purview-scans-resource-sets"></a>Azure Purview 如何扫描资源集

当 Azure Purview 检测到它认为是资源集一部分的资源时，将从完全扫描切换到样本扫描。 在样本扫描中，它只会打开它认为位于资源集中的文件子集。 对于打开的每个文件，它将使用该文件的架构并运行其分类器。 然后，Azure Purview 在打开的资源中查找最新的资源，并在目录中整个资源集的条目中使用该资源的架构和分类。

## <a name="what-azure-purview-stores-about-resource-sets"></a>Azure Purview 存储有关资源集的哪些内容

除了单个架构和分类以外，Azure Purview 还会存储有关资源集的以下信息：

- 它深度扫描的最新分区资源中的数据。
- 有关构成资源集的分区资源的聚合信息。
- 分区计数，显示找到的分区资源数。
- 架构计数，显示它在深度扫描的样本集中找到的唯一架构数。 此值要么是介于 1 和 5 之间的数字，要么是大于 5 的值。
- 当资源集中包含多个分区类型时的分区类型列表。 例如，IoT 传感器可能会输出 XML 和 JSON 文件，不过，这两种文件类型在逻辑上都是同一个资源集的一部分。

## <a name="built-in-resource-set-patterns"></a>内置资源集模式

Azure Purview 支持以下资源集模式。 这些模式可能显示为目录中的某个名称，也可能显示为文件名的一部分。
### <a name="regex-based-patterns"></a>基于正则表达式的模式

| 模式名称 | 显示名称 | 说明 |
|--------------|--------------|-------------|
| Guid         | {GUID}       | [RFC 4122](https://tools.ietf.org/html/rfc4122) 中定义的全局唯一标识符 |
| Number       | {N}          | 一个或多个数字 |
| 日期/时间格式 | {Year}{Month}{Day}{N}     | 我们支持各种日期/时间格式，但所有格式采用 {Year}[delimiter]{Month}[delimiter]{Day} 或 {N} 系列表示形式。 |
| 4ByteHex     | {HEX}        | 一个 4 位数的十六进制数。 |
| 本地化 | {LOC}        | [BCP 47](https://tools.ietf.org/html/bcp47) 中定义的语言标记，- 和 _ 名称均受支持（例如 en_ca 和 en-ca） |

### <a name="complex-patterns"></a>复杂模式

| 模式名称 | 显示名称 | 说明 |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark 分区文件标识符 |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | 跨多个文件夹的年/月/日模式 |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>资源集在 Azure Purview 目录中的显示方式

当 Azure Purview 在资源集中匹配一组资产时，它会尝试提取最有用的信息用作目录中的显示名称。 应用默认命名约定的一些示例： 

### <a name="example-1"></a>示例 1

限定名称：`https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

显示名称：“spark output name”

### <a name="example-2"></a>示例 2

限定名称：`https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

显示名称：“my partitioned data”

### <a name="example-3"></a>示例 3

限定名称：`https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

显示名称：“data”

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>使用模式规则自定义资源集分组

扫描存储帐户时，Azure Purview 使用一组定义的模式来确定一组资产是否为资源集。 在某些情况下，Azure Purview 的资源集分组可能无法准确反映数据资产。 这些问题可能包括：

- 错误地将资产标记为资源集
- 将资产放入错误的资源集
- 错误地将资产标记为不是资源集

若要自定义或替代 Azure Purview 检测哪些资源可分组为资源集的方式，以及其在目录中的显示方式，你可以在管理中心定义模式规则。 有关分步说明和语法，请参阅[资源集模式规则](how-to-resource-set-pattern-rules.md)。
## <a name="next-steps"></a>后续步骤

若要开始使用 Azure Purview，请参阅[快速入门：创建 Azure Purview 帐户](create-catalog-portal.md)。
