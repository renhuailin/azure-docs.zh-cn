---
title: 从必应语音迁移到语音服务
titleSuffix: Azure Cognitive Services
description: 了解如何从现有的必应语音订阅迁移到 Azure 认知服务的语音服务。
services: cognitive-services
author: wsturman
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/03/2020
ms.author: lajanuar
ms.openlocfilehash: fc08bea2700e1b9abaed6c4b8ce29b0ce10f8956
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122070624"
---
# <a name="migrate-from-bing-speech-to-the-speech-service"></a>从必应语音迁移到语音服务

本文介绍了如何将应用程序从必应语音 API 迁移到语音服务。

本文概述了必应语音 API 与语音服务之间的区别，并就应用程序的迁移建议了策略。 必应语音 API 订阅密钥对语音服务不起作用；你将需要新的语音服务订阅。

单个语音服务订阅密钥授予对以下功能的访问权限。 每个功能单独计量，以便仅针对你使用的功能收费。

* [语音转文本](speech-to-text.md)
* [自定义语音转文本](./custom-speech-overview.md)
* [文本转语音](text-to-speech.md)
* [自定义文本转语音声音](./how-to-custom-voice-create-voice.md)
* [语音翻译](speech-translation.md)（不包括[文本翻译](../translator/translator-info-overview.md)）

[语音 SDK](speech-sdk.md) 是必应语音客户端库的功能替换，但使用了不同 API。

## <a name="comparison-of-features"></a>功能比较

语音服务非常类似于必应语音，但有以下差异。

| 功能 | 必应语音 | 语音服务 | 详细信息 |
|--|--|--|--|
| C# SDK | :heavy_check_mark: | :heavy_check_mark: | 语音服务支持 Windows 10、通用 Windows 平台 (UWP) 和 .NET Standard 2.0。 |
| C++ SDK | :heavy_minus_sign: | :heavy_check_mark: | 语音服务支持 Windows 和 Linux。 |
| Java SDK | :heavy_check_mark: | :heavy_check_mark: | 语音服务支持 Android 和语音设备。 |
| 连续语音识别 | 10 分钟 | 无限制 | 语音 SDK 支持无限制的连续识别，并在超时或断开连接时自动重新连接。 |
| 部分或中期结果 | :heavy_check_mark: | :heavy_check_mark: | 受语音 SDK 支持。 |
| 自定义语音模型 | :heavy_check_mark: | :heavy_check_mark: | 必应语音需要单独的自定义语音订阅。 |
| 自定义语音字体 | :heavy_check_mark: | :heavy_check_mark: | 必应语音需要单独的自定义语音订阅。 |
| 24-kHz 语音 | :heavy_minus_sign: | :heavy_check_mark: |
| 语音意向识别 | 需要单独的 LUIS API 调用 | 已集成（与 SDK） | 可以将 LUIS 密钥用于语音服务。 |
| 简单意向识别 | :heavy_minus_sign: | :heavy_check_mark: |
| 批量听录长音频文件 | :heavy_minus_sign: | :heavy_check_mark: |
| 识别模式 | 通过终结点 URI 手动 | 自动 | 识别模式在语音服务中不可用。 |
| 终结点位置 | 全球 | 区域 | 区域终结点改善延迟。 |
| REST API | :heavy_check_mark: | :heavy_check_mark: | 语音服务 REST API 与必应语音兼容（不同终结点）。 REST API 支持文本转语音以及限制的语音转文本功能。 |
| Websocket 协议 | :heavy_check_mark: | :heavy_minus_sign: | 语音 SDK 针对需要与服务建立持续连接的功能对 Web 套接字连接进行抽象，因此不再支持手动订阅这些连接。 |
| 服务到服务 API 调用 | :heavy_check_mark: | :heavy_minus_sign: | 通过 C# 服务库在必应语音中提供。 |
| 开源 SDK | :heavy_check_mark: | :heavy_minus_sign: |

语音服务使用基于时间的定价模型（而非基于事务的模型）。 有关详细信息，请参阅[语音服务定价](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/)。

## <a name="migration-strategies"></a>迁移策略

如果你或组织有处于开发或生产阶段且使用必应语音 API 的应用程序，请尽快将应用程序更新为使用语音服务。 有关可用 SDK、代码示例和教程，请参阅[语音服务文档](index.yml)。

语音服务 [REST API](./overview.md#reference-docs) 与必应语音 API 兼容。 如果当前正在使用必应语音 REST API，则只需要更改 REST 终结点并切换到语音服务订阅密钥。

如果使用特定编程语言的必应语音客户端库，则迁移到[语音 SDK](speech-sdk.md) 需要更改应用程序，因为 API 不同。 语音 SDK 可以简化代码，同时使你可以访问新功能。 语音 SDK 能以各种编程语言提供。 所有平台上的 API 均类似，从而简化了多平台开发。

语音服务不提供全球终结点。 确定应用程序在使用一个区域终结点处理其所有流量的情况下，能否高效运行。 如果不能，则使用地理位置来确定最高效的终结点。 需要在你使用的各区域使用单独的语音服务订阅。

语音 SDK 入门：

1. 下载[语音 SDK](speech-sdk.md)。
1. 演练语音服务[快速入门指南](./get-started-speech-to-text.md?pivots=programming-language-csharp&tabs=dotnet)和[教程](how-to-recognize-intents-from-speech-csharp.md)。 另请查看[代码示例](./speech-sdk.md#sample-source-code)来体验新的 API。
1. 更新应用程序以使用语音服务。

## <a name="support"></a>支持

必应语音客户应通过打开[支持票证](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)来联系客户支持。 如果你的支持需要一个[技术支持计划](https://azure.microsoft.com/support/plans/)，也可以与我们联系。

有关语音服务、SDK 和 API 支持，请访问语音服务[支持页](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext)。

## <a name="next-steps"></a>后续步骤

* [免费试用语音服务](overview.md#try-the-speech-service-for-free)
* [语音转文本入门](get-started-speech-to-text.md)
* [开始使用文本转语音](get-started-text-to-speech.md)

## <a name="see-also"></a>另请参阅

* [语音服务发行说明](releasenotes.md)
* [什么是语音服务](overview.md)
* [语音服务和语音 SDK 文档](speech-sdk.md#get-the-speech-sdk)