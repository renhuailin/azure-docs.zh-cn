---
title: 创建“翻译”资源
titleSuffix: Azure Cognitive Services
description: 本文将演示如何创建 Azure 认知服务 Translator 资源并获取订阅密钥和终结点 URL。
services: cognitive-services
author: laujan
ms.author: lajanuar
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: how-to
ms.date: 02/16/2021
ms.openlocfilehash: c21d78e9731bc1711e62d26d6cca269868ac1714
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121744719"
---
# <a name="create-a-translator-resource"></a>创建“翻译”资源

在本文中，你将了解如何在 Azure 门户中创建 Translator 资源。 [Azure Translator](translator-overview.md) 是一种基于云的机器翻译服务，是 [Azure 认知服务](../what-are-cognitive-services.md)系列 REST API 的一部分。 Azure 资源是你创建的服务实例。 对 Azure 服务的所有 API 请求都需要一个终结点 URL 和一个只读订阅密钥来对访问进行身份验证。

## <a name="prerequisites"></a>先决条件

首先，需要一个有效的 [**Azure 帐户**](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以 [**创建有效期为 12 个月的免费订阅**](https://azure.microsoft.com/free/)。

## <a name="translator-resource-types"></a>Translator 资源类型

可以通过两种不同的资源类型访问 Translator 服务：

* 单服务资源类型允许访问单个服务 API 密钥和终结点。  

* 多服务资源类型允许使用单个 API 密钥和终结点访问多个认知服务。 认知服务资源当前可用于以下服务：
  * 语言（[Translator](../translator/translator-overview.md)、[语言理解 (LUIS)](../luis/what-is-luis.md)、[文本分析](../text-analytics/overview.md)）  
  * 视觉（[计算机视觉](../computer-vision/overview.md)），（[人脸](../face/overview.md)）  
  * 决策（[内容审查器](../content-moderator/overview.md)）  

## <a name="create-your-resource"></a>创建资源

* 直接导航到 Azure 门户中的 [**创建 Translator**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation) 页面，以完成项目详细信息。

* 直接导航到 Azure 门户中的 [**创建认知服务**](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)页面，以完成项目详细信息。

>[!TIP]
>如果愿意，可以从 Azure 门户主页开始，开始“创建”过程，如下所示：
>
> 1. 导航到 [**Azure 门户**](https://ms.portal.azure.com/#home)主页。
> 1. 从 Azure 服务菜单中选择“创建一个资源”。
>1. 在“搜索市场”搜索框中，输入并选择“Translator”（单服务资源）或“认知服务”（多服务资源）。  请参阅上面的[选择你的资源类型](#create-your-resource)。
> 1. 选择“创建”，随即会转到“项目详细信息”页。
><br/><br/>

## <a name="complete-your-project-and-instance-details"></a>完成项目和实例详细信息

1. **订阅**。 选择一个可用的 Azure 订阅。

1. 资源组。 你选择的 Azure 资源组用作新资源的虚拟容器。 可以创建新资源组，或将资源添加到共享相同生命周期、权限和策略的预先存在的资源组。

1. 资源区域。 选择“全球”，除非你的业务或应用程序需要特定区域。 翻译器是非区域性服务 — 不依赖特定的 Azure 区域。 请参阅 [Azure 中的区域和可用性区域](../../availability-zones/az-overview.md)。

1. **名称**。 输入为资源选择的名称。 所选名称在 Azure 中必须唯一。

> [!NOTE]
> 如果你使用的是需要自定义域终结点的 Translator 功能，则在“名称”字段中输入的值将是该终结点的自定义域名参数。

5. **定价层**。 选择满足你需求的[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/translator)：

   * 每个订阅都有一个免费层。
   * 免费层具有与付费计划相同的特征和功能，并且不会过期。
   * 每个帐户只能有一个免费订阅。</li></ul>

1. 如果已创建多服务资源，则需要通过复选框确认其他使用细节。

1. 选择“查看 + 创建”  。

1. 查看服务条款，然后选择“创建”以部署资源。

1. 成功部署资源后，选择“转到资源”。

### <a name="authentication-keys-and-endpoint-url"></a>身份验证密钥和终结点 URL

所有认知服务 API 请求都需要终结点 URL 和只读密钥才能进行身份验证。

* 身份验证密钥。 密钥是在每次请求翻译服务时传递的唯一字符串。 可以通过查询字符串参数传递密钥，或在 HTTP 请求标头中指定密钥。

* 终结点 URL。 请在 API 请求中使用“全球”终结点，除非你需要特定的 Azure 区域。 *请参阅*[基 URL](reference/v3-0-reference.md#base-urls)。 “全球”终结点 URL 为 `api.cognitive.microsofttranslator.com`。

## <a name="get-your-authentication-keys-and-endpoint"></a>获取身份验证密钥和终结点

1. 部署新资源后，选择“转到资源”或直接导航到资源页。
1. 在左侧导航栏中的“资源管理”下，选择“密钥和终结点”。
1. 将订阅密钥和终结点 URL 复制并粘贴到方便的位置（例如 Microsoft 记事本）。

:::image type="content" source="../media/cognitive-services-apis-create-account/get-cog-serv-keys.png" alt-text="获取密钥和终结点。":::

## <a name="how-to-delete-a--resource-or-resource-group"></a>如何删除资源或资源组

> [!Warning]
> 删除资源组会删除组中包含的所有资源。

若要删除认知服务或 Translator 资源，可以删除资源或删除资源组。

若要删除资源，请执行以下操作：

1. 在 Azure 门户中导航到你的资源组。
1. 选择相邻复选框，选择要删除的资源。
1. 从右边缘附近的顶部菜单中选择“删除”。
1. 在“已删除资源”对话框中，键入“是”。
1. 选择“删除”。

若要删除资源组，请执行以下操作：

1. 在 Azure 门户中导航到你的资源组。
1. 从左边缘附近的顶部菜单栏中选择“删除资源组”。
1. 输入资源组名称并选择“删除”来确认删除请求。

## <a name="how-to-get-started-with-translator"></a>如何开始使用 Translator

在本快速入门中，你将了解如何将 Translator 服务与 REST API 配合使用。

> [!div class="nextstepaction"]
> [Translator 入门](quickstart-translator.md)

## <a name="more-resources"></a>更多资源

* [Microsoft Translator 代码示例](https://github.com/MicrosoftTranslator)。  GitHub 上提供了多语言 Translator 代码示例。
* [Microsoft Translator 支持论坛](https://www.aka.ms/TranslatorForum)
* [Azure 入门（3 分钟视频）](https://azure.microsoft.com/get-started/?b=16.24)