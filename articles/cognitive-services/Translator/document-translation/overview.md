---
title: 什么是 Microsoft Azure 认知服务文档翻译？
description: 基于云的批处理文档翻译服务和过程的概述。
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 08/09/2021
ms.openlocfilehash: 64198fce7acb3ea02349978de0c2787cb744e6b4
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123433883"
---
# <a name="what-is-document-translation"></a>什么是文档翻译？

文档翻译是 [Azure Translator](../translator-overview.md) 服务的一项基于云的功能，属于 REST API 的 Azure 认知服务系列。 在本概述中，你将了解如何使用文档翻译 API 跨所有[支持的语言和方言](../../language-support.md)翻译多个复杂的文档，同时保留原始文档结构和数据格式。

本文档包含以下文章类型：

* [**快速入门**](get-started-with-document-translation.md)介绍了入门说明，指导您完成向服务发出请求。
* [**操作指南**](create-sas-tokens.md)包含以更具体的方式或自定义方式使用功能的说明。
* [参考](reference/rest-api-guide.md)提供 REST API 设置、值、关键字和配置。

## <a name="document-translation-key-features"></a>文档翻译主要功能

| 功能 | 说明 |
| ---------| -------------|
| **翻译大型文件**| 异步翻译整个文档。|
|**翻译大量文件**|在保留文档结构和数据格式的同时，翻译所有支持的语言和方言的多个文件。|
|**保留源文件显式**| 在保留原始布局和格式的同时翻译文件。|
|**应用自定义翻译**| 使用常规和[自定义翻译](../customization.md#custom-translator)模型翻译文档。|
|应用自定义词汇表|使用自定义词汇表翻译文档。|
|**自动检测文档语言**|让文档翻译服务确定文档的语言。|
|**翻译包含多种语言的文档内容**|使用自动检测功能，将包含多种语言的文档翻译成目标语言。|

> [!NOTE]
> 如果翻译包含多种语言的文档，该功能适用于每种语言的完整句子。 如果句子包含多种语言，内容可能不会全部翻译成目标语言。
>

## <a name="document-translation-development-options"></a>文档翻译开发选项

可使用 REST API 或客户端库 SDK 将文档翻译添加到应用程序：

* [REST API](reference/rest-api-guide.md)。 是一个与语言无关的接口，使你能够创建 HTTP 请求和授权标头来翻译文档。

* [客户端库 SDK](client-sdks.md) 是语言特定的类、对象、方法和代码，可以通过在项目中添加引用来快速使用它们。 目前文档翻译支持 [C#/.NET](/dotnet/api/azure.ai.translation.document) 和 [Python](https://pypi.org/project/azure-ai-translation-document/) 编程语言 。

## <a name="get-started"></a>入门

我们的操作指南介绍了如何快速开始使用文档翻译器。 首先，需要一个有效的 [Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free)。

> [!div class="nextstepaction"]
> 从[此处](get-started-with-document-translation.md "了解如何借助 HTTP REST 使用文档翻译")开始

## <a name="supported-document-formats"></a>支持的文档格式

文档翻译支持以下文档文件类型：

| 文件类型| 文件扩展名|说明|
|---|---|--|
|Adobe PDF|pdf|Adobe Acrobat 可移植文档格式|
|逗号分隔值 |csv| 电子表格程序使用的且以逗号分隔的原始数据文件。|
|HTML|html, htm|超文本标记语言。|
|本地化交换文件格式|xlf. 、xliff| 并行文档格式，是翻译记忆系统的导出格式。 使用的语言在该文件中定义。|
|Markdown| markdown, mdown, mkdn, md, mkd, mdwn, mdtxt, mdtext, rmd| 用于创建带格式文本的轻型标记语言。|
|MHTML|mthml, mht| 一种网页存档格式，用于组合 HTML 代码及其配套资源。|
|Microsoft Excel|xls, xlsx|用于数据分析和文档的电子表格文件。|
|Microsoft Outlook|msg|在 Microsoft Outlook 中创建或保存的电子邮件。|
|Microsoft PowerPoint|ppt, pptx| 用于以幻灯片格式显示内容的演示文稿文件。|
|Microsoft Word|doc, docx| 文本文档文件。|
|OpenDocument 文本|odt|一个开源文本文档文件。|
|OpenDocument 演示文稿|odp|一个开源演示文稿文件。|
|OpenDocument 电子表格|ods|一个开源电子表格文件。|
|多信息文本格式|rtf|包含格式的文本文档。|
|制表符分隔值/TAB|tsv/tab| 电子表格程序使用的且以制表符分隔的原始数据文件。|
|文本|txt| 无格式的文本文档。|

## <a name="supported-glossary-formats"></a>受支持的词汇表格式

文档翻译支持以下词汇表文件类型：

| 文件类型| 文件扩展名|说明|
|---|---|--|
|逗号分隔值| csv |电子表格程序使用的且以逗号分隔的原始数据文件。|
|本地化交换文件格式| xlf , xliff| 一种并行文档格式，导出翻译内存系统。使用的语言在文件内定义。|
|制表符分隔值/TAB|tsv, tab| 电子表格程序使用的且以制表符分隔的原始数据文件。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文档翻译入门](get-started-with-document-translation.md)
