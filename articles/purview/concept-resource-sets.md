---
title: 了解资源集
description: 本文介绍了哪些资源集以及 Azure 监控范围如何创建它们。
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: e4b48729f13ec0234a7a711032a2db34e55a8bd1
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 02/04/2021
ms.locfileid: "99539461"
---
# <a name="understanding-resource-sets"></a>了解资源集

本文可帮助你了解 Azure 监控范围如何使用资源集将数据资产映射到逻辑资源。
## <a name="background-info"></a>背景信息

大规模数据处理系统通常将单个表存储在磁盘上作为多个文件。 此概念在 Azure 监控范围中使用资源集来表示。 资源集是目录中的单个对象，用于表示存储中的大量资产。

例如，假设 Spark 群集已将数据帧保存到 Azure DataL Lake Storage (ADLS) Gen2 数据源。 尽管在 Spark 中，表看起来就像单个逻辑资源，但在磁盘上可能有数千个 Parquet 文件，其中每个文件都代表数据帧内容总数的分区。 IoT 数据和 web 日志数据具有相同的挑战。 假设有一个传感器多次输出日志文件。 只有从单个传感器获得几百个日志文件，才会花费很长时间。

为了解决将大量数据资产映射到单个逻辑资源的挑战，Azure 监控范围使用资源集。

## <a name="how-azure-purview-detects-resource-sets"></a>Azure 监控范围如何检测资源集

Azure 监控范围支持检测 Azure Blob 存储中的资源集、ADLS Gen1 和 ADLS Gen2。

在扫描时，Azure 监控范围会自动检测资源集。 此功能查看通过扫描引入的所有数据并将其与一组定义的模式进行比较。

例如，假设你扫描其 URL 为的数据源 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` 。 Azure 监控范围查看路径段，并确定它们是否匹配任何内置模式。 它包含 Guid、数字、日期格式、本地化代码 (的内置模式，例如 en-us) 等。 在这种情况下，数字模式匹配 *23*。 Azure 监控范围假定此文件是名为的资源集的一部分 `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` 。

或者，对于类似的 URL， `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` Azure 监控范围同时匹配本地化模式和数字模式，同时生成一个名为的资源集 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` 。

使用此策略时，Azure 监控范围会将以下资源映射到相同的资源集 `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` ：

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Azure 监控范围不会检测为资源集的文件类型

监控范围有意不要尝试将 Word、Excel 或 PDF 等大多数文档文件类型分类为资源集。 异常是 CSV 格式，因为这是一种常见的分区文件格式。

## <a name="how-azure-purview-scans-resource-sets"></a>Azure 监控范围如何扫描资源集

当 Azure 监控范围检测到其认为是资源集的一部分的资源时，它会从完全扫描切换到示例扫描。 在示例扫描中，它仅打开其认为位于资源集中的文件的子集。 对于它打开的每个文件，它使用其架构并运行其分类器。 然后，Azure 监控范围在打开的资源中找到最新资源，并在目录中的整个资源集的条目中使用该资源的架构和分类。

## <a name="what-azure-purview-stores-about-resource-sets"></a>Azure 监控范围存储的有关资源集的内容

除了单个架构和分类以外，Azure 监控范围还存储有关资源集的以下信息：

- 从其深入扫描的最新分区资源中的数据。
- 聚合有关组成资源集的分区资源的信息。
- 显示其找到的分区资源数的分区计数。
- 一个架构计数，显示它在执行深度扫描的示例集中找到了多少个唯一架构。 此值为介于1到5之间的数字，或大于5，5 + 的值。
- 当资源集中包含多个分区类型时，分区类型的列表。 例如，IoT 传感器可能输出 XML 和 JSON 文件，尽管这两个文件在逻辑上都是同一资源集的一部分。

## <a name="built-in-resource-set-patterns"></a>内置资源集模式

Azure 监控范围支持以下资源集模式。 这些模式可以作为名称出现在目录中，也可以作为文件名称的一部分出现。
### <a name="regex-based-patterns"></a>基于 Regex 的模式

| 模式名称 | 显示名称 | 说明 |
|--------------|--------------|-------------|
| Guid         | GUID.EMPTY       | [RFC 4122](https://tools.ietf.org/html/rfc4122)中定义的全局唯一标识符 |
| 数字       | 北          | 一个或多个数字 |
| 日期/时间格式 | 年月底那北     | 我们支持各种日期/时间格式，但所有数据都用 {Year} [分隔符] {Month} [分隔符] {Day} 或序列 {N} 秒表示。 |
| 4ByteHex     | 表示        | 四位十六进制数。 |
| 本地化 | LOC        | 支持在 [BCP 47](https://tools.ietf.org/html/bcp47)中定义的语言标记， (例如 en_ca 和 en-ca，)  |

### <a name="complex-patterns"></a>复杂模式

| 模式名称 | 显示名称 | 说明 |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark 分区文件标识符 |
| 日期 (yyyy/mm/dd) InPath  | {Year}/{Month}/{Day} | 跨多个文件夹的年/月/日模式 |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>资源集在 Azure 监控范围目录中的显示方式

当 Azure 监控范围将一组资产与资源集相匹配时，它会尝试提取最有用的信息以用作目录中的显示名称。 应用默认命名约定的一些示例： 

### <a name="example-1"></a>示例 1

限定名： https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}

显示名称： "spark output name"

### <a name="example-2"></a>示例 2

限定名： https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}

显示名称： "我的已分区数据"

### <a name="example-3"></a>示例 3

限定名： https://myblob.blob.core.windows.net/sample-data/data{N}.csv

显示名称： "数据"

## <a name="known-issues-with-resource-sets"></a>资源集的已知问题

尽管资源集在大多数情况下非常有效，但你可能会遇到以下问题： Azure 监控范围：

- 错误地将资产标记为资源集
- 将资产置于错误的资源集中
- 错误地将资产标记为不是资源集

## <a name="next-steps"></a>后续步骤

若要开始使用 Azure 监控范围，请参阅 [快速入门：创建 Azure 监控范围帐户](create-catalog-portal.md)。
