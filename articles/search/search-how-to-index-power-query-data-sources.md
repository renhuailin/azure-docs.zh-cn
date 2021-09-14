---
title: 使用 Power Query 连接器（预览版）为数据编制索引
titleSuffix: Azure Cognitive Search
description: 使用 Power Query 连接器从不同的数据源导入数据。
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/25/2021
ms.custom: references_regions
ms.openlocfilehash: fb7a16824ccb74ad2cab7c0175532d297ebaf88c
ms.sourcegitcommit: 43dbb8a39d0febdd4aea3e8bfb41fa4700df3409
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123451198"
---
# <a name="index-data-using-power-query-connectors-preview"></a>使用 Power Query 连接器（预览版）为数据编制索引

> [!IMPORTANT] 
> Power Query 连接器支持目前处于“封闭公共预览版”阶段。 [注册](https://aka.ms/azure-cognitive-search/indexer-preview)以请求访问。

如果通过使用索引器对外部数据源进行抓取的方式来编制索引，现在可以使用 [Power Query](/power-query/power-query-what-is-power-query) 连接器在 Azure 认知搜索中进行数据源连接。

Power Query 连接器可以访问更广泛的数据源，包括其他云提供商的数据源。 此预览版中支持的新数据源包括：

+ Amazon Redshift
+ Elasticsearch
+ PostgreSQL
+ Salesforce 对象
+ Salesforce 报表
+ Smartsheet
+ Snowflake

本文介绍一种基于 Azure 门户的方法，用于使用 Power Query 连接器来设置索引器。 目前没有 SDK 支持。

> [!NOTE]
> 提供的预览版功能不附带服务级别协议，我们不建议将其用于生产工作负荷。 有关详细信息，请参阅 [Microsoft Azure 预览版补充使用条款](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)。

## <a name="supported-functionality"></a>支持的功能
Power Query 连接器用于索引器中。 Azure 认知搜索中的索引器是一种抓取程序，它从外部数据源提取可搜索的数据和元数据，并根据索引与数据源之间字段到字段的映射填充索引。 由于不需要编写任何将数据添加到索引的代码，该服务就能拉取数据，因此这种方法有时也称为“拉取模式”。 索引器为用户提供了一种从他们的数据源中为内容编制索引的简便方法，无需编写他们自己的抓取程序或推送模型。

在技能组、计划、高水印更改检测逻辑和其他索引器支持的大多数参数等方面，引用 Power Query 数据源的索引器具有相同的支持级别。

## <a name="prerequisites"></a>先决条件
在开始从某一种受支持的数据源拉取数据之前，需要确保已经设置了所有的资源。
+ Azure 认知搜索服务
    + 在某个[受支持的区域](search-how-to-index-power-query-data-sources.md#regional-availability)中设置的 Azure 认知搜索服务。
    + 请确保 Azure 认知搜索团队已为该预览版启用了搜索服务。 可以通过填写[此表单](https://aka.ms/azure-cognitive-search/indexer-preview)来注册该预览版。 
+ Azure Blob 存储帐户
    + 预览版需要 Blob 存储帐户才能用作数据的中介。 数据将会从数据源流出，然后流向 Blob 存储，再流向索引。 此要求只对初始封闭预览版存在。

## <a name="getting-started-using-the-azure-portal"></a>开始使用 Azure 门户
该 Azure 门户为 Power Query 连接器提供支持。 通过数据采样并读取容器中的元数据，Azure 认知搜索中的导入数据向导可以创建默认索引、将源字段映射到目标索引字段，并以单个操作加载索引。 根据源数据的大小和复杂性，在数分钟内就能创建一个有效的全文搜索索引。

 以下视频演示如何在 Azure 认知搜索中设置 Power Query 连接器。
 
> [!VIDEO https://www.youtube.com/embed/uy-l4xFX1EE]

### <a name="step-1--prepare-source-data"></a>步骤 1 - 准备源数据
确保数据源包含数据。 导入数据向导会读取元数据并执行数据采样，以推断索引架构，但它也会从你的数据源加载数据。 如果缺少数据，该向导将会停止并返回错误。 

### <a name="step-2--start-import-data-wizard"></a>步骤 2 - 启动“导入数据”向导
在批准你使用该预览版后，Azure 认知搜索团队将会提供 Azure 门户链接，该链接会使用功能标志，以便你可以访问 Power Query 连接器。 请打开此页面，并通过选择“导入数据”，从 Azure 认知搜索服务页中的命令栏中启动该向导。

:::image type="content" source="media/search-import-data-portal/import-data-cmd.png" alt-text="“导入数据”命令的屏幕截图" border="true":::

### <a name="step-3--select-your-data-source"></a>步骤 3 – 选择数据源
使用此预览版时，可以从几个数据源拉取数据。 所有使用 Power Query 的数据源都将在它们的磁贴上包含“由 Power Query 提供支持”。 选择数据源。 
 
![选择数据源](./media/search-power-query-connectors/power-query-import-data.png "选择数据源")

在选择了数据源后，请选择“下一步: 配置数据”，以进行到下一部分。

### <a name="step-4--configure-your-data"></a>步骤 4 – 配置数据
在选择了数据源后，将配置连接。 每个数据源都会需要不同的信息。 对于有些数据源，Power Query 文档提供了有关如何连接到数据的其他详细信息。 

+ [PostgreSQL](/power-query/connectors/postgresql)
+ [Salesforce 对象](/power-query/connectors/salesforceobjects)
+ [Salesforce 报表](/power-query/connectors/salesforcereports)

在提供了连接凭据后，请选择“下一步”。

### <a name="step-5--select-your-data"></a>步骤 5 – 选择数据
导入向导将会预览数据源中可用的各个表。 在这一步中，你将检查一个表，其中包含要导入到索引中的数据。
 
![预览数据](./media/search-power-query-connectors/power-query-preview-data.png "预览数据")

在选择了表后，请选择“下一步”。

### <a name="step-6--transform-your-data-optional"></a>步骤 6 – 转换数据（可选）
Power Query 连接器提供丰富的 UI 体验，让你能够操作数据，以便可以将正确的数据发送到索引。 可以删除列、筛选行，还可以进行很多其他操作。 

无需先转换数据，再将数据导入 Azure 认知搜索。

![转换数据](./media/search-power-query-connectors/power-query-transform-your-data.png "转换数据") 

若要详细了解如何使用 Power Query 转换数据，请参阅[使用 Power BI Desktop 中的 Power Query](/power-query/power-query-quickstart-using-power-bi)。 

在转换完数据后，请选择“下一步”。

### <a name="step-7--add-azure-blob-storage"></a>步骤 7 – 添加 Azure Blob 存储
Power Query 连接器预览版目前需要提供 Blob 存储帐户。 此步骤只对初始封闭预览版存在。 此 Blob 存储帐户将充当从数据源移动到 Azure 认知搜索索引的数据的临时存储。

建议提供完全访问权限存储帐户连接字符串： 
```
{ "connectionString" : "DefaultEndpointsProtocol=https;AccountName=<your storage account>;AccountKey=<your account key>;" }
```

可通过导航到“存储帐户”边栏选项卡 >“设置”>“密钥”（对于经典存储帐户）或“设置”>“访问密钥”（对于 Azure 资源管理器存储帐户），从 Azure 门户获取连接字符串。

在提供了数据源名称和连接字符串后，请选择“下一步: 添加认知技能(可选)”。 

### <a name="step-8--add-cognitive-skills-optional"></a>步骤 8 – 添加认知技能（可选）
[AI 扩充](cognitive-search-concept-intro.md)是索引器扩展，可用于使内容更具可搜索性。

这是此预览版的可选步骤。 在完成后，请选择“下一步: 自定义目标索引”。

### <a name="step-9--customize-target-index"></a>步骤 9 – 自定义目标索引
在“索引”页上，应该会看到字段列表，列表中带有数据类型和一系列用于设置索引属性的复选框。 向导可以通过源数据采样，基于元数据生成字段列表。

可以通过单击属性列顶部的复选框，来批量选择属性。 对于应该返回给客户端应用并且需要接受全文搜索处理的每个字段，请选择“可检索”和“可搜索”。  你会注意到，无法对整数进行全文搜索或模糊搜索（数字按原义评估，通常在筛选器中使用）。

有关详细信息，请查看索引属性和语言分析器的说明。

花费片刻时间来检查所做的选择。 在运行向导后，会创建物理数据结构，届时，除非删除并重新创建所有对象，否则无法编辑这些字段的大多数属性。

![创建索引](./media/search-power-query-connectors/power-query-index.png "创建索引")

在完成后，请选择“下一步: 创建索引器”。

### <a name="step-10--create-an-indexer"></a>步骤 10 – 创建索引器
最后一步是创建索引器。 为索引器命名可让它作为独立的资源存在，无论在同一向导序列中创建了哪种索引和数据源对象，都可以计划和管理该索引器。

“导入数据”向导的输出是索引器，它抓取数据源并将所选内容导入到 Azure 认知搜索中的某个索引中。

在创建索引器时，可以选择按计划运行该索引器并添加更改检测。 若要添加更改检测，请指定“高水印”列。

![创建索引器](./media/search-power-query-connectors/power-query-indexer-configuration.png "创建索引器")

在填写完此页面后，请选择“提交”。

## <a name="high-water-mark-change-detection-policy"></a>高使用标记更改检测策略
此更改检测策略依赖于对版本或行的上次更新时间进行捕获的一个“高使用标记”列。

### <a name="requirements"></a>要求
+ 所有插入都为列指定一个值。
+ 对某个项目的所有更新也会更改该列的值。
+ 此列的值随每次插入或更新而增加。

## <a name="unsupported-column-names"></a>不支持的列名称
Azure 认知搜索索引中的字段名称必须满足特定的要求。 其中一个要求是不允许使用有些字符（如“/”）。 如果数据库中的列名不符合这些要求，索引架构检测不会将列识别为有效字段名称，并且你将不会看到该列作为索引的建议字段列出。 通常，使用[字段映射](search-indexer-field-mappings.md)可以解决此问题，但门户不支持字段映射。

若要为表中使用不受支持的字段名称的列的内容编制索引，请在导入数据过程的“转换数据”阶段中重命名该列。 例如，可以将名为“计费代码/邮政编码”的列重命名为“zipcode”。 通过重命名列，索引架构检测会将其识别为有效字段名称，并将其作为建议添加到索引定义。

## <a name="regional-availability"></a>区域可用性
该预览版只适用于以下区域中具有搜索服务的客户：
+ 美国中部
+ 美国东部
+ 美国东部 2
+ 美国中北部
+ 北欧
+ 美国中南部
+ 美国中西部
+ 西欧
+ 美国西部
+ 美国西部 2

## <a name="preview-limitations"></a>预览版限制
此预览版有很多值得期待的精彩内容，但存在一些限制。 本部分介绍特定于该预览版当前版本的限制。
+ 此预览版本不支持从数据源拉取二进制数据。 
+ 目前不支持[调试会话](cognitive-search-debug-session.md)。

## <a name="next-steps"></a>后续步骤
你已经了解了如何使用 Power Query 连接器从新数据源拉取数据。 若要详细了解索引器，请参阅 [Azure 认知搜索中的索引器](search-indexer-overview.md)。