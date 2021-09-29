---
title: 认知服务和机器学习
titleSuffix: Azure Cognitive Services
description: 了解在哪些情况下适合将 Azure 认知服务与其他用于机器学习的 Azure 产品/服务配合使用。
services: cognitive-services
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 08/22/2019
ms.openlocfilehash: e723089b0e248b3bab167b985bc410b3e00d4dc6
ms.sourcegitcommit: 0770a7d91278043a83ccc597af25934854605e8b
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/13/2021
ms.locfileid: "124788261"
---
# <a name="cognitive-services-and-machine-learning"></a>认知服务和机器学习

认知服务提供机器学习功能以解决常见问题，例如分析文本以获取其情感情绪，或分析图像以识别物体或人脸。 无需机器学习或数据科学方面的专业知识就能使用这些服务。 

[认知服务](./what-are-cognitive-services.md)是一组服务，每个服务都支持不同的通用预测功能。 服务分为不同类别，以帮助你找到合适的服务。 

|服务类别|目标|
|--|--|
|[决策](https://azure.microsoft.com/services/cognitive-services/directory/decision/)|构建应用，用于呈现有助于做出明智和高效决策的建议。|
|[语言](https://azure.microsoft.com/services/cognitive-services/directory/lang/)|允许应用使用预建脚本处理自然语言、评估情绪及学习如何识别用户想要的内容。|
|[搜索](https://azure.microsoft.com/services/cognitive-services/directory/search/)|将必应搜索 API 添加到应用中，并利用单一 API 调用梳理数十亿网页、图像、视频和新闻的能力。|
|[语音](https://azure.microsoft.com/services/cognitive-services/directory/speech/)|将语音转换为文本，将文本转换为自然语音。 从一种语言翻译成另一种语言，并启用说话人验证和识别。|
|[影像](https://azure.microsoft.com/services/cognitive-services/directory/vision/)|识别和确定你的图片、视频和数字墨迹内容，为它们添加描述文字和编制索引，并审查这些内容。|

在以下情况时使用认知服务：

* 可以使用通用解决方案。
* 从编程 REST API 或 SDK 访问解决方案。 

在以下情况时使用另一种和机器学习解决方案：

* 需要选择算法并需要针对非常具体的数据进行训练。

## <a name="what-is-machine-learning"></a>什么是机器学习？

机器学习是一种概念，你可以将数据与算法结合在一起，以解决特定需求。 对数据和算法进行训练后，输出是可以再次用于其他数据的模型。 已训练的模型可根据新数据提供见解。 

构建机器学习系统的过程需要一些机器学习或数据科学方面的知识。

机器学习是使用 [Azure 机器学习 (AML) 产品和服务](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning?context=azure%2fmachine-learning%2fstudio%2fcontext%2fml-context)提供的。

## <a name="what-is-a-cognitive-service"></a>什么是认知服务？

认知服务提供了机器学习解决方案中的部分或全部组件：数据、算法和训练模型。 这些服务旨在要求你具备有关数据的一般知识，而无需具备机器学习或数据科学方面的经验。 这些服务同时提供 REST API 和基于语言的 SDK。 因此，你需要具备编程语言知识才能使用该服务。

## <a name="how-are-cognitive-services-and-azure-machine-learning-aml-similar"></a>认知服务和 Azure 机器学习 (AML) 的相似之处是什么？

两者的最终目标都是应用人工智能 (AI) 以增强业务运营，不过，它们在各自的产品/服务中提供此服务的方式有所不同。 

通常，受众不同：

* 认知服务适用于没有机器学习经验的开发人员。
* Azure 机器学习是为数据科学家量身定制的。 

## <a name="how-is-a-cognitive-service-different-from-machine-learning"></a>认知服务与机器学习有何不同？

认知服务提供已训练的模型。 该模型将数据和算法结合在一起，可从 REST API 或 SDK 获得。 你可以在几分钟内实现此服务，具体取决于你的方案。  认知服务提供针对常见问题的答案，例如文本中的关键短语或图像中的项目标识。 

机器学习是一个通常需要较长时间才能成功实现的过程。 这些时间花费在数据收集、清理、转换、算法选择、模型训练和部署上，以达到认知服务所提供的相同级别的功能。 通过机器学习，可以为高度专业化和/或特定的问题提供答案。 机器学习问题需要熟悉相关问题的特定主题和数据，以及数据科学方面的专业知识。

## <a name="what-kind-of-data-do-you-have"></a>你拥有哪种类型的数据？

认知服务作为一组服务，对于已训练的模型可能不需要、需要部分或需要全部自定义数据。 

### <a name="no-additional-training-data-required"></a>无需其他训练数据

提供完全训练的模型的服务可被视为不透明框。 你无需知道它们的工作方式或用于训练它们的数据。 你可以将数据引入已完全训练的模型，以获得预测。 

### <a name="some-or-all-training-data-required"></a>需要部分或全部训练数据

某些服务允许引入自己的数据，然后训练模型。 这样，就可以使用服务的数据和算法通过自己的数据来扩展模型。 输出可以满足你的需求。 导入自己的数据时，可能需要以特定于服务的方式标记数据。 例如，如果要训练模型以识别花朵，则可以提供花朵图像的目录以及每个图像中花朵的位置以训练模型。 

服务可能允许提供数据以增强其自身的数据。 服务可能需要提供数据。 

### <a name="real-time-or-near-real-time-data-required"></a>需要实时或准实时的数据

服务可能需要实时或准实时数据来构建有效的模型。 这些服务处理大量的模型数据。 

## <a name="service-requirements-for-the-data-model"></a>数据模型的服务要求

以下数据根据服务允许或需要的数据类型对每种服务进行了分类。

|认知服务|无需训练数据|你提供部分或全部训练数据|实时或准实时数据收集|
|--|--|--|--|
|[异常检测器](./Anomaly-Detector/overview.md)|x|x|x|
|必应搜索 |x|||
|[计算机视觉](./computer-vision/overview.md)|x|||
|[内容审查器](./Content-Moderator/overview.md)|x||x|
|[自定义视觉](./custom-vision-service/overview.md)||x||
|[人脸](./Face/Overview.md)|x|x||
|[墨迹识别器](/previous-versions/azure/cognitive-services/Ink-Recognizer/overview)|x|x||
|[语言理解 (LUIS)](./LUIS/what-is-luis.md)||x||
|[个性化体验创建服务](./personalizer/what-is-personalizer.md)|x*|x*|x|
|[QnA Maker](./QnAMaker/Overview/overview.md)||x||
|[说话人识别器](./speech-service/speaker-recognition-overview.md)||x||
|[语音文本转语音 (TTS)](speech-service/text-to-speech.md)|x|x||
|[语音语音转文本 (STT)](speech-service/speech-to-text.md)|x|x||
|[语音翻译](speech-service/speech-translation.md)|x|||
|[文本分析](./text-analytics/overview.md)|x|||
|[翻译](./translator/translator-overview.md)|x|||
|[翻译器 - 自定义翻译器](./translator/custom-translator/overview.md)||x||

*个性化体验创建服务仅需要服务（在实时运行中）收集的训练数据来评估策略和数据。 个性化体验创建服务不需要大型历史数据集即可进行前期或批量训练。 

## <a name="where-can-you-use-cognitive-services"></a>在哪里可以使用认知服务？
 
这些服务可在任何可进行 REST API 或 SDK 调用的应用程序中使用。 应用程序的示例包括网站、机器人、虚拟或混合现实、桌面和移动应用程序。 

## <a name="how-is-azure-cognitive-search-related-to-cognitive-services"></a>Azure 认知搜索与认知服务有何关联？

[Azure 认知搜索](../search/search-what-is-azure-search.md)是一种单独的云搜索服务，可选择使用认知服务将图像和自然语言处理添加到索引工作负荷中。 认知服务通过包装单个 API 的[内置技能](../search/cognitive-search-predefined-skills.md)在 Azure 认知搜索中公开。 你可以使用免费资源进行演练，但计划为较大的卷创建并附加一个[可计费资源](../search/cognitive-search-attach-cognitive-services.md)。

## <a name="how-can-you-use-cognitive-services"></a>如何使用认知服务？

每个服务都提供有关你的数据的信息。 可以将服务组合在一起以形成链式解决方案，例如将语音（音频）转换为文本，将文本翻译为多种语言，然后使用翻译后的语言从知识库中获取答案。 可以直接使用认知服务创建智能解决方案，也可以将其与传统的机器学习项目相结合，以补充模型或加速开发过程。 

为其他机器学习工具提供导出模型的认知服务：

|认知服务|模型信息|
|--|--|
|[自定义视觉](./custom-vision-service/overview.md)|[导出](./custom-vision-service/export-model-python.md)用于 Android 的 Tensorflow、用于 iOS11 的 CoreML、用于 Windows ML 的 ONNX|

## <a name="learn-more"></a>了解详细信息

* [体系结构指南 - Microsoft 的机器学习产品有哪些？](/azure/architecture/data-guide/technology-choices/data-science-and-machine-learning)
* [机器学习 - 深度学习与机器学习简介](../machine-learning/concept-deep-learning-vs-machine-learning.md)

## <a name="next-steps"></a>后续步骤

* 在 [Azure 门户](cognitive-services-apis-create-account.md)或 [Azure CLI](./cognitive-services-apis-create-account-cli.md) 中创建认知服务帐户。
* 了解如何对认知服务进行[身份验证](authentication.md)。
* 使用[诊断日志记录](diagnostic-logging.md)来确定和调试问题。 
* 在 Docker [容器](cognitive-services-container-support.md)中部署认知服务。
* 通过[服务更新](https://azure.microsoft.com/updates/?product=cognitive-services)随时保持最新状态。