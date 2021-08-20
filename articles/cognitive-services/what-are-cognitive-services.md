---
title: 什么是 Azure 认知服务？
titleSuffix: Azure Cognitive Services
description: 认知服务让每个开发人员都可以使用 AI，而无需机器学习和数据科学专业知识。 你只需要从应用程序中进行 API 调用即可向你的应用中添加看（高级图像搜索和识别）、听、说、搜索和决策制定的功能。
services: cognitive-services
author: nitinme
manager: nitinme
keywords: cognitive services, cognitive intelligence, cognitive solutions, ai services, cognitive understanding, cognitive features
ms.service: cognitive-services
ms.subservice: ''
ms.topic: overview
ms.date: 04/16/2021
ms.author: nitinme
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: 4badad1f7e22fcb82e122a56e219fc61973634a2
ms.sourcegitcommit: 7d63ce88bfe8188b1ae70c3d006a29068d066287
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/22/2021
ms.locfileid: "114451257"
---
# <a name="what-are-azure-cognitive-services"></a>什么是 Azure 认知服务？

Azure 认知服务是具有 REST API 和客户端库 SDK 的基于云的服务，可用于帮助你将认知智能构建到应用程序中。 即使你没有人工智能 (AI) 或数据科学技能，也可向应用程序添加认知功能。 Azure 认知服务包含各种 AI 服务，让你能够构建可以看、听、说、理解，甚至可以决策的认知解决方案。

## <a name="categories-of-cognitive-services"></a>认知服务类别

提供认知理解功能的认知服务主要分为五大类：

* 影像
* 语音
* 语言
* 决策
* 搜索

本文的以下部分提供了属于这五大类的服务的列表。

## <a name="vision-apis"></a>视觉 API

|服务名称|服务说明|
|:-----------|:------------------|
|[计算机视觉](./computer-vision/index.yml "计算机视觉")|使用计算机视觉服务，你可以访问用于处理图像并返回信息的高级认知算法。 请参阅[计算机视觉快速入门](./computer-vision/quickstarts-sdk/client-library.md)，了解如何开始服务。|
|[自定义视觉服务](./custom-vision-service/index.yml "自定义影像服务")|自定义视觉可用于生成、部署和改进您自己的图像分类器。 图像分类器是一种 AI 服务，它可以根据图像的视觉特征为图像应用标签。 |
|[人脸](./face/index.yml "人脸")| 使用人脸服务可访问高级人脸算法，从而实现人脸属性检测和识别。 请参阅[人脸快速入门](./face/quickstarts/client-libraries.md)，了解如何开始服务。|

## <a name="speech-apis"></a>语音 API

|服务名称|服务说明|
|:-----------|:------------------|
|[语音服务](./speech-service/index.yml "语音服务")|语音服务将支持语音的功能添加到应用程序。 语音服务包括各种功能，如语音到文本、文本到语音以及语音翻译等。|
<!--
|[Speaker Recognition API](./speech-service/speaker-recognition-overview.md "Speaker Recognition API") (Preview)|The Speaker Recognition API provides algorithms for speaker identification and verification.|
|[Bing Speech](./speech-service/how-to-migrate-from-bing-speech.md "Bing Speech") (Retiring)|The Bing Speech API provides you with an easy way to create speech-enabled features in your applications.|
|[Translator Speech](/azure/cognitive-services/translator-speech/ "Translator Speech") (Retiring)|Translator Speech is a machine translation service.|
-->
## <a name="language-apis"></a>语言 API

|服务名称|服务说明|
|:-----------|:------------------|
|[语言理解 LUIS](./luis/index.yml "语言理解")|语言理解 (LUIS) 是一种基于云的对话式 AI 服务，可在用户对话的自然语言文本中应用自定义机器学习智能，以便预测整体含义并提炼出相关的详细信息。 请参阅[人脸快速入门](./luis/luis-get-started-create-app.md)，了解如何开始服务。|
|[QnA Maker](./qnamaker/index.yml "QnA Maker")|通过 QnA Maker，可以从半结构化内容生成问答服务。 请参阅 [QnA Maker 快速入门](./qnamaker/quickstarts/create-publish-knowledge-base.md)，了解如何开始服务。|
|[文本分析](./text-analytics/index.yml "文本分析")| 文本分析提供对原始文本的自然语言处理，用于情绪分析、关键短语提取和语言检测。 请参阅[文本分析快速入门](./text-analytics/quickstarts/client-libraries-rest-api.md) ，了解如何开始服务。|
|[翻译](./translator/index.yml "转换器")|“翻译”近乎实时地提供基于机器的文本翻译。|

