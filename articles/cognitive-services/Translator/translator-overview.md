---
title: 什么是 Microsoft Azure 认知服务翻译器？
titlesuffix: Azure Cognitive Services
description: 将“翻译”集成到应用程序、网站、工具和其他解决方案中，提供多语言用户体验。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.topic: overview
ms.subservice: translator-text
ms.date: 08/10/2021
ms.author: lajanuar
ms.custom: cog-serv-seo-aug-2020
keywords: translator, 文本翻译, 机器翻译, 翻译服务, 自定义翻译器
ms.openlocfilehash: 4d5e60beeb69447823369a5dfe610e73354f1ddc
ms.sourcegitcommit: add71a1f7dd82303a1eb3b771af53172726f4144
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/03/2021
ms.locfileid: "123426951"
---
# <a name="what-is-translator"></a>什么是翻译器？

 翻译器是一种基于云的机器翻译服务，是 [Azure 认知服务](../what-are-cognitive-services.md)系列 REST API 的一部分。  翻译器可用于任何操作系统，并为全球数千家企业使用的许多 Microsoft 产品和服务提供支持，以执行语言翻译和其他与语言相关的操作。 在此概述中，你将了解如何通过翻译器跨所有[受支持的语言](./language-support.md)为应用程序构建智能的多语言解决方案。

翻译器文档包含以下文章类型：

* [快速入门](quickstart-translator.md)。 指导你完成向服务发出请求的入门说明。
* [操作指南](translator-how-to-signup.md)。 有关以更具体的方式或自定义方式访问和使用服务的说明。
* [参考文章](reference/v3-0-reference.md)。 REST API 文档和基于编程语言的内容。

## <a name="translator-features-and-development-options"></a>翻译器功能和开发选项

翻译器服务支持下列功能。 请使用下表中的链接详细了解每项功能并浏览 API 参考信息。

| 功能 | 说明 | 开发选项 |
|----------|-------------|--------------------------|
| [**文本翻译**](text-translation-overview.md) | 在支持的源语言和目标语言之间实时执行文本翻译。 | <ul><li>[**REST API**](reference/rest-api-guide.md) </li><li>[文本翻译 Docker 容器](containers/translator-how-to-install-container.md) — 目前为门控式预览版。</li></ul> |
| [**文档翻译**](document-translation/overview.md) | 翻译批文件和复杂文件，同时保留原始文档的结构和格式。 | <ul><li>[**REST API**](document-translation/reference/rest-api-guide.md)</li><li>[Client-library SDK](document-translation/client-sdks.md)</li></ul> |
| [**自定义翻译**](custom-translator/overview.md) | 构建自定义模型以翻译特定于领域和行业的语言、术语和样式。 | <ul><li>[自定义翻译器门户](https://portal.customtranslator.azure.ai/)</li></ul> |

## <a name="try-the-translator-service-for-free"></a>免费试用翻译器服务

首选，需要一个 Microsoft 帐户；如果没有 Microsoft 帐户，可以在 [Microsoft 帐户门户](https://account.microsoft.com/account)注册一个免费帐户。  选择“创建 Microsoft 帐户”，按步骤创建并验证新的帐户。

接下来，需要拥有一个 Azure 帐户。导航到 [Azure 注册](https://azure.microsoft.com/free/ai/)页，选择“免费启动”按钮，然后使用 Microsoft 帐户凭据创建新的 Azure 帐户 。

现在一切已准备就绪，可立即开始！ [创建“翻译器”服务](translator-how-to-signup.md "转到 Azure 门户。")，以[获取访问密钥和 API 终结点](translator-how-to-signup.md#authentication-keys-and-endpoint-url "身份验证需要终结点 URL 和只读密钥。")，然后试用[快速入门](quickstart-translator.md "了解如何通过 REST 使用翻译器。")  。

## <a name="next-steps"></a>后续步骤

* 详细了解以下功能：
  * [**文本翻译**](text-translation-overview.md)
  * [**文档翻译**](document-translation/overview.md)
  * [**自定义翻译**](custom-translator/overview.md)
* 查看[翻译器定价](https://azure.microsoft.com/pricing/details/cognitive-services/translator-text-api/)。
