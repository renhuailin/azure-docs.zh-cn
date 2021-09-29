---
title: 发布区域和终结点 - LUIS
description: Azure 门户中指定的区域就是你将在其中发布 LUIS 应用的区域，并会为此同一区域生成一个终结点 URL。
ms.service: cognitive-services
ms.subservice: language-understanding
author: aahill
ms.author: aahi
ms.topic: reference
ms.date: 05/27/2021
ms.custom: references_regions
ms.openlocfilehash: 7dc91bf74cf342d882a978c6e7b3a8e39d401644
ms.sourcegitcommit: f6e2ea5571e35b9ed3a79a22485eba4d20ae36cc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/24/2021
ms.locfileid: "128576234"
---
# <a name="authoring-and-publishing-regions-and-the-associated-keys"></a>创作和发布区域及关联的密钥

LUIS 门户支持 LUIS 创作区域。 若要将 LUIS 应用发布到多个区域，每个区域至少需要一个密钥。


<a name="luis-website"></a>

## <a name="luis-authoring-regions"></a>LUIS 创作区域

[!INCLUDE [portal consolidation](includes/portal-consolidation.md)]

LUIS 包含以下可用的创作区域：
    
* 澳大利亚东部
* 西欧
* 美国西部
* 瑞士北部


LUIS 有一个门户，[www.luis.ai](https://www.luis.ai)，可在任何地区使用。 仍必须在同一区域中创建和发布应用。 创作区域具有[配对故障转移区域](../../best-practices-availability-paired-regions.md)。

<a name="regions-and-azure-resources"></a>

## <a name="publishing-regions-and-azure-resources"></a>发布区域和 Azure 资源 

该应用将发布到与 LUIS 门户中添加的 LUIS 资源关联的所有区域。 例如，对于在 [www.luis.ai][www.luis.ai] 上创建的应用，如果你在 **westus** 中创建 LUIS 或认知服务资源并 [将其作为资源添加到该应用](luis-how-to-azure-subscription.md)，则该应用将发布到此区域中。

## <a name="public-apps"></a>公共应用
公共应用在所有区域中发布，以便有基于区域的 LUIS 资源密钥的用户可以在与其资源密钥关联的任何区域中访问该应用。

<a name="publishing-regions"></a>

## <a name="publishing-regions-are-tied-to-authoring-regions"></a>发布区域绑定到创作区域

创作区域中的应用仅可发布到对应的发布区域。 如果应用目前位于错误的创作区域中，请导出应用，然后将其导入发布区域对应的正确创作区域。

> [!NOTE]
> https://www.luis.ai 上创建的 LUIS 应用现可发布到除[欧洲](#publishing-to-europe)和[澳大利亚](#publishing-to-australia)区域之外的所有终结点。

## <a name="publishing-to-europe"></a>发布到欧洲

 全球区域 | 创作 API 区域 | 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| 欧洲 | `westeurope`| 法国中部<br>`francecentral`     | `https://francecentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 欧洲 | `westeurope`| 北欧<br>`northeurope`     | `https://northeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 欧洲 | `westeurope`| 西欧<br>`westeurope`    |  `https://westeurope.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 欧洲 | `westeurope`| 英国南部<br>`uksouth`    |  `https://uksouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 欧洲 | `westeurope`| 瑞士北部<br>`switzerlandnorth`    |  `https://switzerlandnorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |
| 欧洲 | `westeurope`| 挪威东部<br>`norwayeast`    |  `https://norwayeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="publishing-to-australia"></a>发布到澳大利亚

 全球区域 | 创作 API 区域 | 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| 澳大利亚 | `australiaeast` | 澳大利亚东部<br>`australiaeast`     |  `https://australiaeast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY`   |

## <a name="other-publishing-regions"></a>其他发布区域

 全球区域 | 创作 API 区域 | 发布和查询区域<br>`API region name`   |  终结点 URL 格式   |
|-----|------|------|------|
| 非洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 南非北部<br>`southafricanorth` |  `https://southafricanorth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 印度中部<br>`centralindia` |  `https://centralindia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 东亚<br>`eastasia`     |  `https://eastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| Japan East<br>`japaneast`     |   `https://japaneast.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 日本西部<br>`japanwest`     |   `https://japanwest.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 韩国中部<br>`koreacentral`     |   `https://koreacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| Southeast Asia<br>`southeastasia`     |   `https://southeastasia.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 亚洲 | `westus`<br>[www.luis.ai][www.luis.ai]| 阿联酋北部<br>`northuae`     |   `https://northuae.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 加拿大中部<br>`canadacentral`     |   `https://canadacentral.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | Central US<br>`centralus`     |   `https://centralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国东部<br>`eastus`      |  `https://eastus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国东部 2<br>`eastus2`     |  `https://eastus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国中北部<br>`northcentralus`  |  `https://northcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国中南部<br>`southcentralus`  |  `https://southcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国中西部<br>`westcentralus`    |  `https://westcentralus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 | `westus`<br>[www.luis.ai][www.luis.ai] | 美国西部<br>`westus`  |   `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国西部 2<br>`westus2`    |  `https://westus2.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 北美 |`westus`<br>[www.luis.ai][www.luis.ai] | 美国西部 3<br>`westus3`    |  `https://westus3.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |
| 南美洲 | `westus`<br>[www.luis.ai][www.luis.ai] | 巴西南部<br>`brazilsouth`    |  `https://brazilsouth.api.cognitive.microsoft.com/luis/v2.0/apps/YOUR-APP-ID?subscription-key=YOUR-SUBSCRIPTION-KEY` |

## <a name="endpoints"></a>终结点

详细了解[创作和预测终结点](developer-reference-resource.md)。

## <a name="failover-regions"></a>故障转移区域

每个区域都有一个要故障转移到的次要区域。 欧洲在欧洲内部故障转移，澳大利亚在澳大利亚内部故障转移。

创作区域具有[配对故障转移区域](../../best-practices-availability-paired-regions.md)。

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [预生成实体参考](./luis-reference-prebuilt-entities.md)

 [www.luis.ai]: https://www.luis.ai
