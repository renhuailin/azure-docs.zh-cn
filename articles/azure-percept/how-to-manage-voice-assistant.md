---
title: 在 Azure Percept Studio 中配置语音助手应用程序
description: 在 Azure Percept Studio 中配置语音助手应用程序
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: 593d2ccadf814cb5af27a4bb738f28132f39ab7d
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/02/2021
ms.locfileid: "101661883"
---
# <a name="managing-your-voice-assistant"></a>管理语音助手

本文介绍如何在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)中配置语音助手应用程序的关键字和命令。 有关在 IoT 中心而不是门户中配置关键字的指导，请参阅本操作 [指南文章](./how-to-configure-voice-assistant.md)。

如果尚未创建语音助手应用程序，请参阅 [使用 Azure Percept Studio 和 Azure Percept 音频生成无代码语音助手](./tutorial-no-code-speech.md)。

## <a name="keyword-configuration"></a>关键字配置

关键字是用于激活语音助手的单词或短语。 例如，"你好 Cortana" 是 Cortana 助手的关键字。 语音激活使用户能够通过简单地使用关键字来完全自由地与产品交互。 当你的产品持续侦听关键字时，将在设备上本地处理所有音频，直到发生检测，以确保用户数据保持尽可能专用。

### <a name="configuration-within-the-voice-assistant-demo-window"></a>语音助手演示窗口中的配置

1. 单击 "演示" 页上的 "**自定义关键字**" 旁边的 "**更改**"。

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="影像.":::

    如果未打开演示页面，请导航到 "设备" 页， (参见下面的 ") 并单击"**操作**"下的"**测试语音助手**"以访问演示。

1. 选择一个可用关键字，并单击 " **保存** " 以应用更改。

1. 当配置完成时，Azure Percept 音频设备上的三个 LED 指示灯将变为浅蓝色 (不闪烁) ，你的语音助手就可以使用了。

### <a name="configuration-within-the-device-page"></a>设备页中的配置

1. 在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)的 "概述" 页上，单击左侧菜单窗格中的 " **设备** "。

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="影像.":::

1. 选择您的语音助手应用程序部署到的设备。

1. 打开 " **语音** " 选项卡。

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="影像.":::

1. 单击 "**关键字**" 旁边的 "**更改**"。

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="影像.":::

1. 选择一个可用关键字，并单击 " **保存** " 以应用更改。

1. 当配置完成时，Azure Percept 音频设备上的三个 LED 指示灯将变为浅蓝色 (不闪烁) ，你的语音助手就可以使用了。

## <a name="create-a-custom-keyword"></a>创建自定义关键字

使用 [Speech Studio](https://speech.microsoft.com/)，可以为语音助手创建自定义关键字。 训练基本自定义关键字模型最多需要30分钟。

有关创建自定义关键字的指南，请 [参阅 Speech Studio 文档](https://docs.microsoft.com/azure/cognitive-services/speech-service/speech-devices-sdk-create-kws) 。 配置后，你的新关键字将出现在项目的 Cruz 门户中，以便与你的语音助手应用程序一起使用。

## <a name="commands-configuration"></a>命令配置

自定义命令可让你轻松地构建为语音首次交互体验优化的丰富语音命令应用。 自定义命令最适用于任务完成或命令和控制方案。

### <a name="configuration-within-the-voice-assistant-demo-window"></a>语音助手演示窗口中的配置

1. 单击 "演示" 页上的 "**自定义命令**" 旁边的 "**更改**"。 如果未打开演示页面，请导航到 "设备" 页， (参见下面的 ") 并单击"**操作**"下的"**测试语音助手**"以访问演示。

1. 选择可用的自定义命令之一，并单击 " **保存** " 以应用更改。

### <a name="configuration-within-the-device-page"></a>设备页中的配置

1. 在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)的 "概述" 页上，单击左侧菜单窗格中的 " **设备** "。

1. 选择您的语音助手应用程序部署到的设备。

1. 打开 " **语音** " 选项卡。

1. 单击 "**命令**" 旁边的 "**更改**"。

1. 选择可用的自定义命令之一，并单击 " **保存** " 以应用更改。

## <a name="create-custom-commands"></a>创建自定义命令

借助 [Speech Studio](https://speech.microsoft.com/)，你可以创建自定义命令，让你的语音助手执行。

按照 [Speech Studio 文档](https://docs.microsoft.com/azure/cognitive-services/speech-service/quickstart-custom-commands-application) 中的说明创建自定义命令。 配置后，你的新命令将在 Azure Percept Studio 中提供，以便与你的语音助手应用程序一起使用。

## <a name="next-steps"></a>后续步骤

生成语音助手应用程序后，请尝试使用 Azure Percept 深色开发 [无代码远景解决方案](./tutorial-nocode-vision.md) 。