---
title: 改善模型-自定义影像服务
titleSuffix: Azure Cognitive Services
description: 在本文中，你将了解如何在自定义视觉服务中提高数据量、质量和各种数据的质量。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 02/09/2021
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
ms.openlocfilehash: ae0112292994fdcf88e80abff8ab52e5971cb0ed
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/03/2021
ms.locfileid: "101731079"
---
# <a name="how-to-improve-your-custom-vision-model"></a>如何提高自定义视觉模型

在本指南中，您将学习如何提高自定义影像服务模型的质量。 [分类器](./getting-started-build-a-classifier.md)或[对象探测器](./get-started-build-detector.md)的质量取决于你提供的标记数据的数量、质量和种类，以及总体数据集的平衡方式。 良好的模型包含一个平衡的定型数据集，代表将提交给它的内容。 构建此类模型的过程是迭代的;经常需要几轮培训，才能获得预期结果。

下面是一个常规模式，可帮助你训练更准确的模型：

1. 第一轮定型
1. 添加更多的图像并平衡数据；重新训练
1. 添加具有不同背景、照明、对象大小、相机角度和样式的图像；重新训练
1. 使用新图像来测试预测
1. 根据预测结果修改现有的训练数据

## <a name="prevent-overfitting"></a>阻止过度拟合

有时，模型将了解如何根据映像共有的任意特征进行预测。 例如，创建苹果与柑橘的分类器时，如果使用了手中的苹果和白盘中的柑橘的图像，则分类器可能会过分注重于手与白盘，而不是苹果与柑橘。

![意外分类的图像](./media/getting-started-improving-your-classifier/unexpected.png)

若要更正此问题，请提供具有不同角度、背景、对象大小、组和其他变体的图像。 以下各节将对这些概念进行扩展。

## <a name="data-quantity"></a>数据数量

定型图像的数目是数据集最重要的因素。 建议为每个标签使用至少50映像作为起点。 使用更少的图像时，过度拟合的风险会提高，同时，性能数字可能会指出质量良好，但模型可能很难处理实际数据。 

## <a name="data-balance"></a>数据平衡

考虑训练数据的相对数量也很重要。 例如，对两个不同的标签分别使用 50 和 500 个图像会使训练数据集不平衡。 这使得模型对其中一个标签的预测准确度高于另一个标签。 如果在图像最少的标签与图像最多的标签之间至少保持 1:2 的比例，则可能会看到更好的结果。 例如，如果图像最多的标签有 500 个图像，则图像最少的标签应该至少有 250 个图像用于训练。

## <a name="data-variety"></a>数据多样性

请务必使用在正常使用期间提交到分类器的代表性图像。 否则，你的模型将了解如何根据你的图像共有的任意特征进行预测。 例如，创建苹果与柑橘的分类器时，如果使用了手中的苹果和白盘中的柑橘的图像，则分类器可能会过分注重于手与白盘，而不是苹果与柑橘。

![意外分类的图像](./media/getting-started-improving-your-classifier/unexpected.png)

若要更正此问题，请包括各种图像，以确保模型可以很好地通用化。 以下是可使定型集更加多样化的一些方法：

* __背景：__ 在不同背景的前面提供对象图像。 自然场景中的照片比中性背景前的照片更好，因为前者为分类器提供了更多信息。

    ![背景图像示例](./media/getting-started-improving-your-classifier/background.png)

* __照明：__ 提供具有各种光照 (的图像，这些图像使用闪光、高曝光度等) ，尤其是用于预测的图像具有不同的光源。 使用不同饱和度、色调和亮度的图像也很有帮助。

    ![光照图像示例](./media/getting-started-improving-your-classifier/lighting.png)

* __对象大小：__ 提供一个图像，其中的对象大小和数量 (例如，bananas 的部分，以及单个香蕉) 的特写。 不同的大小有助于分类器涵盖所有方面。

    ![大小图像示例](./media/getting-started-improving-your-classifier/size.png)

* 照相机角度：提供以不同照相机角度拍摄的图像。 或者，如果必须使用固定相机（例如监控摄像头）拍摄所有照片，请确保为每个定期出现的对象分配不同的标签，以避免过度拟合 &mdash; 将不相关的对象（例如灯柱）解释为关键特征。

    ![角度图像示例](./media/getting-started-improving-your-classifier/angle.png)

