---
title: 快速入门：光学字符识别 REST API
titleSuffix: Azure Cognitive Services
description: 在本快速入门中，开始使用光学字符识别 REST API。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: include
ms.date: 04/19/2021
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: ceeb1804c9332d9e0d3e11336ff92e8aacc8516c
ms.sourcegitcommit: c27f71f890ecba96b42d58604c556505897a34f3
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/05/2021
ms.locfileid: "129585229"
---
使用光学字符识别 REST API 读取印刷体文本和手写文本。

> [!NOTE]
> 此快速入门使用 cURL 命令来调用 REST API。 也可以使用编程语言调用 REST API。 请参阅 GitHub 示例，查看 [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST)、[Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST)、[Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST)、[JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) 和 [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST) 的相关示例。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/) 
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 已安装 [cURL](https://curl.haxx.se/)


## <a name="extract-printed-and-handwritten-text"></a>提取印刷体文本和手写文本

OCR 服务可以提取图像或文档中的可见文本，并将其转换为字符流。 有关文本提取的详细信息，请参阅[光学字符识别 (OCR) 概述](../overview-ocr.md)。


### <a name="call-the-read-api"></a>调用读取 API

要创建和运行示例，请执行以下步骤：

1. 将以下命令复制到文本编辑器中。
1. 必要时在命令中进行如下更改：
    1. 将 `<subscriptionKey>` 的值替换为你的订阅密钥。
    1. 将请求 URL 的第一部分 (`westcentralus`) 替换为你自己的终结点 URL 中的文本。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. （可选）将请求正文中的图像 URL (`https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\`) 更改为要分析的其他图像的 URL。
1. 打开命令提示符窗口。
1. 将文本编辑器中的命令粘贴到命令提示符窗口，然后运行命令。

```bash
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyze" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

响应将包含一个 `Operation-Location` 标头，其值为唯一的 URL。 使用此 URL 可以查询读取操作的结果。 该 URL 在 48 小时后过期。

### <a name="how-to-use-preview-features"></a>如何使用预览功能
有关预览版语言和功能，请参阅[如何指定模型版本](../Vision-API-How-to-Topics/call-read-api.md#determine-how-to-process-the-data-optional)以使用最新预览版。 预览版模型包含对当前 GA 语言和功能的任何增强功能。

### <a name="get-read-results"></a>获取读取结果

1. 将以下命令复制到文本编辑器中。
1. 将该 URL 替换为在上一步骤中复制的 `Operation-Location` 值。
1. 必要时在命令中进行如下更改：
    1. 将 `<subscriptionKey>` 的值替换为你的订阅密钥。
1. 打开命令提示符窗口。
1. 将文本编辑器中的命令粘贴到命令提示符窗口，然后运行命令。

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.2/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例应用程序会在命令提示符窗口中分析和显示成功响应，如下例所示：

```json
{
  "status": "succeeded",
  "createdDateTime": "2021-04-08T21:56:17.6819115+00:00",
  "lastUpdatedDateTime": "2021-04-08T21:56:18.4161316+00:00",
  "analyzeResult": {
    "version": "3.2",
    "readResults": [
      {
        "page": 1,
        "angle": 0,
        "width": 338,
        "height": 479,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              25,
              14,
              318,
              14,
              318,
              59,
              25,
              59
            ],
            "text": "NOTHING",
            "appearance": {
              "style": {
                "name": "other",
                "confidence": 0.971
              }
            },
            "words": [
              {
                "boundingBox": [
                  27,
                  15,
                  294,
                  15,
                  294,
                  60,
                  27,
                  60
                ],
                "text": "NOTHING",
                "confidence": 0.994
              }
            ]
          }
        ]
      }
    ]
  }
}

```



## <a name="next-steps"></a>后续步骤

在本快速入门中，你学习了如何调用读取 REST API。 接下来，详细了解读取 API 功能。

> [!div class="nextstepaction"]
>[调用读取 API](../Vision-API-How-to-Topics/call-read-api.md)

* [OCR 概述](../overview-ocr.md)
