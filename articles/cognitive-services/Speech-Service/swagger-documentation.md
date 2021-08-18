---
title: Swagger 文档 - 语音服务
titleSuffix: Azure Cognitive Services
description: Swagger 文档可用于自动生成适用于多种编程语言的 SDK。 Swagger 支持服务中的所有操作
services: cognitive-services
author: alexeyo26
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: reference
ms.date: 02/16/2021
ms.author: alexeyo
ms.openlocfilehash: 3b27bd16168f4949081f3f399f7f8f8293d2aa0b
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292562"
---
# <a name="swagger-documentation"></a>Swagger 文档

语音服务提供了一个 Swagger 规范，用于与少量 REST API 交互，这些 REST API 用于导入数据、创建模型、测试模型准确性、创建自定义终结点、排列批量听录以及管理订阅。 可使用这些 API 以编程方式完成通过 [Speech Studio 自定义语音识别区域](./custom-speech-overview.md)提供的大部分操作。

> [!NOTE]
> 语音服务具有多个 REST API，分别用于[语音转文本](rest-speech-to-text.md)和[文本转语音](rest-text-to-speech.md)。  
>
> 但是 Swagger 规范中仅记录了[语音转文本 REST API v3.0](rest-speech-to-text.md#speech-to-text-rest-api-v30) 和 v2.0。 有关所有其他语音服务 REST API 的信息，请参阅上一段中引用的文档。

## <a name="generating-code-from-the-swagger-specification"></a>从 Swagger 规范生成代码

[Swagger 规范](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)包含可快速测试各种路径的选项。 但有时需要为所有路径生成代码，从而创建可基于未来的解决方案的单个调用库。 让我们看看生成 Python 库的过程。

你需要将 Swagger 设置为你的语音资源的区域。 可在 Azure 门户中的语音资源设置的“概述”部分确认该区域。 [此处](regions.md#speech-to-text)提供受支持区域的完整列表。

1. 在浏览器中，转到你所在[区域](regions.md#speech-to-text)的 Swagger 规范：  
       `https://<your-region>.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0`
1. 在该页上，单击“API 定义”，然后单击“Swagger” 。 复制显示的页的 URL。
1. 在新浏览器中，转到 [https://editor.swagger.io](https://editor.swagger.io)
1. 依次单击“文件”、“导入 URL”，然后粘贴该 URL，再单击“确定”  。
1. 单击“生成客户端”，然后选择“Python” 。 客户端库会以 `.zip` 文件格式下载到你的计算机中。
1. 提取下载的全部内容。 可使用 `tar -xf` 提取全部内容。
1. 将提取的模块安装到 Python 环境：  
      `pip install path/to/package/python-client`
1. 将安装的包命名为 `swagger_client`。 检查安装是否正常：  
       `python -c "import swagger_client"`

可以使用通过 [GitHub 上的语音服务示例](https://aka.ms/csspeech/samples)生成的 Python 库。

## <a name="reference-documents"></a>参考文档

* [Swagger：语音转文本 REST API v3.0](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0)
* [语音转文本 REST API](rest-speech-to-text.md)
* [文本转语音 REST API](rest-text-to-speech.md)

## <a name="next-steps"></a>后续步骤

* [GitHub 上的语音服务示例](https://aka.ms/csspeech/samples)。
* [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)