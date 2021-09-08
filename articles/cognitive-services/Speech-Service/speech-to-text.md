---
title: 语音转文本概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: 使用语音转文本软件，可将音频流实时听录为文本。 应用程序、工具或设备可以使用、显示和处理此文本输入。 本文概述了语音转文本服务的优势和功能。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 09/01/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 语音转文本, 语音转文本软件
ms.openlocfilehash: 7da34e76d760b8f29a90112c00a1f8f05655d940
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123543965"
---
# <a name="what-is-speech-to-text"></a>什么是语音转文本？

本概述介绍语音转文本服务的优势和功能。
使用语音转文本（也称为语音识别）功能，可将音频流实时听录为文本。 应用程序、工具或设备可以使用、显示和处理此文本即命令输入。 此服务由 Microsoft 对 Cortana 和 Office 产品使用的同一识别技术提供支持。 它可与<a href="./speech-translation.md" target="_blank">翻译</a>和<a href="./text-to-speech.md" target="_blank">文本转语音</a>服务产品无缝地协同工作。 有关可用语音转文本语言的完整列表，请参阅[支持的语言](language-support.md#speech-to-text)。

语音转文本服务默认使用通用语言模型。 此模型已使用 Microsoft 自有的数据训练，部署在云中。 它非常适合用于对话和听写方案。 使用语音转文本在独特的环境中进行识别和听录时，可以创建并训练自定义的声学、语言和发音模型。 自定义有助于解决环境干扰或特定于行业的词汇的问题。

本文档包含以下文章类型：

* **快速入门** 介绍了入门说明，指导您完成向服务发出请求。
* **操作指南** 包含以更具体的方式或自定义方式使用服务的说明。
* **概念** 对服务的功能和特性进行了深入说明。
* **教程** 是较长的指南，向您演示了如何在更广泛的业务解决方案中使用该服务作为组件。

> [!NOTE]
> 必应语音已于 2019 年 10 月 15 日停止使用。 如果你的应用程序、工具或产品使用了必应语音 API，请参阅我们创建的指南来帮助你迁移到语音服务。
> - [从必应语音迁移到语音服务](how-to-migrate-from-bing-speech.md)

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

## <a name="get-started"></a>入门

请参阅[快速入门](get-started-speech-to-text.md)以开始使用语音转文本。 该服务通过[语音 SDK](speech-sdk.md)、[REST API](rest-speech-to-text.md#pronunciation-assessment-parameters) 和[语音 CLI](spx-overview.md) 提供。

## <a name="sample-code"></a>代码示例

GitHub 上提供了语音 SDK 的示例代码。 这些示例涵盖了常见方案，例如，从文件或流中读取音频、连续和单次识别，以及使用自定义模型。

- [语音转文本示例 (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [批量听录示例 (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)
- [发音评估示例 (REST)](rest-speech-to-text.md#pronunciation-assessment-parameters)

## <a name="customization"></a>自定义

除了标准语音服务模型外，还可以创建自定义模型。 自定义有助于克服语音识别障碍，如说话风格、词汇和背景噪音，详见[自定义语音识别](./custom-speech-overview.md)。 自定义选项因语言/区域设置而异，请参阅[支持的语言](./language-support.md)以验证相关支持。

## <a name="batch-transcription"></a>批量听录

批量听录是一组 REST API 操作，可用于听录存储中的大量音频。 你可以指向具有共享访问签名 (SAS) URI 的音频文件并异步接收听录结果。 有关如何使用批量听录 API 的详细信息，请参阅[操作说明](batch-transcription.md)。

[!INCLUDE [speech-reference-doc-links](includes/speech-reference-doc-links.md)]

## <a name="next-steps"></a>后续步骤

- [免费获取语音服务订阅密钥](overview.md#try-the-speech-service-for-free)
- [获取语音 SDK](speech-sdk.md)