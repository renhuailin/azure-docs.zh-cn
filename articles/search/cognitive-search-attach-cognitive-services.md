---
title: 将认知服务附加到技能组
titleSuffix: Azure Cognitive Search
description: 了解如何将认知服务一体化订阅附加到 Azure 认知搜索中的 AI 扩充管道。
author: LuisCabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 02/16/2021
ms.openlocfilehash: c1ba8ce3e84439a3f9419e0c038b93fb298370b9
ms.sourcegitcommit: b11257b15f7f16ed01b9a78c471debb81c30f20c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/08/2021
ms.locfileid: "111591325"
---
# <a name="attach-a-cognitive-services-resource-to-a-skillset-in-azure-cognitive-search"></a>将认知服务资源附加到 Azure 认知搜索中的技能组

在 Azure 认知搜索中配置 [AI 扩充管道](cognitive-search-concept-intro.md)时，可以免费扩充有限数量的文档。 对于更大、更频繁的工作负载，应附加一个可计费的“一体化”认知服务资源。 “一体化”订阅将“认知服务”引用为产品/服务而不是单独的服务，并通过单个 API 密钥授予访问权限。

“一体化”认知服务资源可促进提升一系列可添加到技能组中的[预定义技能](cognitive-search-predefined-skills.md)：

+ 用于图像分析和光学字符识别 (OCR) 的[计算机视觉](https://azure.microsoft.com/services/cognitive-services/computer-vision/)
+ 用于语言检测、实体识别、情绪分析和关键短语提取的[文本分析](https://azure.microsoft.com/services/cognitive-services/text-analytics/)
+ [文本翻译](https://azure.microsoft.com/services/cognitive-services/translator-text-api/)

技能组定义中有一个可选的“一体化”认知服务密钥。 当每日事务数小于 20 时，会吸收成本。 但是，当事务数超出该数字时，需要有效的资源密钥才能继续进行处理。

任何“一体化”资源密钥都有效。 在内部，搜索服务将使用位于同一物理区域中的资源，即使“一体化”密钥用于不同区域中的资源也是如此。 [产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=search)页并排显示区域可用性。

> [!NOTE]
> 如果在技能组中省略预定义的技能，则不会访问认知服务，也不会向你收费，即使该技能组指定了密钥也是如此。

## <a name="how-billing-works"></a>计费原理

+ Azure 认知搜索使用技能组上提供的认知服务资源密钥来对图像和文本扩充进行计费。 可计费技能的执行将按[认知服务即用即付价格](https://azure.microsoft.com/pricing/details/cognitive-services/)进行计费。

+ 图像提取是一种 Azure 认知搜索操作，该操作在文档扩充前被破解时发生。 图像提取可计费。 有关图像提取的定价信息，请参阅 [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)。

+ 文本提取也发生在文档破解阶段。 文本提取不可计费。

+ 不调用认知服务的技能（包括条件、整形、文本合并和文本拆分技能）不可计费。

## <a name="same-region-requirement"></a>相同区域要求

认知搜索和认知服务必须存在于同一物理区域内，如[产品可用性](https://azure.microsoft.com/global-infrastructure/services/?products=search)页中所示。 提供认知搜索的大多数区域也提供认知服务。

如果尝试在不同时具备这两种服务的区域中进行 AI 扩充，则将看到以下消息：“提供的密钥不是搜索服务区域的有效 CognitiveServices 类型密钥。”

> [!NOTE]
> 某些内置技能基于非区域认知服务（例如，[文本翻译技能](cognitive-search-skill-text-translation.md)）。 使用非区域性技能意味着可能会在 Azure 认知搜索区域以外的区域中为你的请求提供服务。 有关非区域性服务的详细信息，请参阅[认知服务产品（按区域）](https://aka.ms/allinoneregioninfo)页。

## <a name="use-free-resources"></a>使用免费资源

可以使用有限的免费处理选项来完成 AI 扩充教程和快速入门练习。

免费（有限扩充）资源限制为每个索引器每日 20 个文档。 可以[重置索引器](search-howto-run-reset-indexers.md)以重置计数器。

如果使用“导入数据”向导进行 AI 扩充，则“添加 AI 扩充(可选)”页上会有“附加认知服务”选项 。

![展开的“附加认知服务”部分](./media/cognitive-search-attach-cognitive-services/attach1.png "展开的“附加认知服务”部分")

## <a name="use-billable-resources"></a>使用付费资源

对于每天创建超过 20 个扩充的工作负荷，请确保附加可计费的认知服务资源。 我们建议你始终附加可计费的认知服务资源，即使你从未打算调用认知服务 API 也是如此。 附加资源会重写每日限制。

只有调用认知服务 API 的技能才收费。 [自定义技能](cognitive-search-create-custom-skill-example.md)，或者不是基于 API 的技能（例如[文本合并器](cognitive-search-skill-textmerger.md)、[文本拆分器](cognitive-search-skill-textsplit.md)和[整形程序](cognitive-search-skill-shaper.md)）不收费。

如果使用“导入数据”向导，可以从“添加 AI 扩充(可选)”页配置可计费资源 。

1. 展开“附加认知服务”，然后选择“创建新的认知服务资源”。 此时会打开一个新的选项卡让你创建资源：

   ![创建认知服务资源](./media/cognitive-search-attach-cognitive-services/cog-services-create.png "创建认知服务资源")

1. 在“位置”列表中，选择包含搜索服务的区域。

1. 在“定价层”列表中，选择“S0”获取认知服务功能一体化集合，包括为 Azure 认知搜索提供的内置技能提供支持的“视觉和语言”功能 。

   对于 S0 层，可以在[认知服务定价页](https://azure.microsoft.com/pricing/details/cognitive-services/)上找到特定工作负荷的费率。
  
   + 在“选择套餐”列表中，确保“认知服务”已选中。
   + 在“语言”功能下，“文本分析标准版”的费率适用于 AI 索引。
   + 在“视觉”功能下，适用“计算机视觉 S1”的费率。

1. 选择“创建”预配新的认知服务资源。

1. 返回到上一选项卡。选择“刷新”显示该认知服务资源，然后选择该资源。

   ![选择认知服务资源](./media/cognitive-search-attach-cognitive-services/attach2.png "选择认知服务资源")

1. 展开“添加认知技能”部分，选择要针对数据运行的特定认知技能。 完成向导中的剩余步骤。

## <a name="attach-an-existing-skillset-to-a-cognitive-services-resource"></a>将现有技能集附加到认知服务资源

如果你有现有的技能集，可将其附加到新的或不同的认知服务资源。

1. 在搜索服务概述页面，选择“技能组”：

   ![“技能组”选项卡](./media/cognitive-search-attach-cognitive-services/attach-existing1.png "“技能组”选项卡")

1. 选择技能集的名称，然后选择现有资源或新建资源。 选择“确定”以确认所做的更改。

   ![技能组资源列表](./media/cognitive-search-attach-cognitive-services/attach-existing2.png "技能组资源列表")

   请记住，“免费(有限扩充)”选项限制为每日 20 个文档，可以使用“创建新的认知服务资源”预配新的可计费资源。 如果创建了新资源，请选择“刷新”以刷新认知服务资源的列表，然后选择该资源。

## <a name="attach-cognitive-services-programmatically"></a>以编程方式附加认知服务

以编程方式定义技能集时，请将 `cognitiveServices` 节添加到该技能集。 在该节中，包含要与该技能集关联的认知服务资源的键。 请记住，该资源必须位于 Azure 认知搜索资源所在的同一区域。 另外请包含 `@odata.type`，并将其设置为 `#Microsoft.Azure.Search.CognitiveServicesByKey`。

以下示例演示了此模式。 请注意定义末尾的 `cognitiveServices` 节。

```http
PUT https://[servicename].search.windows.net/skillsets/[skillset name]?api-version=2020-06-30
api-key: [admin key]
Content-Type: application/json
{
    "name": "skillset name",
    "skills": 
    [
      {
        "@odata.type": "#Microsoft.Skills.Text.EntityRecognitionSkill",
        "categories": [ "Organization" ],
        "defaultLanguageCode": "en",
        "inputs": [
          {
            "name": "text", "source": "/document/content"
          }
        ],
        "outputs": [
          {
            "name": "organizations", "targetName": "organizations"
          }
        ]
      }
    ],
    "cognitiveServices": {
        "@odata.type": "#Microsoft.Azure.Search.CognitiveServicesByKey",
        "description": "mycogsvcs",
        "key": "<your key goes here>"
    }
}
```

## <a name="example-estimate-costs"></a>示例：估算成本

若要估算认知搜索索引编制的相关成本，请先构想一下文档的大致结构，以便能够得出一些数字。 例如，可以使用以下似近值：

+ 1,000 个 PDF。
+ 每个 PDF 有 6 个页面。
+ 每个页面包含一个图像（共 6,000 个图像）。
+ 每个页面包含 3,000 个字符。

假设管道的功能包括：每个 PDF 的文档破解、图像和文本提取、图像的光学字符识别 (OCR)，以及组织的实体识别。

本文中所示的价格是虚构的。 这些价格用于演示估算过程。 你的成本可能更低。 有关实际交易价格，请参阅[认知服务定价](https://azure.microsoft.com/pricing/details/cognitive-services)。

1. 破解包含文本和图像内容的文档时，文本提取目前是免费的。 对于 6,000 个图像，假设每提取 1,000 个图像需要 $1。 则此步骤的成本是 $6.00。

2. 对于英语环境中 6000 个图像的 OCR，OCR 认知技能使用最佳算法 (DescribeText)。 假设每分析 1,000 个图像的成本为 $2.50，则这一步需要支付 $15.00。

3. 提取实体时，每页总共有 3 个文本记录。 每条记录包含 1,000 个字符。 每页 3 个文本记录 * 6,000 页 = 18,000 个文本记录。 假设 1000 个文本记录的成本为 $2.00，则这一步的成本为 $36.00。

综合起来，在使用上述技能集引入 1,000 个此类 PDF 文档时，需要支付大约 $57.00。

## <a name="next-steps"></a>后续步骤

+ [Azure 认知搜索定价页](https://azure.microsoft.com/pricing/details/search/)
+ [如何定义技能集](cognitive-search-defining-skillset.md)
+ [创建技能组合 (REST)](/rest/api/searchservice/create-skillset)
+ [如何映射扩充的域](cognitive-search-output-field-mapping.md)
