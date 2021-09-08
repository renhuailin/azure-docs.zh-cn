---
title: Create 关键字快速入门 - 语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终在侦听关键字（或短语）。 当用户说关键字时，设备会将所有后续音频发送到云，直到用户停止说话为止。 自定义关键字是区分设备和加强品牌效应的有效方式。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: pafarley
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 8cf0da3ca3787580513d818a2c0ebca5a07829b6
ms.sourcegitcommit: f2d0e1e91a6c345858d3c21b387b15e3b1fa8b4c
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 09/07/2021
ms.locfileid: "123541077"
---
# <a name="get-started-with-custom-keyword"></a>自定义关键字入门

本快速入门介绍使用 Speech Studio 和语音 SDK 来处理自定义关键字的基础知识。 关键字是使产品激活语音的单词或短语。 使用 Speech Studio 创建关键字模型，然后在应用程序中导出用于语音 SDK 的模型文件。

## <a name="prerequisites"></a>先决条件

本文中的步骤需要语音订阅和语音 SDK。 如果你还没有订阅，[可以免费试用语音服务](overview.md#try-the-speech-service-for-free)。 若要获取 SDK，请参阅适用于平台的[安装指南](quickstarts/setup-platform.md)。

## <a name="create-a-keyword-in-speech-studio"></a>在 Speech Studio 中创建关键字

在使用自定义关键字之前，需使用 [Speech Studio](https://aka.ms/sdsdk-speechportal) 上的[自定义关键字](https://aka.ms/sdsdk-wakewordportal)页来创建关键字。 在你提供关键字后，它将生成一个 `.table` 文件，该文件可用于语音 SDK。

> [!IMPORTANT]
> 自定义关键字模型以及生成的 `.table` 文件只能在 Speech Studio 中创建。
> 不能通过 SDK 或 REST 调用创建自定义关键字。

1. 转到 [Speech Studio](https://aka.ms/sdsdk-speechportal) 并登录，或者，如果你还没有语音订阅，请选择[创建订阅](https://go.microsoft.com/fwlink/?linkid=2086754) 。

1. 在[自定义关键字](https://aka.ms/sdsdk-wakewordportal)页上，创建一个新项目。 

1. 输入名称、可选的描述，然后选择语言 。 每个项目都需要配置一种语言，当前仅支持英语（美国）和中文（普通话，简体）。

    ![描述关键字项目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 从列表中选择项目。 

    ![选择关键字项目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 要创建新的关键字模型，请单击“训练模型”。

1. 输入模型的名称、可选的描述和所选择的关键字，然后单击“下一步”   。 有关如何选择有效关键字，请参阅[指南](keyword-recognition-guidelines.md#choosing-an-effective-keyword)。

    ![输入关键字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 门户为关键字创建候选发音。 通过单击播放按钮收听每个候选发音，并删除所有不正确发音旁边的复选标记。 确保仅正确发音处于选中状态后，单击“训练”开始生成关键字模型。 

    ![屏幕截图显示了你选择正确发音的位置。](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型最多可能需要三十分钟。 模型完成后，关键字列表将从“正在处理”变为“成功” 。 然后你可以下载该文件。

    ![查看关键字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 下载的文件是 `.zip` 存档。 提取存档，将看到一个扩展名为 `.table` 的文件。 这是你将在下一部分中与 SDK 一起使用的文件，因此请确保记下其路径。 文件名将反映你的关键字名，例如关键字“Activate device”的文件名为 `Activate_device.table`。

## <a name="use-a-keyword-model-with-the-speech-sdk"></a>结合使用关键字模型和语音 SDK

::: zone pivot="programming-language-csharp"
[!INCLUDE [C# Basics include](includes/how-to/keyword-recognition/keyword-basics-csharp.md)]
::: zone-end

::: zone pivot="programming-language-python"
[!INCLUDE [Python Basics include](includes/how-to/keyword-recognition/keyword-basics-python.md)]
::: zone-end

::: zone pivot="programming-languages-objectivec-swift"
[!INCLUDE [ObjectiveC/Swift Basics include](includes/how-to/keyword-recognition/keyword-basics-objc.md)]
::: zone-end

## <a name="next-steps"></a>后续步骤

> [!div class="nextstepaction"]
> [获取语音 SDK](speech-sdk.md)
