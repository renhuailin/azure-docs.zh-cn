---
title: 使用 Azure 门户将数据导入搜索索引
titleSuffix: Azure Cognitive Search
description: 了解 Azure 门户中的导入数据向导，你可以使用该向导创建和加载索引，并根据需要使用内置技能来调用 AI 扩充，以进行自然语言处理、翻译、OCR 和图像分析。
author: HeidiSteen
manager: nitinme
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 10/06/2021
ms.openlocfilehash: 9fe443422bac06ccb934a34799bc29be29c45cb5
ms.sourcegitcommit: 860f6821bff59caefc71b50810949ceed1431510
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/09/2021
ms.locfileid: "129714679"
---
# <a name="import-data-wizard-in-azure-cognitive-search"></a>Azure 认知搜索中的导入数据向导

Azure 门户中的导入数据向导可在搜索服务上创建多个用于索引和 AI 扩充的对象。 如果你是初次接触 Azure 认知搜索，那么，它将成为可由你掌控的最强大的功能之一。 你可以轻松创建一个索引或扩充管道，来执行 Azure 认知搜索的大部分功能。

如果你正在使用向导进行概念验证测试，本文将解释向导的内部工作原理，以便你更有效地使用它。

本文并非分步指南。 有关如何使用带有内置示例数据的向导的帮助，请参阅[快速入门：创建搜索索引](search-get-started-portal.md)或[快速入门：创建文本翻译和实体技能组](cognitive-search-quickstart-blob.md)。

## <a name="starting-the-wizard"></a>启动向导

