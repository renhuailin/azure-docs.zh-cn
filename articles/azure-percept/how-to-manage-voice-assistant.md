---
title: 管理 Azure Percept 语音助理应用程序
description: 在 Azure Percept Studio 中配置语音助理应用程序
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/15/2021
ms.custom: template-how-to
ms.openlocfilehash: d66ee6e8e277585061448ecc23e4195c72c04bb6
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123225242"
---
# <a name="manage-your-azure-percept-voice-assistant-application"></a>管理 Azure Percept 语音助理应用程序

本文介绍如何在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 中配置语音助理应用程序的关键字和命令。 有关在 Azure IoT 中心而不是门户中配置关键字的指导，请参阅本[操作指南文章](./how-to-configure-voice-assistant.md)。

如果尚未创建语音助理应用程序，请参阅[使用 Azure Percept Studio 和 Azure Percept Audio 构建无代码语音助理](./tutorial-no-code-speech.md)。

## <a name="keyword-configuration"></a>关键字配置

关键字是用于激活语音助理的单词或短语。 例如，“Hey Cortana”是 Cortana 助理的关键字。 借助语音激活，用户说出关键字即可开始在无需动手的情况下与产品交互。 当产品持续侦听关键字时，所有音频都在设备上进行本地处理，直到检测到关键字，以确保用户数据尽可能保持私有状态。

### <a name="configuration-within-the-voice-assistant-demo-window"></a>语音助理演示窗口中的配置

1. 在演示页面中选择“自定义关键字”旁的“更改” 。

    :::image type="content" source="./media/manage-voice-assistant/hospitality-demo.png" alt-text="酒店餐饮演示窗口的屏幕截图。":::

    如果未打开演示页面，请导航到“设备”页面（请参阅下图），并选择“操作”下的“测试语音助理”以访问演示 。

1. 选择一个可用关键字，然后选择“保存”以应用更改。

1. 配置完成并且语音助理已可供使用时，Azure Percept Audio 设备上的三个 LED 指示灯将变为亮蓝色（不闪烁）。

### <a name="configuration-within-the-device-page"></a>“设备”页面中的配置

1. 在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 的“概述”页面上，选择左侧菜单窗格中的“设备”。

    :::image type="content" source="./media/manage-voice-assistant/portal-overview-devices.png" alt-text="Azure Percept Studio“概述”页面的屏幕截图，其中突出显示了“设备”。":::

1. 选择语音助理应用程序部署到的设备。

1. 打开“语音”选项卡。

    :::image type="content" source="./media/manage-voice-assistant/device-page.png" alt-text="“边缘设备”页面的屏幕截图，其中突出显示了“语音”选项卡。":::

1. 选择“关键字”旁的“更改” 。

    :::image type="content" source="./media/manage-voice-assistant/change-keyword-device.png" alt-text="可用语音解决方案操作的屏幕截图。":::

1. 选择一个可用关键字，然后选择“保存”以应用更改。

1. 配置完成并且语音助理已可供使用时，Azure Percept Audio 设备上的三个 LED 指示灯将变为亮蓝色（不闪烁）。

## <a name="create-a-custom-keyword"></a>创建自定义关键字

使用 [Speech Studio](https://speech.microsoft.com/)，可以为语音助理创建自定义关键字。 训练基本自定义关键字模型需要最长 30 分钟。

有关创建自定义关键字的指南，请参阅 [Speech Studio 文档](../cognitive-services/speech-service/custom-keyword-basics.md)。 配置后，新的关键字将在 Project Santa Cruz 门户中提供，以便用于语音助理应用程序。

## <a name="commands-configuration"></a>命令配置

使用“自定义命令”，可以轻松生成针对语音优先的交互体验进行了优化的丰富语音命令应用。 自定义命令最适用于“任务完成”或“命令与控制”场景。

### <a name="configuration-within-the-voice-assistant-demo-window"></a>语音助理演示窗口中的配置

1. 在“演示”页面中选择“自定义命令”旁的“更改” 。 如果未打开演示页面，请导航到“设备”页面（请参阅下图），并选择“操作”下的“测试语音助理”以访问演示 。

1. 选择一个可用自定义命令，然后选择“保存”以应用更改。

### <a name="configuration-within-the-device-page"></a>“设备”页面中的配置

1. 在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 的“概述”页面上，选择左侧菜单窗格中的“设备”。

1. 选择语音助理应用程序部署到的设备。

1. 打开“语音”选项卡。

1. 选择“命令”旁的“更改” 。

1. 选择一个可用自定义命令，然后选择“保存”以应用更改。

## <a name="create-custom-commands"></a>创建自定义命令

借助 [Speech Studio](https://speech.microsoft.com/)，可以创建由语音助理执行的自定义命令。

有关创建自定义命令的指南，请参阅 [Speech Studio 文档](../cognitive-services/speech-service/quickstart-custom-commands-application.md)。 配置后，新的命令将在 Azure Percept Studio 中提供，以便用于语音助理应用程序。

## <a name="next-steps"></a>后续步骤

生成语音助理应用程序后，请尝试使用 Azure Percept DK 开发[无代码视觉解决方案](./tutorial-nocode-vision.md)。