---
title: Create 关键字快速入门-语音服务
titleSuffix: Azure Cognitive Services
description: 设备始终 (或短语) 侦听关键字。 当用户显示关键字时，设备会将所有后续音频发送到云，直到用户停止说话。 自定义关键字是区分设备并增强品牌的有效方法。
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/03/2020
ms.author: trbye
ms.custom: devx-track-csharp
zone_pivot_groups: keyword-quickstart
ms.openlocfilehash: 49ac70b6881085f48c8bc3a12e31e4a1aa220c6a
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 11/21/2020
ms.locfileid: "95021943"
---
# <a name="get-started-with-custom-keyword"></a>自定义关键字入门

本快速入门介绍如何使用 Speech Studio 和 Speech SDK 来使用自定义关键字。 关键字是允许激活语音的单词或短语。 使用 Speech Studio 创建关键字模型，然后在应用程序中导出用于语音 SDK 的模型文件。

## <a name="prerequisites"></a>先决条件

本文中的步骤需要语音订阅和语音 SDK。 如果还没有订阅，请 [免费试用语音服务](overview.md#try-the-speech-service-for-free)。 若要获取 SDK，请参阅适用于你的平台的 [安装指南](quickstarts/setup-platform.md) 。

## <a name="create-a-keyword-in-speech-studio"></a>在 Speech Studio 中创建关键字

你需要先使用[Speech Studio](https://aka.ms/sdsdk-speechportal)上的 "[自定义关键字](https://aka.ms/sdsdk-wakewordportal)" 页创建关键字，然后才能使用自定义关键字。 提供关键字后，它会生成一个 `.table` 可与语音 SDK 一起使用的文件。

> [!IMPORTANT]
> 自定义关键字模型和生成的 `.table` 文件 **只能** 在 Speech Studio 中创建。
> 不能从 SDK 或 REST 调用创建自定义关键字。

1. 转到 [Speech Studio](https://aka.ms/sdsdk-speechportal) 并 **登录** ，如果还没有语音订阅，请选择 " [**创建订阅**](https://go.microsoft.com/fwlink/?linkid=2086754)"。

1. 在 " [自定义关键字](https://aka.ms/sdsdk-wakewordportal) " 页上，创建一个 **新项目**。 

1. 输入 **名称** 和可选 **描述**，并选择语言。 每种语言都需要一个项目，并且支持目前仅限于该 `en-US` 语言。

    ![描述关键字项目](media/custom-keyword/custom-kws-portal-new-project.png)

1. 从列表中选择你的项目。 

    ![选择关键字项目](media/custom-keyword/custom-kws-portal-project-list.png)

1. 若要创建新的关键字模型，请单击 " **训练模型**"。

1. 输入模型的 **名称** 、可选 **描述** 和所选 **关键字** ，然后单击 " **下一步**"。 请参阅有关选择有效关键字的 [准则](./custom-keyword-overview.md#choose-an-effective-keyword) 。

    ![输入关键字](media/custom-keyword/custom-kws-portal-new-model.png)

1. 门户为关键字创建候选发音。 通过单击 "播放" 按钮来倾听每个候选项，并删除任何不正确的发音旁边的检查。 仅选中好发音后，单击 " **训练** " 开始生成关键字模型。 

    ![屏幕截图显示了正确的 pronounciations。](media/custom-keyword/custom-kws-portal-choose-prons.png)

1. 生成模型最多可能需要30分钟时间。 模型完成后，关键字列表将从 **处理** 更改为 **成功** 。 然后，你可以下载该文件。

    ![查看关键字](media/custom-keyword/custom-kws-portal-download-model.png)

1. 下载的文件是 `.zip` 存档文件。 解压缩存档，并看到一个扩展名为的文件 `.table` 。 这是你在下一部分中与 SDK 一起使用的文件，因此请务必记下其路径。 文件名镜像关键字名称，例如，关键字 **激活设备** 具有文件名 `Activate_device.table` 。

## <a name="use-a-keyword-model-with-the-sdk"></a>结合使用关键字模型和 SDK

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

通过 [语音设备 SDK 快速入门](./speech-devices-sdk-quickstart.md?pivots=platform-android)测试自定义关键字。