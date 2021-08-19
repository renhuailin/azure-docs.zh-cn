---
title: 如何为自定义模型生成训练数据集 - 表单识别器
titleSuffix: Azure Applied AI Services
description: 了解如何确保训练数据集是为训练表单识别器模型而优化的。
author: laujan
manager: nitinme
ms.service: applied-ai-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 07/27/2021
ms.author: lajanuar
ms.openlocfilehash: 47695a2445e0b61f972f6481471305ed5219b5d7
ms.sourcegitcommit: 5f659d2a9abb92f178103146b38257c864bc8c31
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/17/2021
ms.locfileid: "122326338"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>为自定义模型生成一个训练数据集

使用表单识别器自定义模型时，可以向[训练自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)操作提供你自己的训练数据，使该模型能够根据你特定于行业的表单进行训练。 遵循本指南了解如何收集和准备数据以有效地训练模型。

至少需要五个相同类型的已填充表单。

如果要使用手动标记的训练数据，则必须从至少五个相同类型的填充表单开始。 除了所需的数据集之外，还可以使用未标记的表单。

## <a name="custom-model-input-requirements"></a>自定义模型输入要求

首先，请确保训练数据集符合表单识别器的输入要求。

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>训练数据提示

请按照以下附加提示操作，进一步优化你的数据集以进行训练。

* 如果可以，请使用基于文本的 PDF 文档而不是基于图像的文档。 扫描的 PDF 作为图像处理。
* 对于填充表单，请使用填充了所有字段的示例。
* 在每个字段中使用具有不同值的表格。
* 如果表单图像质量较低，请使用较大的数据集（例如 10-15 张图像）。

## <a name="upload-your-training-data"></a>上传训练数据

将用于训练的表单文档组合在一起时，需要将其上传到 Azure blob 存储容器。 如果不知道如何使用容器创建 Azure 存储帐户，请按照 [Azure 门户的 Azure 存储快速入门](../../storage/blobs/storage-quickstart-blobs-portal.md)中的说明操作。 使用标准性能层。

如果要使用手动标记的数据，则还必须上传与训练文档对应的 .labels.js 和 .ocr.js 文件。 可以使用[示例标记工具](label-tool.md)（或你自己的 UI）生成这些文件。

### <a name="organize-your-data-in-subfolders-optional"></a>组织子文件夹中的数据（可选）

默认情况下，[训练自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync) API 将仅使用位于存储容器根目录下的表单文档。 但是，如果在 API 调用中指定子文件夹中的数据，则可以使用这些数据进行训练。 通常，[训练自定义模型](https://westus.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1/operations/TrainCustomModelAsync)调用的正文采用以下格式，其中 `<SAS URL>` 是容器的共享访问签名 URL：

```json
{
  "source":"<SAS URL>"
}
```

如果将以下内容添加到请求正文，则 API 将使用位于子文件夹中的文档进行训练。 `"prefix"` 字段是可选的，它将训练数据集限制为其路径以给定字符串开头的文件。 例如，`"Test"` 的值会导致 API 仅查看以“Test”一词开头的文件或文件夹。

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>后续步骤

现在，你已经了解了如何生成训练数据集，接下来请按照快速入门教程来训练自定义表单识别器模型，并在你的表单上开始使用它。

* [使用客户端库或 REST API 训练模型并提取表单数据](./quickstarts/client-library.md)
* [使用示例标签工具通过标签进行训练](label-tool.md)

## <a name="see-also"></a>请参阅

* [什么是表单识别器？](./overview.md)
