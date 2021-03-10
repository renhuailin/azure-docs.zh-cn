---
title: 快速入门：计算机视觉 REST API
titleSuffix: Azure Cognitive Services
description: 通过本快速入门实现计算机视觉 REST API 入门。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: quickstart
ms.date: 12/02/2020
ms.author: pafarley
ms.custom: seodec18
ms.openlocfilehash: c8221a15ddd92276c105f1e441c8da722655d576
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/07/2021
ms.locfileid: "102445467"
---
使用计算机视觉 REST API：

* 分析图像中的标记、文本说明、人脸、成人内容，等等。
* 使用 Read API 读取印刷文本和手写文本。
* 使用智能裁剪生成缩略图

> [!NOTE]
> 此快速入门使用 cURL 命令来调用 REST API。 也可以使用编程语言调用 REST API。 请参阅 GitHub 示例，查看 [C#](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/dotnet/ComputerVision/REST)、[Python](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/python/ComputerVision/REST)、[Java](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/java/ComputerVision/REST)、[JavaScript](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/javascript/ComputerVision/REST) 和 [Go](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/go/ComputerVision/REST) 的相关示例。

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/) 
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision"  title="创建计算机视觉资源"  target="_blank">创建计算机视觉资源 </a>，获取密钥和终结点。 部署后，单击“转到资源”。
  * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到计算机视觉服务。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
  * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。
