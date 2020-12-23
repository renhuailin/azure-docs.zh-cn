---
title: 搜索自定义视图 - 必应自定义搜索
titleSuffix: Azure Cognitive Services
description: 配置完自定义搜索体验后，可以在必应自定义搜索门户中对其进行测试。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-custom-search
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: aahi
ms.openlocfilehash: 07b0dd68e39f555171e5606b71cd1eec92a4035c
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353334"
---
# <a name="call-your-bing-custom-search-instance-from-the-portal"></a>从门户调用必应自定义搜索实例

> [!WARNING]
> 必应搜索 API 将从认知服务迁移到必应搜索服务。 从 2020 年 10 月 30 日开始，必应搜索的任何新实例都需按照[此处](/bing/search-apis/bing-web-search/create-bing-search-service-resource)所述的过程进行预配。
> 使用认知服务进行预配的必应搜索 API 将在未来三年或在企业协议结束前（以先发生者为准）得到支持。
> 有关迁移说明，请参阅[必应搜索服务](/bing/search-apis/bing-web-search/create-bing-search-service-resource)。

配置完自定义搜索体验后，可以在必应自定义搜索[门户](https://customsearch.ai)中对其进行测试。 

![必应自定义搜索门户的屏幕截图](media/portal-search-screen.png)
## <a name="create-a-search-query"></a>创建搜索查询 

登录到必应自定义搜索 [门户](https://customsearch.ai)后，选择搜索实例，并单击 " **生产** " 选项卡。在 " **终结点**" 下，选择 API 终结点， (例如 Web API) 。 你的订阅决定了显示哪些终结点。

若要创建搜索查询，请为终结点输入参数值。 请注意，门户中显示的参数可能会根据你选择的终结点而更改。 有关详细信息，请参阅 [自定义搜索 API 参考](/rest/api/cognitiveservices-bingsearch/bing-custom-search-api-v7-reference#query-parameters) 。 若要更改搜索实例使用的订阅，请添加相应的订阅密钥，并更新相应的市场和/或语言参数。

一些重要参数如下所述：


|参数  |描述  |
|---------|---------|
|查询     | 要搜索的搜索词。 仅适用于 Web 终结点、图像终结点、视频终结点和自动建议终结点 |
|自定义配置 ID | 所选自定义搜索实例的配置 ID。 该字段是只读字段。 |
|市场     | 结果将来自的市场。 仅适用于 Web 终结点、图像终结点、视频终结点和托管 UI 终结点。        |
|订阅密钥 | 测试时要使用的订阅密钥。 可以从下拉列表中选择一个密钥，也可以手动输入一个。          |

单击“其他参数”可显示以下参数：  

|参数  |描述  |
|---------|---------|
|安全搜索     | 用于筛选成人内容网页的筛选器。 仅适用于 Web 终结点、图像终结点、视频终结点和托管 UI 终结点。 请注意，必应自定义视频搜索仅支持两个值： `moderate` 和 `strict` 。        |
|用户界面语言    | 用于用户界面字符串的语言。 例如，如果你在托管 UI 中启用图像和视频，“图像”和“视频”选项卡使用指定的语言。        |
|计数     | 要在响应中返回的搜索结果数。 仅适用于 Web 终结点、图像终结点和视频终结点。         |
|Offset    | 返回结果前要跳过的搜索结果数。 仅适用于 Web 终结点、图像终结点和视频终结点。        |
    
指定所有必需选项后，单击“调用”即可在右侧窗格中查看 JSON 响应。 如果选择“托管 UI”终结点，可以在底部窗格中测试搜索体验。

## <a name="change-your-bing-custom-search-subscription"></a>更改必应自定义搜索订阅

你可以更改与必应自定义搜索实例关联的订阅而无需创建新的实例。 若要为新订阅发送 API 调用并向其收费，请在 Azure 门户中创建新的必应自定义搜索资源。 在 API 请求中使用新的订阅密钥，同时使用实例的自定义配置 ID。

## <a name="next-steps"></a>后续步骤

- [使用 C# 调用自定义视图](./call-endpoint-csharp.md)
- [使用 Java 调用自定义视图](./call-endpoint-java.md)
- [使用 NodeJs 调用自定义视图](./call-endpoint-nodejs.md)
- [使用 Python 调用自定义视图](./call-endpoint-python.md)

- [使用 C# SDK 调用自定义视图](./quickstarts/client-libraries.md?pivots=programming-language-csharp%253fpivots%253dprogramming-language-csharp)