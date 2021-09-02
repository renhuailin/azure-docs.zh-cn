---
title: 在 Azure Percept Studio 中创建无代码语音助理
description: 了解如何创建无代码语音解决方案并将其部署到 Azure Percept DK
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 0ceb71b5caaf17c485d4f4bd649e6b68d7c72a5d
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123224052"
---
# <a name="create-a-no-code-voice-assistant-in-azure-percept-studio"></a>在 Azure Percept Studio 中创建无代码语音助理

在本教程中，你将从模板创建一个语音助理，以便通过 Azure Percept DK 和 Azure Percept 音频使用。 语音助理演示在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 中运行，该演示包含一系列由语音控制的虚拟对象。 若要控制对象，请先说关键字（即用于唤醒设备的单词或短语），然后再说命令。 每个模板都响应一组特定命令。

本指南将逐步介绍如何设置设备、创建语音助理和必要的[语音服务](../cognitive-services/speech-service/overview.md)资源、测试语音助理、配置关键字以及创建自定义关键字。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- Azure Percept 音频
- 可以连接到 3.5 毫米音频插孔的扬声器或耳机（可选）
- [Azure 订阅](https://azure.microsoft.com/free/)
- [Azure Percept DK 设置体验](./quickstart-percept-dk-set-up.md)：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心
- [Azure Percept 音频设置](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>使用可用模板创建语音助理

1. 转到 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)。

1. 打开“演示和教程”选项卡。

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Azure 门户主页的屏幕截图。":::

1. 单击“语音教程和演示”下的“试用语音助理模板” 。 此时会在屏幕右侧打开一个窗口。

1. 在窗口中执行以下操作：

    1. 在“IoT 中心”下拉菜单中，选择 devkit 连接到的 IoT 中心。

    1. 在“设备”下拉菜单中，选择你的 devkit。

    1. 选择一个可用的语音助理模板。

    1. 单击“我同意此项目的条款和条件”复选框。

    1. 单击“创建”。

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="语音助理模板创建的屏幕截图。":::

1. 单击“创建”后，门户将打开另一个窗口来创建语音主题资源。 在窗口中执行以下操作：

    1. 在“订阅”框中选择 Azure 订阅。

    1. 在“资源组”下拉菜单中选择首选资源组。 如果要创建新的资源组以用于语音助理，请单击下拉菜单下的“创建”，然后按照提示操作。

    1. 对于“应用程序前缀”，请输入一个名称。 这将是项目和自定义命令名称的前缀。

    1. 在“区域”下，选择要将资源部署到的区域。

    1. 在“LUIS 预测定价层”下，选择“标准”（免费层不支持语音请求） 。

    1. 单击“创建”  按钮。 语音助理应用程序的资源将部署到你的订阅中。

        > [!WARNING]
        > 在门户部署完资源之前，请勿关闭此窗口。 提前关闭窗口可能导致语音助理出现意外行为。 部署完资源后，将显示演示。

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="订阅和资源组选择窗口的屏幕截图。":::

## <a name="test-out-your-voice-assistant"></a>测试语音助理

若要与语音助理交互，请先说关键字，然后说命令。 当 Ear SoM 识别出你的关键字时，设备将发出一声钟声（连接扬声器或耳机后可听到），LED 将闪烁蓝色。 处理命令时，LED 将切换为普蓝。 语音助理对命令的响应将以文本形式显示在演示窗口中，并通过扬声器/耳机发出声音。 默认关键字（位于“自定义关键字”旁边）设置为“计算机”，并且每个模板都有一组兼容命令，可使用这些命令与演示窗口中的虚拟对象交互。 例如，如果你使用酒店和医疗保健演示，请说“计算机，打开电视”来打开虚拟电视。

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="酒店餐饮演示窗口的屏幕截图。":::

### <a name="hospitality-and-healthcare-demo-commands"></a>酒店和医疗保健演示命令

医疗保健和酒店演示都有可交互的虚拟电视、灯具、百叶窗和恒温器。 支持以下命令（和其他变体）：

* “开灯/关灯。”
* “打开/关闭电视。”
* “打开/关闭空调。”
* “打开/关闭百叶窗。”
* “将温度设置为 X 度。” （X 是所需的温度，例如 75。）

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="医疗保健演示窗口的屏幕截图。":::

### <a name="automotive-demo-commands"></a>汽车演示命令

汽车演示有可交互的虚拟座椅加热器、除霜器和恒温器。 支持以下命令（和其他变体）：

* “打开/关闭除霜器。”
* “打开/关闭座椅加热器。”
* “将温度设置为 X 度。” （X 是所需的温度，例如 75。）
* “将温度提高/降低 Y 度。”


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="汽车演示窗口的屏幕截图。":::

### <a name="inventory-demo-commands"></a>库存演示命令

库存演示包含一系列可与虚拟库存应用交互的虚拟蓝色、黄色和绿色箱子。 支持以下命令（和其他变体）：

* “添加/删除 X 箱。” （X 是箱数，例如 4。）
* “订购/运送 X 箱。”
* “库存有多少箱？”
* “数一下 Y 箱子。” （Y 是箱子的颜色，如黄色。）
* “运送全部库存。”


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="库存演示窗口的屏幕截图。":::

## <a name="configure-your-keyword"></a>配置关键字

可以为你的语音助理应用程序自定义关键字。

1. 请在演示窗口中单击“自定义关键字”旁边的“更改” 。

1. 选择可用的关键字之一。 可从一系列示例关键字和你创建的任何自定义关键字中进行选择。

1. 单击“ **保存**”。

### <a name="create-a-custom-keyword"></a>创建自定义关键字

可以为你的语音应用程序创建你自己的关键字。 自定义关键字的训练只需几分钟即可完成。

1. 请单击演示窗口顶部附近的“+ 创建自定义关键字”。 

1. 输入所需的关键字（可以是单个词语或短语）。

1. 选择“语音资源”（此项在演示窗口中的“自定义命令”旁边列出，并且包含你的应用程序前缀） 。

1. 单击“ **保存**”。 

## <a name="create-a-custom-command"></a>创建自定义命令

门户还提供使用现有语音资源创建自定义命令的功能。 “自定义命令”指的是语音助理应用程序本身，而不是现有应用程序中的特定命令。 创建自定义命令时，将创建一个新的语音项目，必须在 [Speech Studio](https://speech.microsoft.com/) 中进一步开发它。

若要从演示窗口中创建新的自定义命令，请单击页面顶部的“+ 创建自定义命令”，然后执行以下操作：

1. 输入自定义命令的名称。

1. 输入项目说明（可选）。

1. 选择首选语言。

1. 选择语音资源。

1. 选择 LUIS 资源。

1. 选择 LUIS 创作资源或创建一个新资源。

1. 单击“创建”。

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="自定义命令创建窗口的屏幕截图。":::

创建自定义命令后，必须转到 [Speech Studio](https://speech.microsoft.com/) 进行进一步的开发。 如果打开了 Speech Studio，但未看到列出你的自定义命令，请执行以下步骤：

1. 在 Azure Percept Studio 的左侧菜单面板中，单击“AI 项目”下的“语音” 。

1. 选择“命令”选项卡。

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="可编辑的自定义命令的列表的屏幕截图。":::

1. 选择要开发的自定义命令。 将在 Speech Studio 中打开该项目。

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Speech studio 主屏幕的屏幕截图。":::

有关开发自定义命令的详细信息，请参阅[语音服务文档](../cognitive-services/speech-service/custom-commands.md)。

## <a name="troubleshooting"></a>疑难解答

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>已创建语音助理，但它不响应命令

检查内插板上的 LED 指示灯：

* 三个纯蓝色指示灯表示语音助理已就绪，正在等待关键字。
* 如果中间的 LED (L02) 为白色，则 devkit 已完成初始化，需要配置关键字。
* 如果中间的 LED (L02) 闪烁白色，则 Audio SoM 尚未完成初始化。 初始化可能需要几分钟才能完成。

有关 LED 指示灯的详细信息，请参阅 [LED 文章](./audio-button-led-behavior.md)。

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>语音助理不响应在 Speech Studio 中创建的自定义关键字

如果语音模块已过期，可能会发生这种情况。 请按照以下步骤将语音模块更新到最新版本：

1. 在 Azure Percept Studio 主页的左侧菜单面板中，单击“设备”。

1. 找到并选择你的设备。

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Azure Percept Studio 中设备列表的屏幕截图。":::

1. 在设备窗口中，选择“语音”选项卡。

1. 检查语音模块版本。 如果可更新，版本号旁边会出现“更新”按钮。

1. 单击“更新”以部署语音模块更新。 更新过程通常需要 2-3 分钟才能完成。

## <a name="clean-up-resources"></a>清理资源

使用完语音助理应用程序后，请按照以下步骤清理在本教程中部署的语音资源：

1. 在 [Azure 门户](https://portal.azure.com)的左侧菜单面板中选择“资源组”，或在搜索栏中键入“资源组”。

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="显示左侧菜单面板和资源组的 Azure 门户主页的屏幕截图。":::

1. 选择你的资源组。

1. 选择包含应用程序前缀的所有六项资源，然后单击顶部菜单面板上的“删除”图标。
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="已选中待删除的语音资源的屏幕截图。":::

1. 若要确认删除，请在确认框中键入“yes”，验证你选择了正确的资源，然后单击“删除” 。

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="删除确认窗口的屏幕截图。":::

> [!WARNING]
> 这会删除用你要删除的语音资源创建的任何自定义关键字，并且语音助理演示将会失效。

## <a name="next-steps"></a>后续步骤

现在你已创建了一个无代码语音解决方案，请尝试为你的 Azure Percept DK 创建[无代码影像解决方案](./tutorial-nocode-vision.md)。