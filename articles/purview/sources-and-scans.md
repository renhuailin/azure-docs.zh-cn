---
title: 支持的数据源和文件类型
description: 本文提供有关 Purview 中支持的数据源和文件类型的概念性详细信息。
author: viseshag
ms.author: viseshag
ms.service: purview
ms.subservice: purview-data-map
ms.topic: conceptual
ms.date: 09/27/2021
ms.custom: references_regions
ms.openlocfilehash: 1d484bce594d5894052ffeb4f1ff4a1dbf202d50
ms.sourcegitcommit: e8c34354266d00e85364cf07e1e39600f7eb71cd
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/29/2021
ms.locfileid: "129215237"
---
# <a name="supported-data-sources-and-file-types-in-azure-purview"></a>Azure Purview 中支持的数据源和文件类型

本文讨论 Purview 中支持的数据源、文件类型和扫描概念。

## <a name="supported-data-sources"></a>支持的数据源

Purview 支持[此处](purview-connector-overview.md)列出的所有数据源。

## <a name="file-types-supported-for-scanning"></a>支持扫描的文件类型

以下文件类型支持扫描，以便进行架构提取和分类（如果适用）：

- 扩展名支持的结构化文件格式：AVRO、ORC、PARQUET、CSV、JSON、PSV、SSV、TSV、TXT、XML、GZIP
- 扩展名支持的文档文件格式：DOC、DOCM、DOCX、DOT、ODP、ODS、ODT、PDF、POT、PPS、PPSX、PPT、PPTM、PPTX、XLC、XLS、XLSB、XLSM、XLSX、XLT
- Purview 还支持自定义文件扩展名和自定义分析程序。
 
> [!Note]
> 每个 Gzip 文件必须映射到其中的单个 csv 文件。 Gzip 文件受系统和自定义分类规则的限制。 我们目前不支持扫描映射到其中的多个文件的 gzip 文件，或者除 csv 以外的任何文件类型。 此外，Purview 扫描程序支持为架构提取和分类扫描对齐压缩的 PARQUET 文件类型。 

> [!Note]
> Purview 扫描程序不支持扫描 AVRO、ORC 和 PARQUET 文件类型中的复杂数据类型（例如 MAP、LIST、STRUCT）进行架构提取。   

## <a name="sampling-within-a-file"></a>在文件中采样

在 Purview 术语中，
- L1 扫描：提取基本信息和元数据，例如文件名、大小和完全限定的名称
- L2 扫描：提取结构化文件类型和数据库表的架构
- L3 扫描：在适用情况下提取架构，并使采样的文件遵循系统和自定义分类规则

对于所有结构化文件格式，Purview 扫描程序按以下方式对文件进行采样：

- 对于结构化文件类型，它抽取每个列中前 128 行的内容或前 1 MB 的内容作为样本（以较低者为准）。
- 对于文档文件格式，它抽取每个文件前 20 MB 的内容作为样本。
    - 如果文档文件大于 20 MB，则不受深度扫描的限制（受分类限制）。 在这种情况下，Purview 仅捕获基本元数据，例如文件名和完全限定的名称。
- 对于表格数据源（SQL、CosmosDB），它抽取前 128 行的内容作为样本。 

## <a name="resource-set-file-sampling"></a>资源集文件采样

如果分区文件的文件夹或组与系统资源集策略或客户定义的资源集策略相匹配，则该文件夹或组会在 Purview 中被检测为资源集。 如果检测到资源集，Purview 将对该资源集包含的每个文件夹进行采样。 在[此处](concept-resource-sets.md)了解有关资源集的更多信息。

按文件类型对资源集进行文件采样：

- 分隔文件（CSV、PSV、SSV、TSV）- 在被视为“资源集”的分区文件的文件夹或组中，对 100 个文件中的 1 个文件进行采样（L3 扫描）
- Data Lake 文件类型（Parquet、Avro、Orc）- 在被视为“资源集”的分区文件的文件夹或组中，对 18446744073709551615（最大值）个文件中的 1 个文件进行采样（L3 扫描）
- 其他结构化文件类型（JSON、XML、TXT）- 在被视为“资源集”的分区文件的文件夹或组中，对 100 个文件中的 1 个文件进行采样（L3 扫描）
- SQL 对象和 CosmosDB 实体 - 每个文件都经过 L3 扫描。
- 文档文件类型 - 每个文件都经过 L3 扫描。 资源集模式不适用于这些文件类型。

## <a name="classification"></a>分类

所有 206 条系统分类规则都适用于结构化文件格式。 只有 MCE 分类规则适用于文档文件类型（不是数据扫描本机 regex 模式，基于布隆筛选器的检测）。 有关受支持的分类的详细信息，请参阅 [Azure Purview 中受支持的分类](supported-classifications.md)。

## <a name="next-steps"></a>后续步骤

- [Purview 中的扫描和引入](concept-scans-and-ingestion.md)
- [在 Azure Purview 中管理数据源](manage-data-sources.md)
