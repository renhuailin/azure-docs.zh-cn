---
title: 快速入门：使用“自定义视觉”网站生成对象检测器
titleSuffix: Azure Cognitive Services
description: 本快速入门介绍如何使用“自定义视觉”网站创建、训练并测试对象检测器模型。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: quickstart
ms.date: 09/27/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: 图像识别, 图像识别应用, 自定义视觉
ms.openlocfilehash: 95e1e744db159e53e950aa8c1fc0bd52f10214de
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129359331"
---
# <a name="quickstart-build-an-object-detector-with-the-custom-vision-website"></a>快速入门：使用“自定义视觉”网站生成对象检测器

本快速入门介绍如何使用自定义视觉网站创建对象检测器模型。 生成模型后，可以使用新图像测试该模型，并最终将该模型集成到你自己的图像识别应用中。

如果没有 Azure 订阅，请在开始之前创建一个[免费帐户](https://azure.microsoft.com/free/cognitive-services/)。

## <a name="prerequisites"></a>先决条件

- 一组用于训练检测器模型的图像。 可以使用 GitHub 上的一组[示例图像](https://github.com/Azure-Samples/cognitive-services-python-sdk-samples/tree/master/samples/vision/images)。 或者，可以根据下面的提示选择你自己的图像。

## <a name="create-custom-vision-resources"></a>创建自定义视觉资源

[!INCLUDE [create-resources](includes/create-resources.md)]

## <a name="create-a-new-project"></a>创建新项目

在 Web 浏览器中，导航到[自定义影像服务网页](https://customvision.ai)，然后选择“登录”  。 使用用于登录 Azure 门户的帐户登录。

![“登录”页的图像](./media/browser-home.png)


1. 若要创建首个项目，请选择“新建项目”  。 将出现“创建新项目”对话框  。

    ![“新建项目”对话框中包含名称、描述和域字段。](./media/get-started-build-detector/new-project.png)

1. 输入项目名称和描述。 然后选择一个资源组。 如果登录帐户与 Azure 帐户相关联，则“资源组”下拉列表将显示包含自定义影像服务资源的所有 Azure 资源组。 

   > [!NOTE]
   > 如果没有可用的资源组，请确认已使用登录 [Azure 门户](https://portal.azure.com/)时所用的同一帐户登录 [customvision.ai](https://customvision.ai)。 此外，请确认在自定义视觉网站中选择的“目录”与自定义视觉资源所在 Azure 门户中的目录相同。 在这两个站点中，可从屏幕右上角的下拉帐户菜单中选择目录。 

1. 选择“项目类型”下的“对象检测”。  

1. 接下来，选择一个可用域。 每个域都会针对特定类型的图像优化检测器，如下表所述。 将能够根据需要稍后更改域。

    |域|目的|
    |---|---|
    |__常规__| 已针对各种对象检测任务进行优化。 如果其他域都不合适，或者不确定要选择哪个域，请选择“通用”域。 |
    |__徽标__|已针对在图像中查找品牌徽标进行优化。|
    |__货架上的产品__|已针对检测和分类货架上的产品进行优化。|
    |__压缩域__| 已针对移动设备上实时对象检测的约束进行优化。 可导出压缩域生成的模型在本地运行。|

1. 最后，选择“创建项目”。

## <a name="choose-training-images"></a>选择训练图像

[!INCLUDE [choose training images](includes/choose-training-images.md)]

## <a name="upload-and-tag-images"></a>上传和标记图像

在本部分，你将上传图像并手动标记图像来帮助训练检测器。 

1. 若要添加图像，请选择“添加图像”，然后选择“浏览本地文件” 。 选择“打开”以上传图像。

    ![添加图像控件位于左上角，以按钮形式显示在底部中央。](./media/get-started-build-detector/add-images.png)

1. UI 的“未标记”部分会显示已上传的图像。 下一步是手动标记你希望检测器通过学习识别的对象。 单击第一个图像打开“标记”对话框窗口。 

    ![“未标记”部分中已上传的图像](./media/get-started-build-detector/images-untagged.png)

1. 单击并拖动图像中对象周围的矩形。 然后，使用 **+** 按钮输入新的标记名称，或者从下拉列表中选择现有的标记。 请务必标记要检测的对象的每个实例，因为检测器在训练中使用未标记的背景区域作为负面示例。 完成标记后，单击右侧的箭头保存标记并转到下一个图像。

    ![使用矩形选择控件来标记对象](./media/get-started-build-detector/image-tagging.png)

若要上传另一组图像，请返回到本部分顶部并重复上述步骤。

## <a name="train-the-detector"></a>训练检测器

若要训练检测器模式，请选择“训练”按钮。 检测器将使用所有当前图像及其标记来创建用于识别每个标记对象的模型。

![网页标头工具栏右上角的训练按钮](./media/getting-started-build-a-classifier/train01.png)

此训练过程应该只需要几分钟的时间。 在此期间，会在“性能”选项卡显示有关训练过程的信息。

![主要部分中带有训练对话的浏览器窗口](./media/get-started-build-detector/training.png)

## <a name="evaluate-the-detector"></a>评估检测器

完成训练后，将计算并显示模型的性能。 自定义视觉服务使用提交用于训练的图像来计算精度、召回率和平均精度。 精度和召回率是检测器有效性的两个不同度量值：

- 精确度表示已识别的正确分类的分数。 例如，如果模型将 100 张图像识别为狗，实际上其中 99 张是狗，那么精确度为 99%。
- 召回率表示正确识别的实际分类的分数。 例如，如果实际上有 100 张苹果的图像，并且该模型将 80 张标识为苹果，则召回率为 80%。
- 平均精度是平均精准率 (AP) 的平均值。 AP 是精准率/召回率曲线下的区域（精准率根据每次进行的预测的召回率绘制）。

![训练结果将显示总体精度和召回率，以及平均精度。](./media/get-started-build-detector/trained-performance.png)

### <a name="probability-threshold"></a>概率阈值

[!INCLUDE [probability threshold](includes/probability-threshold.md)]

### <a name="overlap-threshold"></a>重叠阈值

“重叠阈值”滑块处理在训练中必须将对象预测视为“正确”的方式。 此滑块设置预测的对象边界框和实际用户输入的边界框之间允许的最小重叠。 如果边界框未达到这种重叠度，则不会将预测视为正确。

## <a name="manage-training-iterations"></a>管理训练迭代

每次训练检测器时，都会创建一个新的迭代，其中包含其自身的已更新性能指标。 可以在“性能”选项卡的左窗格中查看所有迭代。在左侧窗格中，还可以找到“删除”按钮，如果迭代已过时，可以使用该按钮删除迭代。 删除迭代时，会删除唯一与其关联的所有图像。

请参阅[将模型与预测 API 配合使用](./use-prediction-api.md)，以了解如何以编程方式访问已训练模型。

## <a name="next-steps"></a>后续步骤

本快速入门已介绍如何使用自定义视觉网站创建和训练对象检测器模型。 接下来，获取有关改进模型的迭代过程的详细信息。

> [!div class="nextstepaction"]
> [测试和重新训练模型](test-your-model.md)

* [什么是自定义视觉？](./overview.md)
