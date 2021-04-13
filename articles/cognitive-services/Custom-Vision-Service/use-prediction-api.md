---
title: 使用分类器和预测终结点以编程的方式测试图像 - 自定义视觉
titleSuffix: Azure Cognitive Services
description: 了解如何借助自定义影像服务分类器使用 API 以编程方式测试图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: pafarley
ms.custom: devx-track-csharp
ms.openlocfilehash: 7f1939536e033d2cf964dd2f4ee562e4ee20061b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "88934746"
---
# <a name="use-your-model-with-the-prediction-api"></a>配合使用模型与预测 API

训练模型后，可以通过将图像提交到预测 API 终结点来以编程方式测试这些图像。

> [!NOTE]
> 本文档演示如何使用 C# 将图像提交到预测 API。 有关更多信息和示例，请参阅[预测 API 参考](https://southcentralus.dev.cognitive.microsoft.com/docs/services/Custom_Vision_Prediction_3.0/operations/5c82db60bf6a2b11a8247c15)。

## <a name="publish-your-trained-iteration"></a>发布已训练的迭代

从[自定义影像服务网页](https://customvision.ai)，选择你的项目，然后选择“性能”选项卡。

若要将图像提交到预测 API，首先需要发布迭代以供预测，这可以通过选择“发布”并为已发布的迭代指定名称来完成。 这将使自定义视觉 Azure 资源的预测 API 可以访问你的模型。

![显示性能选项卡，其“发布”按钮周围有红色矩形框。](./media/use-prediction-api/unpublished-iteration.png)

成功发布模型后，你将在左侧侧栏的迭代旁边看到一个“已发布”标签，其名称显示在迭代的说明中。

![显示性能选项卡，其“已发布”标签和已发布迭代的名称周围有一个红色矩形框。](./media/use-prediction-api/published-iteration.png)

## <a name="get-the-url-and-prediction-key"></a>获取 URL 和预测密钥

发布模型后，可以通过选择“预测 URL”来检索所需信息。 这将打开一个对话框，其中包含有关使用预测 API 的信息，包括“预测 URL”和“预测密钥” 。

![显示性能选项卡，其“预测 URL”按钮周围有红色矩形框。](./media/use-prediction-api/published-iteration-prediction-url.png)

![显示性能选项卡，其中使用图像文件时所需的预测 URL 值和预测密钥值周围有红色矩形框。](./media/use-prediction-api/prediction-api-info.png)


在本指南中，你将使用本地图像，因此，请将“如果有图像文件”下的 URL 复制到一个临时位置。 同时复制相应的“预测密钥”值。

## <a name="create-the-application"></a>创建应用程序

1. 在 Visual Studio 中创建新的 C# 控制台应用程序。

1. 使用以下代码作为“Program.cs”文件的主体。

    ```csharp
    using System;
    using System.IO;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace CVSPredictionSample
    {
        public static class Program
        {
            public static void Main()
            {
                Console.Write("Enter image file path: ");
                string imageFilePath = Console.ReadLine();

                MakePredictionRequest(imageFilePath).Wait();

                Console.WriteLine("\n\nHit ENTER to exit...");
                Console.ReadLine();
            }

            public static async Task MakePredictionRequest(string imageFilePath)
            {
                var client = new HttpClient();

                // Request headers - replace this example key with your valid Prediction-Key.
                client.DefaultRequestHeaders.Add("Prediction-Key", "<Your prediction key>");

                // Prediction URL - replace this example URL with your valid Prediction URL.
                string url = "<Your prediction URL>";

                HttpResponseMessage response;

                // Request body. Try this sample with a locally stored image.
                byte[] byteData = GetImageAsByteArray(imageFilePath);

                using (var content = new ByteArrayContent(byteData))
                {
                    content.Headers.ContentType = new MediaTypeHeaderValue("application/octet-stream");
                    response = await client.PostAsync(url, content);
                    Console.WriteLine(await response.Content.ReadAsStringAsync());
                }
            }

            private static byte[] GetImageAsByteArray(string imageFilePath)
            {
                FileStream fileStream = new FileStream(imageFilePath, FileMode.Open, FileAccess.Read);
                BinaryReader binaryReader = new BinaryReader(fileStream);
                return binaryReader.ReadBytes((int)fileStream.Length);
            }
        }
    }
    ```

1. 更改以下信息：
   * 将 `namespace` 字段设置为项目名称。
   * 将占位符 `<Your prediction key>` 替换为之前检索到的密钥值。
   * 将占位符 `<Your prediction URL>` 替换为之前检索到的 URL。

## <a name="run-the-application"></a>运行应用程序

运行应用程序时，系统将提示你在控制台中输入图像文件的路径。 然后，系统会将图像提交到预测 API，并以 JSON 格式的字符串形式返回预测结果。 以下是示例响应。

```json
{
    "id":"7796df8e-acbc-45fc-90b4-1b0c81b73639",
    "project":"8622c779-471c-4b6e-842c-67a11deffd7b",
    "iteration":"59ec199d-f3fb-443a-b708-4bca79e1b7f7",
    "created":"2019-03-20T16:47:31.322Z",
    "predictions":[
        {"tagId":"d9cb3fa5-1ff3-4e98-8d47-2ef42d7fb373","tagName":"cat", "probability":1.0},
        {"tagId":"9a8d63fb-b6ed-4462-bcff-77ff72084d99","tagName":"dog", "probability":0.1087869}
    ]
}
```

## <a name="next-steps"></a>后续步骤

在本指南中，你已了解如何使用 C# SDK 将图像提交到自定义图像分类器/检测器并以编程方式接收响应。 接下来，了解如何使用 C# 完成端到端方案，或使用其他语言 SDK 开始学习。

* [快速入门：自定义视觉 SDK](quickstarts/image-classification.md)
