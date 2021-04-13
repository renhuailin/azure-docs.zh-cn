---
title: 结合使用 ONNX 模型和 Windows ML - 自定义视觉服务
titleSuffix: Azure Cognitive Services
description: 了解如何创建使用从 Azure 认知服务导出的 ONNX 模型的 Windows UWP 应用。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/29/2020
ms.author: pafarley
ms.openlocfilehash: 5ae333d0693071f1a5a5d12e87d6aa155ceccd3c
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/31/2021
ms.locfileid: "106055563"
---
# <a name="use-an-onnx-model-from-custom-vision-with-windows-ml-preview"></a>结合使用自定义视觉服务 ONNX 模型和 Windows ML（预览版）

了解如何结合使用从自定义影像服务导出的 ONNX 模型和 Windows ML（预览版）。

本指南介绍如何将从自定义视觉服务导出的 ONNX 文件与 Windows ML 结合使用。 你将在示例 UWP 应用程序中使用自己训练的图像分类器。

## <a name="prerequisites"></a>先决条件

* Windows 10 版本 1809 或更高版本
* 适用于内部版本 17763 或更高版本的 Windows SDK
* 启用了“通用 Windows 平台开发”工作负载的 Visual Studio 2017 版本 15.7 或更高版本。
* 已在你的电脑上启用开发人员模式。 有关详细信息，请参阅[启用设备进行开发](/windows/uwp/get-started/enable-your-device-for-development)。

## <a name="about-the-example-app"></a>关于示例应用

包含的应用程序是通用 Windows UWP 应用。 它允许你从计算机中选择一个图像，然后由本地存储的分类模型进行处理。 模型返回的标记和分数显示在图像旁边。

## <a name="get-the-example-code"></a>获取示例代码

GitHub 上的[认知服务 ONNX 自定义视觉示例](https://github.com/Azure-Samples/cognitive-services-onnx-customvision-sample)存储库中提供了示例应用程序。 将其克隆到本地计算机，并在 Visual Studio 中打开 SampleOnnxEvaluationApp.sln。

## <a name="test-the-application"></a>测试应用程序

1. 按 `F5` 键，通过 Visual Studio 启动应用。 系统可能会提示启用“开发人员模式”。
1. 当应用程序启动时，请使用相关按钮来选择要评分的图像。 默认 ONNX 模型经过训练，可对不同类型的浮游生物进行分类。

## <a name="use-your-own-model"></a>使用自己的模型

若要使用你自己的图像分类器模型，请按以下步骤操作：

1. 使用自定义视觉服务创建和训练一个分类器。 有关如何执行此操作的说明，请参阅[创建和训练分类器](./getting-started-build-a-classifier.md)。 使用其中一个压缩域，例如“常规(压缩)” 。 
   * 如果现有分类器使用不同的域，你可在项目设置中将其转换为压缩域。 然后，在继续之前重新训练项目。
1. 导出模型。 切换到“性能”选项卡，然后选择已使用压缩域进行训练的迭代。 选择显示的“导出”按钮。 然后依次选择“ONNX”、“导出” 。 文件就绪以后，请选择“下载”按钮。 有关导出选项的详细信息，请参阅[导出模型](./export-your-model.md)。
1. 打开下载的 .zip 文件并从中提取 model.onnx 文件 。 此文件包含你的分类器模型。
1. 在 Visual Studio 的“解决方案资源管理器”中，右键单击“资产”文件夹，然后选择“添加现有项”。 选择 ONNX 文件。
1. 在“解决方案资源管理器”中，右键单击 ONNX 文件并选择“属性”。 更改文件的以下属性：
   * __生成操作__ -> __内容__
   * __复制到输出目录__ -> __如果较新则复制__
1. 然后打开 MainPage.xaml.cs，并将 `_ourOnnxFileName` 的值更改为 ONNX 文件的名称。
1. 按 `F5` 生成并运行项目。
1. 单击按钮选择要评估的图像。

## <a name="next-steps"></a>后续步骤

要了解导出和使用自定义影像服务模型的其他方法，请参阅以下文档：

* [导出模型](./export-your-model.md)
* [在 Android 应用程序中使用导出的 Tensorflow 模型](https://github.com/Azure-Samples/cognitive-services-android-customvision-sample)
* [在 Swift iOS 应用中使用导出的 CoreML 模型](https://go.microsoft.com/fwlink/?linkid=857726)
* [在带 Xamarin 的 iOS 应用程序中使用导出的 CoreML 模型](https://github.com/xamarin/ios-samples/tree/master/ios11/CoreMLAzureModel)

有关结合使用 ONNX 模型和 Windows ML 的详细信息，请参阅[使用 Windows ML 将模型集成到应用](/windows/ai/windows-ml/integrate-model)。