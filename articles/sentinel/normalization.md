---
title: Azure Sentinel 中的数据规范化 | Microsoft Docs
description: 本文介绍 Azure Sentinel 如何规范化许多不同源中的数据，并详细说明规范化架构。
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/08/2020
ms.author: yelevin
ms.openlocfilehash: 08f7f7998fce5c3361ec8d89a7ae4da4a43db832
ms.sourcegitcommit: 80d311abffb2d9a457333bcca898dfae830ea1b4
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/26/2021
ms.locfileid: "110466626"
---
# <a name="normalization-in-azure-sentinel"></a>Azure Sentinel 中的规范化

本文介绍 Azure Sentinel 中的数据架构规范化，并专门介绍了网络连接和会话架构（其链接也包含在本文内）。

## <a name="what-is-normalization"></a>什么是规范化

同时处理多种数据类型和表具有难度。 必须熟悉许多不同的数据类型和架构，此外，必须为每种数据类型和架构（甚至是那些具有共性的数据类型和架构，例如，与防火墙设备相关的类型和架构）编写并使用一组独特的分析规则、工作簿和搜寻查询。 在调查和搜寻时所需的不同数据类型之间的关联也很复杂。 Azure Sentinel 提供了一种无缝体验，以用于在统一的规范化视图中处理来自不同源的数据。

Azure Sentinel 通用信息模型由以下三个方面构成：

- 规范化架构涵盖通用的可预测事件类型（表）集，这些集易于处理，并可用作统一功能的构建基础（例如网络表）。 该架构还包括规范化的列约定，以及值和格式标准化（IP 地址等数据的标准一致表示形式）的定义。

- 分析器将不同类型的现有数据映射到规范化架构。 在查询时（使用函数）或引入时，可以根据模型将数据分析成规范化架构。 目前仅支持查询时分析。

- 每个规范化架构的内容包括分析规则、交互式工作簿、搜寻查询和其他内容。

### <a name="current-release"></a>当前版本

在此版本中，[规范化网络连接和会话架构](./normalization-schema.md) (v1.0.0) 适用于公共预览版。 该架构描述网络连接或会话，例如防火墙、Wire Data、NSG、Netflow、代理系统和 Web 安全网关记录的连接或会话。  所选的查询时分析器和分析规则随架构一起提供，并利用该架构。

目前只能通过查询时分析器（请参阅“分析器”部分）使用架构。

