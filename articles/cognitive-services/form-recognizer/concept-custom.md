---
title: 自定义模型 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 了解与表单识别器 API 自定义模型相关的概念 - 用法和限制。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: lajanuar
ms.openlocfilehash: 60ead0dd0943a441a87b6298d27ba5533098300f
ms.sourcegitcommit: 34feb2a5bdba1351d9fc375c46e62aa40bbd5a1f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/10/2021
ms.locfileid: "111890647"
---
# <a name="form-recognizer-custom-models"></a>表单识别器自定义模型

表单识别器使用高级机器学习技术来分析和提取表单和文档中的数据。 表单识别器模型是提取的数据的表示形式，用作分析特定内容的参考。 有两种类型的表单识别器模型：

* 自定义模型。 表单识别器自定义模型表示从特定于业务的表单提取的数据。 自定义模型必须经过训练才能分析不同的表单数据。

* 预生成模型。 表单识别器当前支持收据、名片、标识卡和发票的预生成模型。 预生成模型从文档图像中检测并提取信息，并在结构化 JSON 输出中返回提取的数据。

## <a name="what-does-a-custom-model-do"></a>自定义模型的作用是什么？

使用表单识别器，你可以训练一个模型，该模型将从与你的用例相关的表单中提取信息。 你只需要五个相同类型的表单示例即可开始。 自定义模型可以使用或不使用标记数据集进行训练。

## <a name="create-use-and-manage-your-custom-model"></a>创建、使用和管理自定义模型

从概略性层面看，生成、训练和使用自定义模型的步骤如下：

> [!div class="nextstepaction"]
>[&#120783;. 收集训练数据集](build-training-data-set.md#custom-model-input-requirements)

生成自定义模型从建立训练数据集开始。 对于示例数据集，至少需要五个相同类型的已完成表单。 它们可以是不同的文件类型，并且包含文本和手写内容。 表单必须属于相同类型的文档，并遵循表单识别器的[输入要求](build-training-data-set.md#custom-model-input-requirements)。  

> [!div class="nextstepaction"]
> [&#120784;. 上传训练数据集](build-training-data-set.md#upload-your-training-data)

需要将训练数据上传到 Azure blob 存储容器。 如果不知道如何使用容器创建 Azure 存储帐户，请参阅 [Azure 门户的 Azure 存储快速入门](../../storage/blobs/storage-quickstart-blobs-portal.md)。 使用免费定价层 (F0) 试用该服务，稍后升级到用于生产的付费层。  

> [!div class="nextstepaction"]
>[&#120785;. 训练自定义模型](quickstarts/client-library.md#train-a-custom-model)

你可以在使用或不使用标记数据集的情况下[训练模型](quickstarts/client-library.md#train-a-custom-model)。 未标记的数据集仅依赖于布局 API 来检测和识别关键信息，而无需添加人工输入。 标记的数据集也依赖于布局 API，但包含了附加的人工输入，例如特定的标签和字段位置。 若要同时使用标记和未标记的数据，请从标记训练数据的至少五个相同类型的已完成表单开始，然后将未标记的数据添加到所需数据集。  

>[!div class="nextstepaction"]
>[&#120786;. 用自定义模型分析文档](quickstarts/client-library.md#analyze-forms-with-a-custom-model)

使用不属于训练数据集的表单测试新训练的模型。 可以继续执行进一步的训练，以提高自定义模型的性能。  

> [!div class="nextstepaction"]
>[&#120787;.管理自定义模型](quickstarts/client-library.md#manage-custom-models)

随时可以查看订阅下的所有自定义模型列表，检索有关特定自定义模型的信息，或从帐户中删除自定义模型。

## <a name="next-steps"></a>后续步骤

浏览我们的 API 参考文档，详细了解表单识别器客户端库。

> [!div class="nextstepaction"]
> [表单识别器 API 参考](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/AnalyzeWithCustomForm)
>
