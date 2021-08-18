---
title: Speech Studio 概述 - 语音服务
titleSuffix: Azure Cognitive Services
description: Speech Studio 是一组基于 UI 的工具，用于在应用程序中生成和集成 Azure 语音服务的功能。
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/07/2021
ms.author: lajanuar
ms.openlocfilehash: 6042a129308b7be478b2bd1a45379ad56f62aca6
ms.sourcegitcommit: e7d500f8cef40ab3409736acd0893cad02e24fc0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "122067059"
---
# <a name="what-is-speech-studio"></a>什么是 Speech Studio？

[Speech Studio](https://speech.microsoft.com) 是一组基于 UI 的工具，用于在应用程序中生成和集成 Azure 语音服务的功能。 你可使用无代码方法在 Speech Studio 中创建项目，然后使用[语音 SDK](speech-sdk.md)、[语音 CLI](spx-overview.md) 或各种 REST API 引用在应用程序中创建的资产。

## <a name="set-up-your-azure-account"></a>设置 Azure 帐户

你需要拥有 Azure 帐户并添加语音服务资源，然后才能使用 [Speech Studio](https://speech.microsoft.com)。 如果没有帐户和资源，[可以免费试用语音服务](overview.md#try-the-speech-service-for-free)。

在创建 Azure 帐户和语音服务资源后，请执行以下操作：

1. 使用 Azure 帐户登录到 [Speech Studio](https://speech.microsoft.com)。
1. 选择需要开始使用的语音服务资源。 （可以随时在顶部菜单的“设置”中更改资源。）

## <a name="speech-studio-features"></a>Speech Studio 功能

以下语音服务功能在 Speech Studio 中作为项目类型提供。

* 实时语音转换为文本：通过拖放音频文件快速测试语音转换为文本，而无需使用任何代码。 这是一种演示工具，用于查看语音转换为文本在音频示例上的工作原理，但请参阅语音转换为文本[概述](speech-to-text.md)，以了解可用的完整功能。
* 自定义语音识别：自定义语音识别允许创建针对特定词汇集和说话风格定制的语音识别模型。 与使用基本的语音识别模型相比，自定义语音识别模型是你独特竞争优势的一部分，因为它们无法公开访问。 请参阅[快速入门](how-to-custom-speech-test-and-train.md)，开始上传示例音频以创建自定义语音识别模型。
* 发音评估：发音评估可评估语音发音，并为说话人提供有关讲话音频准确度和流利程度的反馈。 Speech Studio 提供了一个沙盒，用于快速测试此功能，而无需任何代码，但请参阅[操作方法](how-to-pronunciation-assessment.md)一文，了解如何在应用程序中将该功能与语音 SDK 配合使用。
* 语音库：生成自然发音的应用和服务。 从 70 多种语言和变体中的 170 多种语音中进行选择。 使用类似人类的表达性极佳的神经语音将场景变成现实。
* 自定义语音：自定义语音允许为文本语音转换创建独一无二的自定义语音。 你可以提供音频文件，在 Speech Studio 中创建匹配的听录，然后在应用程序中使用自定义语音。 请参阅[操作方法](how-to-custom-voice-create-voice.md)一文，了解如何通过终结点创建和使用自定义语音。 
* 音频内容创建：[音频内容创建](how-to-audio-content-creation.md)是一种易于使用的工具，可用于为各种场景（例如有声读物、新闻广播、视频旁白和聊天机器人）生成高度自然的音频内容。 Speech Studio 允许导出创建的音频文件，以在应用程序中使用。
* 自定义关键字：自定义关键字是使产品激活语音的字词或短语。 你可在 Speech Studio 中创建自定义关键字，然后在应用程序中生成一个[与语音 SDK 配合使用](custom-keyword-basics.md)的二进制文件。
* 自定义命令：使用自定义命令，可以轻松构建针对语音优先的交互体验进行了优化的丰富语音命令应用。 它提供了在 Speech Studio 中进行无代码创作的体验、自动托管模型和相对较低的复杂性，帮助你集中精力为“语音命令”场景构建最佳解决方案。 请参阅[操作方法](how-to-develop-custom-commands-application.md)指南，了解如何生成自定义命令应用程序，另请参阅[将自定义命令应用程序与语音 SDK 集成](how-to-custom-commands-setup-speech-sdk.md)指南。

## <a name="next-steps"></a>后续步骤

[探索 Speech Studio](https://speech.microsoft.com) 并创建项目。




