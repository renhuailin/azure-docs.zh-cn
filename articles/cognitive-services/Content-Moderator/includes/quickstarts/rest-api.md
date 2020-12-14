---
title: 内容审查器 REST API 快速入门
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何开始使用 Azure 内容审查器 REST API。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: include
ms.date: 12/08/2020
ms.author: pafarley
ms.openlocfilehash: 06af722e8c827acdad356acb982ac3761ef68236
ms.sourcegitcommit: 80c1056113a9d65b6db69c06ca79fa531b9e3a00
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 12/09/2020
ms.locfileid: "96905651"
---
开始使用 Azure 内容审查器 REST API。 

内容审查器是一种 AI 服务，可用于处理可能的冒犯性、危险或不可取内容。 使用 AI 支持型内容审核服务扫描文本、图像和视频，并自动应用内容标志。 然后，将应用与审阅工具进行集成，为人工审阅者团队提供一个联机审查环境。 在应用中内置内容筛选软件，以符合法规或维护用户的预期环境。

使用内容审查器 REST API：

* [审查文本](#moderate-text)
* [审查图像](#moderate-images)

## <a name="prerequisites"></a>先决条件

* Azure 订阅 - [免费创建订阅](https://azure.microsoft.com/free/cognitive-services/)
* 拥有 Azure 订阅后，在 Azure 门户中<a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator"  title="创建内容审查器资源"  target="_blank">创建内容审查器资源<span class="docon docon-navigate-external x-hidden-focus"></span></a>，以获取密钥和终结点。 等待其部署并单击“转到资源”按钮。
    * 需要从创建的资源获取密钥和终结点，以便将应用程序连接到内容审查器。 你稍后会在快速入门中将密钥和终结点粘贴到下方的代码中。
    * 可以使用免费定价层 (`F0`) 试用该服务，然后再升级到付费层进行生产。


## <a name="moderate-text"></a>审查文本

你将使用类似于以下的命令，调用内容审查器 API 来分析文本正文并将结果输出到控制台。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="textmod":::

将命令复制到文本编辑器，并进行以下更改：

1. 将 `Ocp-Apim-Subscription-Key` 分配到有效的人脸订阅密钥。
1. 需更改该查询 URL 的第一部分，使之与订阅密钥的相应终结点匹配。
   [!INCLUDE [subdomains-note](../../../../../includes/cognitive-services-custom-subdomains-note.md)]
1. 选择性地将请求正文更改为要分析的任何文本字符串。

进行更改以后，请打开命令提示符并输入新的命令。 

### <a name="examine-the-results"></a>检查结果

应会在控制台窗口中看到显示为 JSON 数据的文本审查结果。 例如：

```json
{
  "OriginalText": "Is this a crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255,\n1 Microsoft Way, Redmond, WA 98052\n",
  "NormalizedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "AutoCorrectedText": "Is this a crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, \n1 Microsoft Way, Redmond, WA 98052",
  "Misrepresentation": null,
  "PII": {
    "Email": [
      {
        "Detected": "abcdef@abcd.com",
        "SubType": "Regular",
        "Text": "abcdef@abcd.com",
        "Index": 21
      }
    ],
    "IPA": [
      {
        "SubType": "IPV4",
        "Text": "255.255.255.255",
        "Index": 61
      }
    ],
    "Phone": [
      {
        "CountryCode": "US",
        "Text": "6657789887",
        "Index": 45
      }
    ],
    "Address": [
      {
        "Text": "1 Microsoft Way, Redmond, WA 98052",
        "Index": 78
      }
    ]
  },
 "Classification": {
    "Category1": 
    {
      "Score": 0.5
    },
    "Category2": 
    {
      "Score": 0.6
    },
    "Category3": 
    {
      "Score": 0.5
    },
    "ReviewRecommended": true
  },
  "Language": "eng",
  "Terms": [
    {
      "Index": 10,
      "OriginalIndex": 10,
      "ListId": 0,
      "Term": "crap"
    }
  ],
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "1717c837-cfb5-4fc0-9adc-24859bfd7fac"
}
```

有关内容审查器屏幕的文本属性的详细信息，请参阅[文本审查概念](../../text-moderation-api.md)指南。

## <a name="moderate-images"></a>审查图像

你将使用类似于以下的命令，调用内容审查器 API 来审查远程图像并将结果输出到控制台。

:::code language="shell" source="~/cognitive-services-quickstart-code/curl/content-moderator/quickstart.sh" ID="imagemod":::

将命令复制到文本编辑器，并进行以下更改：

1. 将 `Ocp-Apim-Subscription-Key` 分配到有效的人脸订阅密钥。
1. 需更改该查询 URL 的第一部分，使之与订阅密钥的相应终结点匹配。
1. 可选择性地将请求正文中的 `"Value"` URL 更改为任何要审查的远程图像。

> [!TIP]
> 还可以通过将本地图像的字节数据传递到请求正文中来审查这些图像。 可查看[参考文档](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66c)，了解如何执行此操作。

进行更改以后，请打开命令提示符并输入新的命令。 

### <a name="examine-the-results"></a>检查结果

应会在控制台窗口中看到显示为 JSON 数据的图像审查结果。 

```json
{
  "AdultClassificationScore": x.xxx,
  "IsImageAdultClassified": <Bool>,
  "RacyClassificationScore": x.xxx,
  "IsImageRacyClassified": <Bool>,
  "AdvancedInfo": [],
  "Result": false,
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "TrackingId": "<Request Tracking Id>"
}
```

有关内容审查器屏幕的图像属性的详细信息，请参阅[图像审查概念](../../image-moderation-api.md)指南。

## <a name="clean-up-resources"></a>清理资源

如果想要清理并删除认知服务订阅，可以删除资源或资源组。 删除资源组同时也会删除与之相关联的任何其他资源。

* [门户](../../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用内容审查器 REST API 来执行审查任务。 接下来，请阅读概念指南来详细了解图像或其他媒体的审查。

* [图像审查的概念](../../image-moderation-api.md)
* [文本审查概念](../../text-moderation-api.md)
* [什么是 Azure 内容审查器？](../../overview.md)