---
title: 在 Azure Percept Studio 中创建无代码视觉解决方案
description: 了解如何在 Azure Percept Studio 中创建无代码视觉解决方案并将其部署到 Azure Percept DK
author: NabilaBabar
ms.author: amiyouss
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/10/2021
ms.custom: template-tutorial
ms.openlocfilehash: a8d1ce721c1bd67895661be25ac0bab3c230839b
ms.sourcegitcommit: 40866facf800a09574f97cc486b5f64fced67eb2
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/30/2021
ms.locfileid: "123227204"
---
# <a name="create-a-no-code-vision-solution-in-azure-percept-studio"></a>在 Azure Percept Studio 中创建无代码视觉解决方案

借助 Azure Percept Studio，无需编码即可构建和部署自定义计算机视觉解决方案。 在本文中，你将：

- 在 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) 中创建视觉项目
- 使用开发工具包收集训练图像
- 在[自定义视觉](https://www.customvision.ai/)中标记训练图像
- 对自定义对象检测或分类模型进行训练
- 将模型部署到开发工具包
- 通过设置重新训练来改善模型

本教程适合几乎没有 AI 经验和刚开始使用 Azure Percept 的开发人员。

## <a name="prerequisites"></a>先决条件

- Azure Percept DK (devkit)
- [Azure 订阅](https://azure.microsoft.com/free/)
- Azure Percept DK 设置体验：你已将 devkit 连接到 Wi-Fi 网络，创建了 IoT 中心，并已将 devkit 连接到 IoT 中心

## <a name="create-a-vision-prototype"></a>创建视觉原型

1. 启动浏览器并转到 [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819)。

1. 在“概述”页上，单击“演示和教程”选项卡。:::image type="content" source="./media/tutorial-nocode-vision/percept-studio-overview-inline.png" alt-text="Azure Percept Studio“概述”屏幕。" lightbox="./media/tutorial-nocode-vision/percept-studio-overview.png":::

1. 在“视觉教程和演示”下，单击“创建视觉原型” 。

    :::image type="content" source="./media/tutorial-nocode-vision/vision-tutorials-and-demos-inline.png" alt-text="Azure Percept Studio 演示和教程屏幕。" lightbox="./media/tutorial-nocode-vision/vision-tutorials-and-demos.png":::

1. 在“新建 Azure Percept 自定义视觉原型”页面上，执行以下操作：

    1. 在“项目名称”框中，输入视觉原型的名称。

    1. 在“项目说明”框中输入视觉原型的说明。

    1. 在“设备类型”下拉菜单中选择“Azure Percept DK” 。

    1. 选择“资源”下拉菜单下的资源，或单击“创建新资源” 。 如果选择创建新资源，请在“创建”窗口中执行以下操作：
        1. 输入新应用的名称。
        1. 选择 Azure 订阅。
        1. 选择一个资源组或新建一个资源组。
        1. 选择首选区域。
        1. 选择定价层（建议使用 S0）。
        1. 在窗口底部，单击“创建”。

        :::image type="content" source="./media/tutorial-nocode-vision/create-resource.png" alt-text="“创建资源”窗口。":::

    1. 对于“项目类型”，请选择视觉项目是要执行对象检测还是图像分类。 有关项目类型的详细信息，请单击“帮助我选择”。

    1. 对于“优化”，请选择是要针对准确性、低网络延迟还是这两者来优化项目。

    1. 单击“创建”  按钮。

        :::image type="content" source="./media/tutorial-nocode-vision/create-prototype.png" alt-text="“创建自定义视觉原型”页面。":::

## <a name="connect-a-device-to-your-project-and-capture-images"></a>将设备连接到项目并捕获图像

创建视觉解决方案后，必须向其添加开发工具包及其相应的 IoT 中心。

1. 打开开发工具包。

1. 在“IoT 中心”下拉菜单中，选择开发工具包在 OOBE 期间连接到的 IoT 中心。

1. 在“设备”下拉菜单中，选择你的开发工具包。

接下来，必须加载图像或捕获图像来训练 AI 模型。 建议每种标记类型至少上传 30 个图像。 例如，如果想要构建检测狗和猫程序，则必须至少上传 30 张狗狗图像和 30 张猫咪图像。 若要使用开发工具包的视觉 SoM 捕获图像，请执行以下操作：

1. 在“图像捕获”窗口中，选择“查看设备流”来查看视觉 SoM 视频流 。

1. 检查视频流，确保你的视觉 SoM 相机已正确对齐来获取训练图片。 根据需要进行调整。

1. 在“图像捕获”窗口中，单击“拍照” 。

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture.png" alt-text="图像捕获屏幕。":::

1. 或者，可选中“自动图像捕获”框来设置自动图像捕获，以便一次收集大量图像。 在“捕获速率”下选择首选拍照速率，在“目标”下选择想要收集的图像总数 。 单击“设置自动捕获”，开始自动捕获图像的过程。

    :::image type="content" source="./media/tutorial-nocode-vision/image-capture-auto.png" alt-text="“自动图像捕获”下拉菜单。":::

如果照片数充足，请单击屏幕底部的“下一步: 标记图像和模型训练”。 所有图像都将保存在[自定义视觉](https://www.customvision.ai/)中。

> [!NOTE]
> 如果选择将训练图像直接上传到自定义视觉，请注意图像文件大小不能超过 6 MB。

## <a name="tag-images-and-train-your-model"></a>标记图像并训练模型

在训练模型之前，请向图像添加标记。

1. 在“标记图像和模型训练”页面上，单击“在自定义视觉中打开项目” 。

1. 在“自定义视觉”页面的左侧，单击“标记”下的“未标记”，查看你刚才在上一步中收集的图像  。 选择一个或多个未标记的图像。

1. 在“图像详细信息”窗口中，单击图像来开始标记。 如果选择“对象检测”作为项目类型，则还必须在要标记的特定对象周围绘制一个[边界框](../cognitive-services/custom-vision-service/get-started-build-detector.md#upload-and-tag-images)。 根据需要调整边界框。 键入对象标记，然后单击 + 来应用标记。 例如，如果你要创建一个视觉解决方案，使其在货架需要补货时通知你，那么需要向空货架图像添加“空货架”标记，向满货架图像添加“满货架”标记。 对所有未标记的图像重复此操作。

    :::image type="content" source="./media/tutorial-nocode-vision/image-tagging.png" alt-text="自定义视觉中的图像标记屏幕。":::

1. 标记图像后，单击窗口右上角的 X 图标。 单击“标记”下的“已标记”可查看所有新标记的图像 。

1. 标记图像后，即可训练 AI 模型了。 为此，请单击页面顶部附近的“训练”。 每种标记类型必须至少有 15 张图像来训练模型（建议至少使用 30 张图像）。 训练通常耗时约 30 分钟，但如果图像集非常大，则可能需要更长时间。

    :::image type="content" source="./media/tutorial-nocode-vision/train-model.png" alt-text="突出显示了“训练”按钮的训练图像选项。":::

1. 训练完成后，屏幕将显示模型的性能。 若要详细了解如何评估这些结果，请查看[模型评估文档](../cognitive-services/custom-vision-service/get-started-build-detector.md#evaluate-the-detector)。 训练后，你可能还希望针对其他图像[测试模型](../cognitive-services/custom-vision-service/test-your-model.md)并根据需要重新训练。 每次训练模型时，模型都会另存为新的迭代。 若要详细了解如何提高模型性能，请查看[自定义视觉文档](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)。

    :::image type="content" source="./media/tutorial-nocode-vision/iteration.png" alt-text="模型训练结果。":::

    > [!NOTE]
    > 如果选择在自定义视觉中针对其他图像测试你的模型，请注意测试图像文件大小不能超过 4 MB。

对模型的性能感到满意后，请关闭浏览器标签页来关闭自定义视觉。

## <a name="deploy-your-ai-model"></a>设计 AI 模型

1. 回到“Azure Percept Studio”选项卡，然后单击“下一步: 评估和部署”。

1. “评估和部署”窗口将显示所选模型迭代的性能。 在“模型迭代”下拉菜单中选择要部署到开发工具包的迭代，然后单击屏幕底部的“部署模型” 。

    :::image type="content" source="./media/tutorial-nocode-vision/deploy-model-inline.png" alt-text="模型部署屏幕。" lightbox="./media/tutorial-nocode-vision/deploy-model.png":::

1. 部署模型后，查看设备的视频流来了解模型推理的实际应用情况。

    :::image type="content" source="./media/tutorial-nocode-vision/view-device-stream.png" alt-text="显示耳机检测程序实际应用情况的设备流。":::

关闭此窗口后，可在 Azure Percept Studio 主页上的“AI 项目”下单击“视觉”，然后选择你的视觉项目名称，随时回来编辑该项目 。

:::image type="content" source="./media/tutorial-nocode-vision/vision-project-inline.png" alt-text="视觉项目页面。" lightbox="./media/tutorial-nocode-vision/vision-project.png":::

## <a name="improve-your-model-by-setting-up-retraining"></a>通过设置重新训练来改善模型

在对模型进行训练并将其部署到设备后，可以通过设置重新训练参数来捕获更多训练数据，从而提高模型的性能。 此功能可让你基于概率范围捕获图像，从而改善经过训练的模型的性能。 例如，可以将设备设置为仅在概率较低时捕获训练图像。 下面是有关如何添加更多图像以及如何平衡训练数据的一些[附加指导](../cognitive-services/custom-vision-service/getting-started-improving-your-classifier.md)。

1. 若要设置重新训练，请返回到“项目”，然后返回到“项目摘要”
1. 在“图像捕获”选项卡中，选择“自动图像捕获”和“设置重新训练”。
1. 选中“自动图像捕获”框来设置自动图像捕获，以便一次收集大量图像。
1. 在“捕获速率”下选择首选拍照速率，在“目标”下选择想要收集的图像总数 。
1. 在“设置重新训练”部分中，选择要为其捕获更多训练数据的迭代，然后选择概率范围。 仅会将满足概率要求的图像上传到你的项目。

    :::image type="content" source="./media/tutorial-nocode-vision/vision-image-capture.png" alt-text="捕获图像。":::

## <a name="clean-up-resources"></a>清理资源

如果为本教程创建了新的 Azure 资源，但不再想要开发或使用视觉解决方案，请执行以下步骤来删除资源：

1. 转到 [Azure 门户](https://ms.portal.azure.com/)。
1. 单击“所有资源”。
1. 单击在本教程中创建的资源旁边的复选框。 资源类型将作为“认知服务”列出。
1. 单击屏幕顶部附近的“删除”图标。

## <a name="video-walkthrough"></a>视频演练

有关上述步骤的直观演练，请参阅以下视频：

</br>

> [!VIDEO https://www.youtube.com/embed/9LvafyazlJM]

</br>

## <a name="next-steps"></a>后续步骤

接下来，请查看视觉操作方法文章，了解 Azure Percept Studio 中的其他视觉解决方案功能。

<!--
Add links to how-to articles and oobe article.
-->