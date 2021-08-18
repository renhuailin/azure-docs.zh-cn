---
title: 文档翻译 REST API 引用指南
titleSuffix: Azure Cognitive Services
description: 查看包含指向文档翻译 REST API 链接的列表。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 06/21/2021
ms.author: lajanuar
ms.openlocfilehash: 38757efc32a90a74cdf73bc84af28b80f8402948
ms.sourcegitcommit: 5fabdc2ee2eb0bd5b588411f922ec58bc0d45962
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 06/23/2021
ms.locfileid: "112543876"
---
# <a name="document-translation-rest-api-reference-guide"></a>文档翻译 REST API 引用指南

文档翻译是 Azure Translator 服务的一项基于云的功能，属于 REST API 的 Azure 认知服务系列。 文档翻译 API 可翻译所有[支持的语言和方言](../../language-support.md)的文档，同时保留文档结构和数据格式。 可用语言如下表所示：

| 请求| 说明|
|---------|--------------|
| [**获取支持的文档格式**](get-supported-document-formats.md)| 此方法返回支持的文档格式列表。|
|[**获取支持的词汇表格式**](get-supported-glossary-formats.md)|此方法返回支持的词汇表格式列表。|
|[**获取支持的存储源**](get-supported-storage-sources.md)| 此方法返回支持的存储源/选项列表。|
|[**开始翻译 (POST)**](start-translation.md)|此方法启动文档翻译作业。 |
|[**获取文档状态**](get-documents-status.md)|此方法返回翻译作业中所有文档的状态。|
|[**获取文档状态**](get-document-status.md)| 此方法返回作业中特定文档的状态。 |
|[**获取翻译状态**](get-translations-status.md)| 此方法返回用户提交的所有翻译请求的列表以及每个请求的状态。|
|[**获取翻译状态**](get-translation-status.md) | 此方法返回特定文档翻译请求的状态摘要。 它包括整体请求状态和作为该请求一部分正在翻译的文档的状态。|
|[**取消翻译 (DELETE)**](cancel-translation.md)| 此方法取消当前正在处理或者排队的文档翻译。 |

> [!div class="nextstepaction"]
> [浏览适用于 C# 和 Python 的客户端库和 SDK。](../client-sdks.md)