## <a name="decision-apis"></a>决策 API

|服务名称|服务说明|
|:-----------|:------------------|
|[异常检测器](./anomaly-detector/index.yml "异常检测器") |使用异常检测器可以监视并检测时序数据中的异常。 请参阅[异常探测器快速入门](./anomaly-detector/quickstarts/client-libraries.md)，了解如何开始服务。|
|[内容审查器](./content-moderator/overview.md "内容审查器")|内容审查器监视可能的冒犯性、不可取和危险内容。 请参阅[内容审查器快速入门](./content-moderator/client-libraries.md)，了解如何开始服务。|
|[个性化体验创建服务](./personalizer/index.yml "个性化体验创建服务")|个性化体验创建服务可让你选择要显示给用户的最佳体验，并从其实时行为中学习信息。 请参阅[个性化体验创建服务快速入门](./personalizer/quickstart-personalizer-sdk.md)，了解如何开始服务。|

## <a name="search-apis"></a>搜索 API

> [!NOTE]
> 正在查找 [Azure 认知搜索](../search/index.yml)？ 尽管它对某些任务使用认知服务，但它是一项支持其他方案的不同搜索技术。

|服务名称|服务说明|
|:-----------|:------------------|
|[必应新闻搜索](/azure/cognitive-services/bing-news-search/ "必应新闻搜索")|必应新闻搜索返回确定与用户查询相关的新闻文章列表。|
|[必应视频搜索](/azure/cognitive-services/Bing-Video-Search/ "必应视频搜索")|必应视频搜索返回确定与用户查询相关的视频列表。|
|[必应 Web 搜索](./bing-web-search/index.yml "必应 Web 搜索")|必应 Web 搜索返回确定与用户查询相关的搜索结果列表。|
|[必应自动建议](/azure/cognitive-services/Bing-Autosuggest "必应自动建议")|通过必应自动建议，可向必应发送部分搜索查询词，并取回建议的查询列表。|
|[必应自定义搜索](/azure/cognitive-services/bing-custom-search "必应自定义搜索")|借助必应自定义搜索，可以为关注的主题创建定制的搜索体验。|
|[必应实体搜索](/azure/cognitive-services/bing-entities-search/ "必应实体搜索")|必应实体搜索返回必应确定与用户查询相关的实体的相关信息。|
|[必应图像搜索](/azure/cognitive-services/bing-image-search "必应图像搜索")|必应图像搜索返回确定与用户查询相关的图像显示。|
|[必应视觉搜索](/azure/cognitive-services/bing-visual-search "必应视觉搜索")|必应视觉搜索返回有关图像的见解，例如在视觉上相似的图像、在图像中找到的产品的购物来源以及相关的搜索。|
|[必应当地企业搜索](/azure/cognitive-services/bing-local-business-search/ "必应当地企业搜索")| 必应当地企业搜索 API 使应用程序可以基于搜索查询查找有关当地企业的联系人和位置信息。|
|[必应拼写检查](/azure/cognitive-services/bing-spell-check/ "必应拼写检查")|使用必应拼写检查，可执行上下文语法和拼写检查。|

## <a name="get-started-with-cognitive-services"></a>认知服务入门

首先，让我们使用以下方法，通过实操性快速入门创建认知服务资源：

* [Azure 门户](cognitive-services-apis-create-account.md?tabs=multiservice%2Cwindows "Azure 门户")
* [Azure CLI](cognitive-services-apis-create-account-cli.md?tabs=windows "Azure CLI")
* [Azure SDK 客户端库](cognitive-services-apis-create-account-cli.md?tabs=windows "cognitive-services-apis-create-account-client-library?pivots=programming-language-csharp")
* [Azure 资源管理器 (ARM) 模板](./create-account-resource-manager-template.md?tabs=portal "Azure 资源管理器 (ARM) 模板")