可将数据分析成其他表示形式，并使用 [OSSEM 实体命名模型](https://ossemproject.com/cdm/entities/intro.html#)创建与现有和将来的规范化表一致的列。

## <a name="normalized-schema-and-parsing"></a>规范化架构和分析

### <a name="how-our-normalized-schemas-are-defined"></a>如何定义规范化架构

Azure Sentinel 与[开源安全事件元数据 (OSSEM)](https://ossemproject.com/intro.html) 通用信息模型相一致，可以在规范化表之间实现可预测的实体关联。 OSSEM 是社区导向型项目，主要注重于记录和标准化来自不同数据源与操作系统的安全事件日志。 此外，该项目还提供一个通用信息模型 (CIM)，可供数据工程师在数据建模过程中使用，从而使安全分析师可以查询和分析不同数据源中的数据。

[OSSEM CIM](https://ossemproject.com/cdm/intro.html) 定义一组实体（例如：file、host、IP、process），并为每个此类实体定义一组特性。 此外，CIM 定义一组表（例如[网络会话](https://ossemproject.com/cdm/tables/network_session.html)表），这些表使用这些实体中的相关特性，可以实现无缝且可预测的关联。 请注意，实体可以嵌套（例如，Source 实体可以包含具有 name 特性的 File 实体）。

若要详细了解 OSSEM 实体结构，请访问[官方的 OSSEM 参考](https://ossemproject.com/cdm/guidelines/entity_structure.html)。

### <a name="how-the-normalized-schemas-are-implemented-in-azure-sentinel"></a>如何在 Azure Sentinel 中实现规范化架构

在 Azure Sentinel 中的 OSSEM CIM 实现中，我们将 OSSEM 表示形式投影为 Log Analytics 表格表示形式，不管这种表示形式是内置表，还是使用可将现有数据映射到此表示形式的查询时分析器或函数创建的。 对于表格表示形式，我们将连接 OSSEM 实体名称和特性名称，并将其统一映射到单个列名称。 例如，如果 Source 实体包含 File 实体，其中的 File 实体包含 Hash 实体，而 Hash 实体又包含 md5 特性，则 Source 实体将实现为以下 Log Analytics 列：SrcFileHashMd5。 （OSSEM 默认使用 snake_case，而 Azure Sentinel 和 Log Analytics 则使用 PascalCase 。 在 OSSEM 中，此类列为 src_file_hash_md5。）

由于 Log Analytics 平台要求和用例是特定于各 Azure Sentinel 客户的，Azure Sentinel 实现中可能存在其他自定义字段。

### <a name="schema-reference"></a>架构参考

有关详细信息，请参阅[架构参考文档](./normalization-schema.md)以及官方的 [OSSEM 项目文档](https://ossemproject.com/cdm/intro.html)。

架构参考中还包括了值和格式标准化。 源字段（原始字段或分析的字段）可能不采用标准格式或者未使用架构标准值列表，因此需要转换为架构标准表示形式才能完全标准化。

## <a name="parsers"></a>分析器

- [分析的内容](#what-is-parsing)
- [使用查询时分析器](#using-query-time-parsers)

### <a name="what-is-parsing"></a>分析的内容

已有一组基本的已定义规范化表可用，你需要将数据转换（分析/映射）到这些表。 也就是说，需要将特定数据按其原始格式提取到规范化架构中的已知列中。 Azure Sentinel 中的分析是在查询时发生的 - 分析器是以 Log Analytics 用户函数的形式构建的（使用 Kusto 查询语言 - KQL），可将现有表（例如 CommonSecurityLog、自定义日志表、syslog）中的数据转换为规范化的表架构。

Azure Sentinel 尚不支持的另一种分析是引入时分析，引入时分析可以在从数据源引入数据时直接将数据收集到规范化表中。 引入时分析提供更高的性能，因为可以直接查询数据模型，而无需使用函数。

### <a name="using-query-time-parsers"></a>使用查询时分析器

- [安装分析器](#installing-a-parser)
- [使用分析器](#using-the-parsers)
- [自定义分析器](#customizing-parsers)

#### <a name="installing-a-parser"></a>安装分析器

Azure Sentinel [官方 GitHub 存储库](https://github.com/Azure/Azure-Sentinel/tree/master/Parsers/Normalized%20Schema%20-%20Networking%20(v1.0.0))中提供了可用的查询时分析器。 每个分析器均已进行版本管理，使客户可以轻松使用和监视将来的更新。 若要安装分析器，请执行以下操作：

1. 将上述 GitHub 链接中每个相关 KQL 文件的相关分析器内容复制到剪贴板

1. 在 Azure Sentinel 门户中打开“日志”页，将 KQL 文件的内容粘贴到“日志”屏幕，然后单击“保存”。

    :::image type="content" source="./media/normalization/install-new-parser.png" alt-text="安装新分析器":::

1. 在“保存”对话框中，按如下所述填充字段：
    1. 名称：建议使用“函数别名”字段中所用的值（在前面的示例中为 CheckPoint_Network_NormalizedParser） 
    
    1. 另存为：选择“函数” 

    1. 函数别名：应按照以下命名约定命名 - PRODUCT_Network_NormalizedParser（在前面的示例中为 CheckPoint_Network_NormalizedParser） 。

    1. 类别：可以选择现有类别或创建新类别（例如 NormalizedNetworkSessionsParsers）
    
        :::image type="content" source="./media/normalization/save-new-parser.png" alt-text="保存分析器":::

若要正常使用分析器，还必须安装空网络架构分析器（用于创建所有网络会话架构字段的空表格视图）和网络元分析器（用于将所有已启用的分析器联合到一起，以便为网络架构中各种源的数据创建单一视图）。 这两个分析器的安装方式与上述步骤类似。

保存查询函数后，可能需要关闭查询资源管理器并重新打开它才能反映新函数。

#### <a name="using-the-parsers"></a>使用分析器

启用元分析器后，可以使用它在统一视图中查询当前已启用的所有分析器。 为此，请转到 Azure Sentinel“日志”页并查询元分析器：

:::image type="content" source="./media/normalization/query-parser.png" alt-text="查询分析器":::
 
还可以通过单击“查询资源管理器”，使用“日志”页中的查询资源管理器访问元分析器或单个分析器：

:::image type="content" source="./media/normalization/query-explorer.png" alt-text="查询资源管理器":::

在右侧窗格中，展开“已保存的查询”部分并找到“NormalizedNetworkParsers”文件夹（或在创建分析器时选择的类别名称）：

:::image type="content" source="./media/normalization/find-parser.png" alt-text="查找分析器":::

可以单击每个分析器并查看它使用的基础函数，然后运行该分析器（或者按上文所述直接按别名访问分析器）。 请注意，为方便起见，某些分析器可能会将原始字段和规范化字段并列保留。 可以在分析器的查询中轻松编辑此设置。

> [!TIP]
> 可以在任何查询（包括搜寻查询和检测查询）中使用保存的函数，而不使用 Azure Sentinel 表。 有关详细信息，请参见:
>
> - [Azure Sentinel 中的数据规范化](normalization.md#parsers)
> - [分析 Azure Monitor 日志中的文本](../azure-monitor/logs/parse-text.md)
>
#### <a name="customizing-parsers"></a>自定义分析器

可以重复上述步骤（在查询资源管理器中查找分析器），单击相关的分析器并查看其函数实现。
例如，可以编辑元分析器以添加/删除单个分析器。

:::image type="content" source="./media/normalization/customize-parser.png" alt-text="自定义分析器":::
 
更改函数后，再次单击“保存”并使用相同的名称、别名和类别。 此时将打开替代对话框 – 请按“确定”：

:::image type="content" source="./media/normalization/are-you-sure.png" alt-text="是否确定":::

#### <a name="additional-information"></a>其他信息

对于查询时分析而言，使用 JSON、XML 和 CSV 特别方便。 Azure Sentinel 提供适用于 JSON、XML 和 CSV 的内置分析函数，并提供 JSON 分析工具。  有关详细信息，请参阅博客文章 [Using JSON fields in Azure Sentinel](https://techcommunity.microsoft.com/t5/azure-sentinel/tip-easily-use-json-fields-in-sentinel/ba-p/768747)（在 Azure Sentinel 中使用 JSON 字段）。 

详细了解 Log Analytics 中[已保存的查询](../azure-monitor/logs/queries.md)（查询时分析器实现）。


## <a name="next-steps"></a>后续步骤

在本文档中，你已了解 Azure Sentinel 的规范化架构。 有关参考架构本身，请参阅 [Azure Sentinel 数据规范化架构参考](./normalization-schema.md)。

* [Azure Sentinel 博客](https://aka.ms/azuresentinelblog)。 查找关于 Azure 安全性及合规性的博客文章。