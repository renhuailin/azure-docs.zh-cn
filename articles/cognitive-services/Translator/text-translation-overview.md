---
title: 什么是 Microsoft Azure 认知服务文本翻译？
titlesuffix: Azure Cognitive Services
description: 将文本翻译 API 集成到应用程序、网站、工具和其他解决方案中，提供多语言用户体验。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/09/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, 文本翻译, 机器翻译, 翻译服务, 自定义翻译器
ms.openlocfilehash: 68354706935d0dcfb29055a1eb5b0e273022d306
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121783254"
---
# <a name="what-is-text-translation"></a>什么是文本翻译？

 文本翻译是翻译器服务的基于云的 REST API 功能，它使用神经网络机器翻译技术跨所有[支持的语言](language-support.md)实现快速准确的源到目标实时文本翻译。 在本概述中，你将了解文本翻译 REST API 如何使你可以为应用程序和工作流构建智能解决方案。

文本翻译文档包含以下文章类型：

* [快速入门](quickstart-translator.md)。 指导你完成向服务发出请求的入门说明。
* [操作指南](translator-how-to-signup.md)。 有关以更具体的方式或自定义方式访问和使用服务的说明。
* [参考文章](reference/v3-0-reference.md)。 REST API 文档和基于编程语言的内容。

## <a name="text-translation-features"></a>文本翻译功能

 文本翻译功能支持以下方法：

* [语言](reference/v3-0-languages.md)。 返回“翻译”、“音译”和“字典查找”操作支持的语言列表  。 此请求不需要身份验证；只需将以下 GET 请求复制并粘贴到 Postman 或是你喜欢的 API 工具或浏览器中：

    ```http
    https://api.cognitive.microsofttranslator.com/languages?api-version=3.0
    ```

* [翻译](reference/v3-0-translate.md#translate-to-multiple-languages)。 使用单个请求将单个源语言文本呈现为多个目标语言文本。

* [音译](reference/v3-0-transliterate.md)。 将源语言的字符或字母转换为目标语言的对应字符或字母。

* [检测](reference/v3-0-detect.md)。 返回源代码语言代码以及用于指示文本翻译和音译是否支持检测到的语言的布尔变量。

    > [!NOTE]
    > 可以使用[单个 REST API 调用](reference/v3-0-translate.md#translate-a-single-input-with-language-autodetection)对文本进行文本翻译、音译和检测。

* [字典查找](reference/v3-0-dictionary-lookup.md)。 返回源术语在目标语言中的等效字词。
* [字典示例](reference/v3-0-dictionary-examples.md) 返回源术语和目标术语对的语法结构和上下文示例。

## <a name="text-translation-deployment-options"></a>文本翻译部署选项

使用以下资源可将文本翻译添加到项目和应用程序：

* 通过 Azure 中提供的 [REST API](reference/rest-api-guide.md)访问基于云的翻译器服务。

* 将 REST API [翻译请求](containers/translator-container-supported-parameters.md)与[文本翻译 Docker 容器](containers/translator-how-to-install-container.md)一起使用。

    > [!IMPORTANT]
    >
    > * 翻译器容器为门控式预览版。 若要使用它，必须完成并提交 [适用于门控式服务的 Azure 认知服务应用程序](https://aka.ms/csgate-translator)联机请求表单，并获得批准以获取对容器的访问权限。
    >
    > * 与云产品/服务相比，[翻译器容器映像](https://hub.docker.com/_/microsoft-azure-cognitive-services-translator-text-translation)支持的功能有限。
    >

## <a name="get-started-with-text-translation"></a>开始使用文本翻译

准备好开始了吗？

* 在 Azure 门户中[创建翻译器资源](translator-how-to-signup.md "转到 Azure 门户。")。

* [获取访问密钥和 API 终结点](translator-how-to-signup.md#authentication-keys-and-endpoint-url)。 身份验证需要终结点 URL 和只读密钥。

* 浏览我们的[快速入门](quickstart-translator.md "了解如何通过 REST 和首选编程语言使用翻译器。")并查看以下编程语言的用例和代码示例： 
  * [**C#/.NET**](quickstart-translator.md?tabs=csharp)
  * [**Go**](quickstart-translator.md?tabs=go)
  * [**Java**](quickstart-translator.md?tabs=java)
  * [**JavaScript/Node.js**](quickstart-translator.md?tabs=nodejs)
  * [**Python**](quickstart-translator.md?tabs=python)

## <a name="next-steps"></a>后续步骤

深入了解文本翻译 REST API：

> [!div class="nextstepaction"]
> [请参阅 REST API 参考](./reference/v3-0-reference.md)