* __样式：__ 提供相同类的不同样式的图像 (例如，相同的水果) 不同。 但是，如果对象的样式有很大的差异（例如，“米老鼠”与现实的老鼠），我们建议将它们标记为单独的类，以更好地表示其独特特征。

    ![样式图像示例](./media/getting-started-improving-your-classifier/style.png)

## <a name="negative-images-classifiers-only"></a>仅 (分类器的负片图像) 

如果你使用的是映像分类器，则可能需要添加 _否定性样本_ ，以帮助你的分类器更准确。 负样本是不匹配任何其他标记的图像。 上传这些图像时，请向其添加特殊的负标签。

对象检测程序自动处理负值样本，因为绘制的边界框之外的任何图像区域均被视为负值。

> [!NOTE]
> 自定义影像服务支持某些自动负片图像处理。 例如，如果要生成葡萄与香蕉的分类器并提交一张鞋子图像用于预测，则分类器对于该图像的葡萄与香蕉评分均应接近 0%。
> 
> 另一方面，如果负片图像仅仅是定型中使用的图像的变量，由于相似性很大，模型很可能将负片图像分类为标记类。 例如，如果具有一个橙子与葡萄柚分类器，并且输入克莱门氏小柑橘的图像，则它可能将克莱门氏小柑橘分类为橙子，因为克莱门氏的许多特征类似于橙子。 如果负片图像具有这种性质，建议在训练期间创建一个或多个单独的标签（例如，“其他”）并使用此标签标记负片图像，使模型更好地区分这些类。

## <a name="consider-occlusion-and-truncation-object-detectors-only"></a>请考虑仅)  (对象的封闭和截断

如果希望对象探测器检测到截断的对象 (对象已部分地从图像中剪切) 或封闭像素对象 (对象已被图像) 中的另一个对象部分阻止，则需要包括包含这些情况的培训映像。

> [!NOTE]
> 其他对象封闭像素的对象的问题不会与 **重叠阈值** 混淆，后者用于评级模型的性能。 [自定义视觉网站](https://customvision.ai)上的 "**重叠阈值**" 滑块用于处理预测的边界框必须与真正的边界框重叠多少，才能视为正确。

## <a name="use-prediction-images-for-further-training"></a>使用预测图像进一步训练

如果通过将图像提交到预测终结点来使用或测试模型，则自定义视觉服务将存储这些图像。 然后，你可以使用这些图像来改进模型。

1. 若要查看提交到模型的图像，请打开 [自定义视觉](https://customvision.ai)网页，然后选择 " __预测__ " 选项卡。默认视图显示当前迭代中的图像。 可使用“迭代”下拉菜单查看先前迭代期间提交的图像。

    ![预测选项卡的屏幕截图，视图中包含图像](./media/getting-started-improving-your-classifier/predictions.png)

2. 将鼠标悬停在图像上可查看模型预测的标记。 对图像进行排序，以便最大程度地提高模型的最大改进。 若要使用不同的排序方法，请在“排序”部分做出选择。 

    若要将某个图像添加到现有训练数据，请选择该图像，设置正确的标记，然后单击“保存并关闭”。 该图像将从“预测”中删除，并添加到训练图像集。 可选择“定型图像”选项卡查看该图像。

    ![“标记”页面的图像](./media/getting-started-improving-your-classifier/tag.png)

3. 然后使用 " __训练__ " 按钮重新训练该模型。

## <a name="visually-inspect-predictions"></a>直观检查预测

若要检查图像预测，请转到“训练图像”选项卡，在“迭代”下拉菜单中选择前一个训练迭代，然后检查“标记”部分下的一个或多个标记。 现在，视图应会围绕其模型未能正确预测给定标记的每个图像显示一个红框。

![迭代历史记录的图像](./media/getting-started-improving-your-classifier/iteration.png)

有时，视觉检查可以识别模式，然后，你可通过添加更多训练数据或修改现有训练数据来纠正这些模式。 例如，苹果与酸橙的分类器可能会错误地将所有青苹果标记为酸橙。 可以通过添加并提供包含青苹果的已标记图像的训练数据来纠正此问题。

## <a name="next-steps"></a>后续步骤

在本指南中，您学习了几种方法，使您的自定义映像分类模型或对象探测器模型更加准确。 接下来，请了解如何通过将图像提交到预测 API 来以编程方式测试这些图像。

> [!div class="nextstepaction"]
> [使用预测 API](use-prediction-api.md)