## <a name="using-cognitive-services-in-different-development-environments"></a>在不同的开发环境中使用认知服务

通过 Azure 和认知服务，可以访问若干开发选项，例如：

* 自动化和集成工具，例如逻辑应用和 Power Automate。
* 部署选项，例如 Azure Functions 和应用服务。 
* 用于安全访问的认知服务 Docker 容器。
* Apache Spark、Azure Databricks、Azure Synapse Analytics 以及适用于大数据场景的 Azure Kubernetes 服务等工具。 

若要了解详细信息，请参阅[认知服务开发选项](./cognitive-services-development-options.md)。

<!--
## Subscription management

Once you are signed in with your Microsoft Account, you can access [My subscriptions](https://www.microsoft.com/cognitive-services/subscriptions "My subscriptions") to show the products you are using, the quota remaining, and the ability to add additional products to your subscription.

## Upgrade to unlock higher limits

All APIs have a free tier, which has usage and throughput limits.  You can increase these limits by using a paid offering and selecting the appropriate pricing tier option when deploying the service in the Azure portal. [Learn more about the offerings and pricing](https://azure.microsoft.com/pricing/details/cognitive-services/ "offerings and pricing"). You'll need to set up an Azure subscriber account with a credit card and a phone number. If you have a special requirement or simply want to talk to sales, click "Contact us" button at the top the pricing page.
-->

## <a name="using-cognitive-services-securely"></a>安全地使用认知服务

Azure 认知服务提供了分层的安全模型，包括通过 Azure Active Directory 凭据进行的[身份验证](authentication.md "身份验证")、有效的资源密钥以及 [Azure 虚拟网络](cognitive-services-virtual-networks.md "Azure 虚拟网络")。

## <a name="containers-for-cognitive-services"></a>认知服务的容器

 Azure 认知服务提供了多个 Docker 容器，可让你在本地使用 Azure 中提供的相同 API。 借助这些容器，你能够灵活地将认知服务移至更接近你的数据的位置，以满足合规性、安全性或其他操作目的。 详细了解[认知服务容器](cognitive-services-container-support.md "认知服务容器")。

## <a name="regional-availability"></a>区域可用性

认知服务中的 API 托管在不断扩大的 Microsoft 托管数据中心网络上。 你可以在 [Azure 区域列表](https://azure.microsoft.com/regions "Azure 区域列表")中找到每个 API 的区域可用性。

正在寻找我们尚不支持的区域？ 请在我们的 [UserVoice 论坛](https://feedback.azure.com/forums/932041-azure-cognitive-services "UserVoice 论坛")上填写功能申请，告诉我们你的需求。

## <a name="supported-cultural-languages"></a>支持的区域性语言

认知服务在服务级别支持各种区域性语言。 可以在[支持的语言列表](language-support.md "支持的语言列表")中找到每个 API 的语言可用性。

## <a name="certifications-and-compliance"></a>认证和合规性

认知服务已获得 CSA STAR 认证、FedRAMP 中等 和HIPAA BAA 等认证。 可以[下载](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942 "下载")认证进行自己的审核和安全评审。

若要了解隐私和数据管理，请访问[信任中心](https://servicetrust.microsoft.com/ "信任中心")。

## <a name="support"></a>支持

认知服务提供若干支持选项，可帮助你继续创建智能应用程序。 认知服务还有一个强大的开发人员社区，可以帮助回答你的特定问题。 有关你可以使用的选项的完整列表，请参阅[认知服务支持和帮助选项](cognitive-services-support-options.md "认知服务支持和帮助选项")。

## <a name="next-steps"></a>后续步骤

* [创建认知服务帐户](cognitive-services-apis-create-account.md "创建认知服务帐户")
* [认知服务文档中的新增内容](whats-new-docs.md "认知服务文档中的新增内容")
* [计划和管理认知服务的成本](plan-manage-costs.md)