* 已安装 [cURL](https://curl.haxx.se/)

## <a name="analyze-an-image"></a>分析图像

若要分析图像的各种视觉特征，请执行以下步骤：

1. 将以下命令复制到文本编辑器中。
1. 必要时在命令中进行如下更改：
    1. 将 `<subscriptionKey>` 的值替换为你的订阅密钥。
    1. 将请求 URL 的第一部分 (`westcentralus`) 替换为你自己的终结点 URL 中的文本。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. （可选）将请求正文中的图像 URL (`http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\`) 更改为要分析的其他图像的 URL。
1. 打开命令提示符窗口。
1. 将文本编辑器中的命令粘贴到命令提示符窗口，然后运行命令。

```bash
curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -H "Content-Type: application/json" "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/analyze?visualFeatures=Categories,Description&details=Landmarks" -d "{\"url\":\"http://upload.wikimedia.org/wikipedia/commons/3/3c/Shaki_waterfall.jpg\"}"
```

### <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例应用程序会在命令提示符窗口中分析和显示成功响应，如下例所示：

```json
{
  "categories": [
    {
      "name": "outdoor_water",
      "score": 0.9921875,
      "detail": {
        "landmarks": []
      }
    }
  ],
  "description": {
    "tags": [
      "nature",
      "water",
      "waterfall",
      "outdoor",
      "rock",
      "mountain",
      "rocky",
      "grass",
      "hill",
      "covered",
      "hillside",
      "standing",
      "side",
      "group",
      "walking",
      "white",
      "man",
      "large",
      "snow",
      "grazing",
      "forest",
      "slope",
      "herd",
      "river",
      "giraffe",
      "field"
    ],
    "captions": [
      {
        "text": "a large waterfall over a rocky cliff",
        "confidence": 0.916458423253597
      }
    ]
  },
  "requestId": "b6e33879-abb2-43a0-a96e-02cb5ae0b795",
  "metadata": {
    "height": 959,
    "width": 1280,
    "format": "Jpeg"
  }
}
```

## <a name="read-printed-and-handwritten-text"></a>读取印刷体文本和手写文本

计算机视觉可以读取图像中的可见文本，并将其转换为字符流。 有关文本识别的详细信息，请参阅[光学字符识别 (OCR)](../concept-recognizing-text.md#read-api) 概念文档。

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
curl -v -X POST "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyze?language={string}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: <subscription key>" --data-ascii "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/a/af/Atomist_quote_from_Democritus.png/338px-Atomist_quote_from_Democritus.png\"}"
```

响应将包含一个 `Operation-Location` 标头，其值为唯一的 URL。 使用此 URL 可以查询读取操作的结果。 该 URL 在 48 小时后过期。

1. 将以下命令复制到文本编辑器中。
1. 将该 URL 替换为在上一步骤中复制的 `Operation-Location` 值。
1. 必要时在命令中进行如下更改：
    1. 将 `<subscriptionKey>` 的值替换为你的订阅密钥。
1. 打开命令提示符窗口。
1. 将文本编辑器中的命令粘贴到命令提示符窗口，然后运行命令。

```bash
curl -v -X GET "https://westcentralus.api.cognitive.microsoft.com/vision/v3.1/read/analyzeResults/{operationId}" -H "Ocp-Apim-Subscription-Key: {subscription key}" --data-ascii "{body}" 
```

### <a name="examine-the-response"></a>检查响应

成功的响应以 JSON 格式返回。 示例应用程序会在命令提示符窗口中分析和显示成功响应，如下例所示：

```json
{
  "status": "succeeded",
  "createdDateTime": "2019-10-03T14:32:04.236Z",
  "lastUpdatedDateTime": "2019-10-03T14:38:14.852Z",
  "analyzeResult": {
    "version": "v3.0",
    "readResults": [
      {
        "page": 1,
        "language": "en",
        "angle": 49.59,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              202,
              618,
              2047,
              643,
              2046,
              840,
              200,
              813
            ],
            "text": "Our greatest glory is not",
            "words": [
              {
                "boundingBox": [
                  204,
                  627,
                  481,
                  628,
                  481,
                  830,
                  204,
                  829
                ],
                "text": "Our",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  519,
                  628,
                  1057,
                  630,
                  1057,
                  832,
                  518,
                  830
                ],
                "text": "greatest",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1114,
                  630,
                  1549,
                  631,
                  1548,
                  833,
                  1114,
                  832
                ],
                "text": "glory",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1586,
                  631,
                  1785,
                  632,
                  1784,
                  834,
                  1586,
                  833
                ],
                "text": "is",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1822,
                  632,
                  2115,
                  633,
                  2115,
                  835,
                  1822,
                  834
                ],
                "text": "not",
                "confidence": 0.164
              }
            ]
          },
...
        ]
      },
      {
        "page": 2,
        "language": "en",
        "angle": 1.32,
        "width": 600,
        "height": 400,
        "unit": "pixel",
        "lines": [
          {
            "boundingBox": [
              1612,
              903,
              2744,
              935,
              2738,
              1139,
              1607,
              1107
            ],
            "text": "in never failing ,",
            "words": [
              {
                "boundingBox": [
                  1611,
                  934,
                  1707,
                  933,
                  1708,
                  1147,
                  1613,
                  1147
                ],
                "text": "in",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  1753,
                  933,
                  2132,
                  930,
                  2133,
                  1144,
                  1754,
                  1146
                ],
                "text": "never",
                "confidence": 0.999
              },
              {
                "boundingBox": [
                  2162,
                  930,
                  2673,
                  927,
                  2674,
                  1140,
                  2164,
                  1144
                ],
                "text": "failing",
                "confidence": 0.164
              },
              {
                "boundingBox": [
                  2703,
                  926,
                  2788,
                  926,
                  2790,
                  1139,
                  2705,
                  1140
                ],
                "text": ",",
                "confidence": 0.164
              }
            ]
          }
        ]
      }
    ]
  }
}
```

## <a name="generate-a-thumbnail"></a>生成缩略图

可以通过计算机视觉，使用智能裁剪生成缩略图。 可以指定所需的高度和宽度，可以与输入图像的纵横比不同。 计算机视觉使用智能裁剪来智能识别感兴趣的区域并围绕该区域生成裁剪坐标。
 
要创建和运行示例，请执行以下步骤：

1. 将以下命令复制到文本编辑器中。
1. 必要时在命令中进行如下更改：
    1. 将 `<subscriptionKey>` 的值替换为你的订阅密钥。
    1. 将 `<thumbnailFile>` 的值替换为用于保存返回的缩略图的路径和名称。
    1. 将请求 URL 的第一部分 (`westcentralus`) 替换为你自己的终结点 URL 中的文本。
        [!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]
    1. （可选）将请求正文 (`https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\`) 中的图像 URL 更改为要从中生成缩略图的其他图像的 URL。
1. 打开命令提示符窗口。
1. 将文本编辑器中的命令粘贴到命令提示符窗口。
1. 按 Enter 运行此程序。

    ```bash
    curl -H "Ocp-Apim-Subscription-Key: <subscriptionKey>" -o <thumbnailFile> -H "Content-Type: application/json" "https://westus.api.cognitive.microsoft.com/vision/v3.1/generateThumbnail?width=100&height=100&smartCropping=true" -d "{\"url\":\"https://upload.wikimedia.org/wikipedia/commons/thumb/5/56/Shorkie_Poo_Puppy.jpg/1280px-Shorkie_Poo_Puppy.jpg\"}"
    ```

### <a name="examine-the-response"></a>检查响应

成功的响应会将缩略图写入 `<thumbnailFile>` 中指定的文件。 如果请求失败，则响应包含错误代码和消息，以帮助确定出错的地方。 如果请求似乎是成功的，但创建的缩略图不是有效的图像文件，则可能是因为订阅密钥无效。


## <a name="next-steps"></a>后续步骤

深入了解计算机视觉 API。 要快速体验计算机视觉 API，请尝试使用 [Open API 测试控制台](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b/console)。

> [!div class="nextstepaction"]
> [探索计算机视觉 API](https://westcentralus.dev.cognitive.microsoft.com/docs/services/computer-vision-v3-1-ga/operations/56f91f2e778daf14a499f21b)
