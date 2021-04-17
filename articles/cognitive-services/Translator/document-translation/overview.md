---
title: 什么是文档翻译？
description: 基于云的批处理文档翻译服务和过程的概述。
ms.topic: overview
manager: nitinme
ms.author: lajanuar
author: laujan
ms.date: 02/11/2021
ms.openlocfilehash: 0d9ef13de29ac140d94e9e4c05b14f35b9e5834c
ms.sourcegitcommit: f5448fe5b24c67e24aea769e1ab438a465dfe037
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "105968163"
---
# <a name="what-is-document-translation-preview"></a>什么是文档翻译（预览版）？

文档翻译是 [Azure Translator](../translator-info-overview.md) 服务的一项基于云的功能，属于 REST API 的 Azure 认知服务系列。 文档翻译 API 可在保留文档结构和数据格式的同时，将文档与 90 种语言和方言进行互译。

本文档包含以下文章类型：  

* [**快速入门**](get-started-with-document-translation.md)介绍了入门说明，指导您完成向服务发出请求。
* [**操作指南**](create-sas-tokens.md)包含以更具体的方式或自定义方式使用功能的说明。  

## <a name="document-translation-key-features"></a>文档翻译主要功能

| 功能 | 说明 |
| ---------| -------------|
| **翻译大型文件**| 异步翻译整个文档。|
|**翻译大量文件**|将多个文件与 90 种语言和方言进行互译。|
|**保留源文件显式**| 在保留原始布局和格式的同时翻译文件。|
|**应用自定义翻译**| 使用常规和[自定义翻译](../customization.md#custom-translator)模型翻译文档。|
|应用自定义词汇表|使用自定义词汇表翻译文档。|

## <a name="how-to-get-started"></a>如何开始？

我们的操作指南介绍了如何快速开始使用文档翻译器。 首先，需要一个有效的 [Azure 帐户](https://azure.microsoft.com/free/cognitive-services/)。  如果没有帐户，可以[创建一个免费帐户](https://azure.microsoft.com/free)。

> [!div class="nextstepaction"]
> [入门](get-started-with-document-translation.md)

## <a name="supported-document-formats"></a>支持的文档格式

文档翻译支持以下文档文件类型：

| 文件类型| 文件扩展名|说明|
|---|---|--|
|Adobe PDF|.pdf|Adobe Acrobat 可移植文档格式|
|HTML|.html|超文本标记语言。|
|本地化交换文件格式|.xlf 、xliff| 并行文档格式，是翻译记忆系统的导出格式。 使用的语言在该文件中定义。|
|Microsoft Excel|.xlsx|用于数据分析和文档的电子表格文件。|
|Microsoft Outlook|.msg|在 Microsoft Outlook 中创建或保存的电子邮件。|
|Microsoft PowerPoint|.pptx| 用于以幻灯片格式显示内容的演示文稿文件。|
|Microsoft Word|.docx| 文本文档文件。|
|制表符分隔值/TAB|.tsv/.tab| 电子表格程序使用的且以制表符分隔的原始数据文件。|
|文本|.txt| 无格式的文本文档。|

## <a name="supported-glossary-formats"></a>受支持的词汇表格式

文档翻译支持以下词汇表文件类型：

| 文件类型| 文件扩展名|说明|
|---|---|--|
|本地化交换文件格式|.xlf 、xliff| 并行文档格式，是翻译记忆系统的导出格式。 使用的语言在该文件中定义。|
|制表符分隔值/TAB|.tsv/.tab| 电子表格程序使用的且以制表符分隔的原始数据文件。|

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [文档翻译入门](get-started-with-document-translation.md)