在 [Azure 门户](https://portal.azure.com)中，从仪表板打开搜索服务页，或者在服务列表中[查找服务](https://ms.portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)。 在顶部的服务概述页中，单击“导入数据”。

   :::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="“导入数据”命令的屏幕截图" border="true":::

向导在浏览器窗口中以完全展开的形式打开，以便为你提供更多操作空间。 有几个页面的内容相当密集。

还可以通过其他 Azure 服务（包括 Azure Cosmos DB、Azure SQL 数据库、SQL 托管实例和 Azure Blob 存储）启动“导入数据”。 在服务概述页上的左侧导航窗格中查找“添加 Azure 认知搜索”。

## <a name="objects-created-by-the-wizard"></a>向导创建的对象

向导将输出下表中的对象。 创建对象后，你可以在门户中查看其 JSON 定义或从代码中调用它们。

| 对象 | 说明 | 
|--------|-------------|
| [索引器](/rest/api/searchservice/create-indexer)  | 一个配置对象，指定数据源、目标索引、可选计划，以及有关错误处理和 base-64 编码的可选技能集、可选计划和可选配置设置。 |
| [数据源](/rest/api/searchservice/create-data-source)  | 将连接信息（包括凭据）保存到源数据。 某个数据源对象专用于索引器。 | 
| [索引](/rest/api/searchservice/create-index) | 用于全文搜索和其他查询的物理数据结构。 | 
| [技能集](/rest/api/searchservice/create-skillset) | 可选。 用于操作、转换和调整内容（包括分析和提取图像文件中的信息）的完整指令集。 它包括对提供扩充的认知服务资源的引用，但非常简单的和受限制的结构除外。 | 
| [知识存储](knowledge-store-concept-intro.md) | 可选。 将 [AI 扩充管道](cognitive-search-concept-intro.md)的输出存储在 Azure 存储的表和 blob 中，以进行独立分析或下游处理。 |

## <a name="benefits-and-limitations"></a>权益和限制

在编写任何代码之前，可以使用向导进行原型制作和概念证明测试。 向导将连接到外部数据源，对数据进行采样以创建初始索引，然后将数据作为 JSON 文档导入到 Azure 认知搜索中的索引。 

如果要评估技能组，向导将处理所有输出字段映射，并添加帮助程序函数以创建可用对象。 如果指定分析模式，则会添加文本拆分。 如果选择图像分析，则会添加文本合并，以便向导可以将文本说明与图像内容重新组合。 如果选择知识存储选项，则会添加整形程序技能来支持有效的投影。 上述所有任务都提供学习曲线。 如果你还未用过扩充，其强大的功能（即，为你处理这些步骤）让你无需投入大量时间和精力即可衡量一项技能的价值。

采样是推断索引架构的过程，它存在一些限制。 创建数据源时，向导会选取文档的随机样本，以确定哪些列是数据源的一部分。 不一定会读取所有文件，因为对于极大的数据源，这可能需要好几个小时。 在提供一系列文档的情况下，将使用源元数据（例如字段名称或类型）在索引架构中创建字段集合。 根据源数据的复杂性，可能需要编辑初始架构以提高准确性，或对其进行扩展以获得完整性。 可以在索引定义页上以内联方式进行更改。

总体而言，使用向导的优点非常明显：只要符合要求，就可以在数分钟内制作出可查询索引的原型。 向导在一定程度上可以处理索引编制的复杂性，例如，以 JSON 文档的形式序列化数据。

向导并非没有限制。 其限制可以总结如下：

+ 向导不支持迭代或重用。 每次运行向导都会创建新的索引、技能集和索引器配置。 在向导中只能保存和重用数据源。 若要编辑或优化其他对象，请删除对象并重新开始，或者使用 REST API 或 .NET SDK 修改结构。

+ 源内容必须位于[受支持的数据源](search-indexer-overview.md#supported-data-sources)中。

+ 采样是针对一部分源数据执行的。 对于大型数据源，向导可能会遗漏字段。 如果采样不足，可能需要扩展架构或更正推断出的数据类型。

+ 门户中公开的 AI 扩充限制为几个内置技能。 

+ 可由向导创建的[知识存储](knowledge-store-concept-intro.md)限制为几个默认投影并使用默认命名约定。 如果要自定义名称或投影，需要通过 REST 或 SDK 创建知识存储。

## <a name="workflow"></a>工作流

向导分为四个主要步骤：

1. 连接到支持的 Azure 数据源。

1. 创建索引架构，通过对源数据采样进行推断。

1. （可选）添加 AI 扩充以提取或生成内容和结构。 此步骤将收集用于创建知识存储的输入。

1. 运行向导以创建对象、加载数据、设置计划和其他配置选项。

<a name="data-source-inputs"></a>

### <a name="data-source-configuration-in-the-wizard"></a>向导中的数据源配置

导入数据向导使用 Azure 认知搜索索引器提供的内部逻辑连接到[受支持的外部数据源](search-indexer-overview.md#supported-data-sources)。索引器旨在对源采样、读取元数据、破解文档以读取内容和结构，并将内容序列化为 JSON，供后续导入到 Azure 认知搜索。

无法保证所有预览数据源都在向导中可用。 由于每个数据源都有可能在下游引入其他更改，因此只有当预览数据源完全支持向导中的所有体验（例如技能组定义和索引架构推断）时，才会将其添加到数据源列表中。

只能从单个表、数据库视图或等效的数据结构导入，但是，此结构可能包含分层的或嵌套的子结构。 有关详细信息，请参阅[如何为复杂类型建模](search-howto-complex-data-types.md)。

### <a name="skillset-configuration-in-the-wizard"></a>向导中的技能组配置

技能组配置发生在数据源定义之后，因为数据源的类型将指示某些内置技能的可用性。 具体来说，如果你正在为 Blob 存储中的文件编制索引，那么，你为这些文件选择的分析模式将决定情绪分析是否可用。

向导将添加你选择的技能，但也会添加实现成功结果所需的其他技能。 例如，如果你指定了知识存储，向导会添加整形程序技能来支持投影（或物理数据结构）。

技能组是可选项；如果不想使用 AI 扩充，可以单击页面底部的按钮跳过此步骤。

<a name="index-definition"></a>

### <a name="index-schema-configuration-in-the-wizard"></a>向导中的索引架构配置

向导将对数据源采样，以检测字段和字段类型。 根据数据源，它还有可能提供用于为元数据编制索引的字段。

由于采样是一项不精确的工作，因此查看索引时要考虑以下因素：

1. 字段列表是否准确？ 如果数据源包含未在采样中选取的字段，你可以手动添加采样遗漏的任何新字段，并删除无法提升搜索体验或者不会在[筛选表达式](search-query-odata-filter.md)或[计分概要文件](index-add-scoring-profiles.md)中使用的字段。

1. 数据类型是否适合传入的数据？ Azure 认知搜索支持[实体数据模型 (EDM) 数据类型](/rest/api/searchservice/supported-data-types)。 对于 Azure SQL 数据，有一个[映射图表](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers.md#TypeMapping)会列出等效值。 有关更多背景信息，请参阅[字段映射和转换](search-indexer-field-mappings.md)。

1. 是否有一个可用作键的字段？ 此字段的类型必须是 Edm.string，并且必须唯一标识某个文档。 对于关系数据，它可能会映射到主键。 对于 Blob，它可能是 `metadata-storage-path`。 如果字段值包含空格或短划线，则必须在“高级选项”下的“创建索引器”步骤中设置“Base 64 编码密钥”选项以禁止验证检查这些字符    。

1. 设置属性以确定如何在索引中使用该字段。 

   请花些时间来完成此步骤，因为属性确定了索引中字段的物理表达式。 以后若要更改属性（即使是以编程方式进行更改），几乎总要删除并重建索引。 **Searchable** 和 **Retrievable** 等核心属性 [对存储会产生负面影响](search-what-is-an-index.md#index-size)。 启用筛选器和使用建议器会提高存储要求。 

   + “可搜索”启用全文搜索  。 在自由格式查询或查询表达式中使用的每个字段必须有此属性。 为标记为“可搜索”的每个字段创建反向索引  。

   + “可检索”在搜索结果中返回该字段  。 用于提供内容以搜索结果的每个字段必须有此属性。 设置此字段不会明显影响索引大小。

   + “可筛选”允许在筛选表达式中引用该字段  。 在 $filter 表达式中使用的每个字段必须有此属性  。 筛选表达式用于精确匹配项。 由于文本字符串保持不变，因此需要更多存储空间来容纳逐字内容。

   + “可查找”为分面导航启用该字段  。 只有也标记为“可筛选”的字段可标记为“可查找”   。

   + “可排序”允许在排序中使用该字段  。 在 $Orderby 表达式中使用的每个字段必须有此属性  。

1. 是否需要[词法分析](search-lucene-query-architecture.md#stage-2-lexical-analysis)？ 对于 **可搜索的** Edm.string 字段，若要获得语言增强的索引和查询，可以设置 **分析器**。 

   默认值为“标准 Lucene”，但如果想要使用 Microsoft 的分析器以进行高级词汇处理（如解决不规则名词和动词形式），可选择“Microsoft 英语”   。 在门户中只能指定语言分析器。 若要使用自定义分析器或非语言分析器（例如关键字、模式等），必须以编程方式来实现。 有关分析器的详细信息，请参阅[添加语言分析器](search-language-support.md)。

1. 是否需要自动完成或建议结果形式的自动提示功能？ 选中“建议器”复选框，对所选字段启用[自动提示查询建议和自动完成](index-add-suggesters.md)。 使用建议器会增加索引中标记化字词的数目，因此会消耗更多的存储。

### <a name="indexer-configuration-in-the-wizard"></a>向导中的索引器配置

向导的最后一页将收集用于索引器配置的用户输入。 你可以[指定计划](search-howto-schedule-indexers.md)并设置其他选项，这些选项因数据源类型而异。

在内部，向导还会设置以下内容，这些内容在创建索引器后才可见：

+ 数据源和索引之间的[字段映射](search-indexer-field-mappings.md)
+ 技能输出和索引之间的[输出字段映射](cognitive-search-output-field-mapping.md)

## <a name="next-steps"></a>后续步骤

了解向导的优点和限制的最佳方式就是逐步运行该向导。 以下快速入门将指导你完成每个步骤。

> [!div class="nextstepaction"]
> [快速入门：使用 Azure 门户创建搜索索引](search-get-started-portal.md)