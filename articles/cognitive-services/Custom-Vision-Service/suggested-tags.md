---
title: 使用智能标记工具更快地标记图像
titleSuffix: Azure Cognitive Services
description: 本指南介绍如何使用智能标记工具为图像生成建议的标记。 这使你可以在训练自定义视觉模型时更快地标记大量图像。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 10/29/2019
ms.author: pafarley
ms.openlocfilehash: 94ca47e6114e4f8c3485f6072facd07c25e4b96a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/30/2021
ms.locfileid: "73647751"
---
# <a name="label-images-faster-with-smart-labeler"></a>使用智能标记工具更快地标记图像

本指南介绍如何使用智能标记工具为图像生成建议的标记。 这使你可以在训练自定义视觉模型时更快地标记大量图像。

为自定义视觉模型标记图像时，该服务将使用已训练的最新模型迭代来预测未标记图像的标签。 然后，根据所选的可信度阈值和预测不确定性将这些预测显示为建议的标记。 随后可以确认或更改建议，从而加速手动标记图像以进行训练的过程。

## <a name="when-to-use-smart-labeler"></a>何时使用智能标记工具

请谨记以下限制：

* 应仅为内容已经过一次训练的图像请求建议的标记。 不要为刚开始训练的新标记获取建议。

> [!IMPORTANT]
> 智能标记工具功能使用与常规预测相同的[定价模型](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。 首次为一组图像触发建议的标记时，将按与预测调用相同的方式进行收费。 之后，该服务会将所选图像的结果存储在数据库中 30 天，你可以在该时间段内随时免费访问这些结果。 30 天后，如果再次请求建议的标记，则会向你收费。

## <a name="smart-labeler-workflow"></a>智能标记工具工作流

以下步骤演示如何使用智能标记工具：

1. 将所有已训练的图像上传到自定义视觉项目。
1. 标记部分数据集，为每个标记选择相同数量的图像。
    > [!TIP]
    > 请确保使用稍后想要获取建议的所有标记。
1. 启动训练过程。
1. 训练完成后，导航到“未标记”视图，然后在左窗格中选择“获取建议的标记”按钮。
    > [!div class="mx-imgBorder"]
    > ![“建议的标记”按钮显示在“未标记的图像”选项卡下。](./media/suggested-tags/suggested-tags-button.png)
1. 在出现的弹出窗口中，设置想要获取建议的图像数。 只应获取部分未标记图像的初始标记建议。 循环访问此过程时，将获得更好的标记建议。
1. 确认建议的标记，修复不正确的标记。
    > [!TIP]
    > 带有建议标记的图像按其预测不确定性进行排序（值越小表示可信度越高）。 可以使用“按不确定性排序”选项更改排序顺序。 如果将顺序设置为“从高到低”，则可以先更正高确定性预测，然后快速地确认低不确定性预测。
    * 在图像分类项目中，可以批量选择并确认标记。 按给定的建议标记筛选视图，取消选择未正确标记的图像，然后批量确认其余部分。
        > [!div class="mx-imgBorder"]
        > ![对于带有筛选器的 IC，建议的标记以批量模式显示。](./media/suggested-tags/ic-batch-mode.png)

        还可以通过从库中选择图像，在各个图像模式中使用建议的标记。

        ![对于 IC，建议的标记以单个图像模式显示。](./media/suggested-tags/ic-individual-image-mode.png)
    * 在对象检测项目中，不支持批量确认，但仍可以按建议的标记进行筛选和排序，以获得更有条理的标记体验。 未标记图像的缩略图将显示边框的覆盖，指示建议标记的位置。 如果未选择建议的标记筛选器，则所有未标记的图像将不带覆盖边框。
        > [!div class="mx-imgBorder"]
        > ![对于带有筛选器的 OD，建议的标记以批量模式显示。](./media/suggested-tags/od-batch-mode.png)

        若要确认对象检测标记，需要将其应用于库中的每个单个图像。

        ![对于 OD，建议的标记以单个图像模式显示。](./media/suggested-tags/od-individual-image-mode.png)
1. 再次启动训练过程。
1. 重复上述步骤，直到对建议质量感到满意为止。

## <a name="next-steps"></a>后续步骤

按照快速入门开始创建和训练自定义视觉项目。

* [生成分类器](getting-started-build-a-classifier.md)
* [生成对象检测器](get-started-build-detector.md)