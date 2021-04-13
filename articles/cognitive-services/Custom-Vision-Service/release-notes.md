---
title: 发行说明 - 自定义影像服务
titleSuffix: Azure Cognitive Services
description: 从自定义视觉团队获取有关新版本的最新信息。
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: conceptual
ms.date: 04/03/2019
ms.author: pafarley
ms.openlocfilehash: 3724a7d515197c1f969bb051fc201b82bee64c42
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 03/19/2021
ms.locfileid: "97813490"
---
# <a name="custom-vision-service-release-notes"></a>自定义影像服务发行说明

## <a name="may-2-2019-and-may-10-2019"></a>2019 年 5 月 2 日和 2019 年 5 月 10 日

- Bug 修复和后端改进

## <a name="may-23-2019"></a>2019 年 5 月 23 日

- 改善了与 Azure 订阅相关的门户 UX 体验，可以更轻松地选择 Azure 目录。

## <a name="april-18-2019"></a>2019 年 4 月18 日 

- 为视觉 AI 开发工具包添加了物体检测导出。
- UI 调整，包括项目搜索。

## <a name="april-3-2019"></a>2019 年 4 月 3 日

- 将每个图像边界框的数量限制增加到了 200。 
- Bug 修复，包括导出到 TensorFlow 的模型的重大性能更新。 

## <a name="march-26-2019"></a>2019 年 3 月 26 日

- 自定义视觉服务已开始在 Azure 上正式发布！
- 添加了具有全新机器学习后端的高级训练功能以提高性能，尤其是在难度较大的数据集和精细分类方面。 利用高级训练，你可以指定用于训练的计算时间预算，而自定义视觉则会以实验方式确定最佳训练和增强设置。 为了进行快速迭代，你可以继续使用现有的快速训练。
- 引入了 3.0 API。 宣布了将于 2019 年 10 月 1 日弃用早于 3.0 的 API。 请参阅[快速入门](./quickstarts/image-classification.md)文档，以获取有关如何入门的示例。
- 3\.0 API 中已将“默认迭代”替换为“发布/取消发布”。
- 添加了新模型导出目标。 Dockerfile 导出已升级为支持适用于 Raspberry Pi 3 的 ARM。 已为[视觉 AI 开发工具包](https://visionaidevkit.com/)添加了导出支持。
- 对于 S0 层，已将每个项目的标记数量的限制增加到了 500。 对于 S0 层，已将每个项目的图像数量的限制增加到了 100,000。
- 删除了成人域。 建议改用常规域。
- 宣布了正式版的[定价](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/)。  

## <a name="february-25-2019"></a>2019 年 2 月 25 日

- 宣布了有限试用版项目（未与 Azure 资源关联的项目）结束，因为自定义视觉即将完成向 Azure 公共预览版的转移。 从 2019 年 3 月 25 日开始，CustomVision.ai 站点将只支持查看与 Azure 资源关联的项目，如免费的自定义视觉资源。 在 2019 年 10 月 1 日之前，仍可通过自定义视觉 API 访问现有的有限试用版项目。 这样你就会有时间为任何使用自定义视觉编写的应用更新 API 密钥。 在 2019 年 10 月 1 日之后，将会删除任何尚未移到 Azure 的有限试用版项目。

## <a name="january-22-2019"></a>2019 年 1 月 22 日

- 添加了对下述新 Azure 区域的支持：美国西部 2、美国东部、美国东部 2、欧洲西部、欧洲北部、东南亚、澳大利亚东部、印度中部、英国南部、日本东部和美国中北部。 对美国中南部的持续支持。

## <a name="december-12-2018"></a>2018 年 12 月 12 日

- 支持导出对象检测模型（引入了对象检测 Compact 域）。
- 修复了大量辅助功能问题以获得改进的屏幕阅读器和键盘导航支持。
- 对图像查看器进行了 UX 更新并改进了对象检测标记体验以便更快速地进行标记。  
- 更新了对象检测域的基础模型以获得更好质量的对象检测。
- bug 修复。

## <a name="november-6-2018"></a>2018 年 11 月 6 日

- 添加了对对象检测中的徽标域的支持。

## <a name="october-9-2018"></a>2018 年 10 月 9 日

- 对象检测进入付费预览版。 现在可以使用 Azure 资源创建对象检测项目。
- 向网站添加了“移至 Azure”功能，以便更轻松地升级有限试用版项目以链接到 Azure。 资源链接的项目（F0 或 S0）。可以在产品的“设置”页上找到此信息。  
- 添加了导出到 ONNX 1.2 的功能，以支持 Windows ML 的 Windows 2018 10 月更新版本。
bug 修复，包括对带有特殊字符的 ONNX 导出的 bug 修复。

## <a name="august-14-2018"></a>2018 年 8 月 14 日

- 在 customvision.ai 网站上添加了“入门”小部件，以指导用户完成项目培训。
- 进一步改进机器学习管道，使多标签项目受益（新的损失层）。

## <a name="june-28-2018"></a>2018 年 6 月 28 日

- bug 修复和后端改进。
- 启用了多类分类，针对其中的图像正好只有一个标签的项目。 在多类模式的预测中，概率将共计为 1（所有图像都会按你指定的标签来分类）。

## <a name="june-13-2018"></a>2018 年 6 月 13 日

- UX 刷新，侧重于易用性和可访问性。
- 改进了机器学习管道，使具有大量标签的多标签项目受益。
- 修复了 TensorFlow 导出中的 bug。 启用了导出的模型版本控制，便于多次导出迭代。

## <a name="may-7-2018"></a>2018 年 5 月 7 日

- 针对有限试用项目引入了对象检测预览版功能。
- 升级到 2.0 API
- S0 层已扩展为最多支持 250 个标记和 50,000 张图像。
- 针对图像分类项目所用的机器学习管道做出了重大的后端改进。 在 2018 年 4 月 27 日之后训练的项目将受益于这些更新。
- 添加了将模型导出到 ONNX 的功能，可在 Windows 机器学习中使用此功能。
- 添加了将模型导出到 Dockerfile 的功能。 这样，你便可以下载项目，以生成自己的 Windows 或 Linux 容器，包括 DockerFile、TensorFlow 模型和服务代码。
- 对于已导出到常规（精简）和地标（精简）域中 TensorFlow 的新训练的模型，[中间值现在为 (0,0,0)](https://github.com/azure-samples/cognitive-services-android-customvision-sample)，目的是在所有项目之间保持一致性。

## <a name="march-1-2018"></a>2018 年 3 月 1 日

- 推出了付费预览版，并将其载入到了 Azure 门户中。 现在，可将项目附加到 F0（免费）或 S0（标准）层的 Azure 资源。 引入了 S0 层项目，最多允许 100 个标记和 25,000 张图像。
- 对机器学习管道/规范化参数做了后端更改。 客户在调整概率阈值时，可以更好地控制精度和召回率之间的权衡。 作为这些更改的一部分，CustomVision.ai 门户中的默认概率阈值已设置为 50%。

## <a name="december-19-2017"></a>2017 年 12 月 19 日

- 添加了导出到 Android (TensorFlow) 的功能，此前已发布导出到 iOS (CoreML) 的功能。因此，可以导出已训练的精简模型，以便在应用程序中脱机运行。
- 添加了零售和地标“精简”域，以支持对这些域使用模型导出。
- 已发布版本 [1.2 训练 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/f2d62aa3b93843d79e948fe87fa89554/operations/5a3044ee08fa5e06b890f11f) 和 [1.1 预测 API](https://southcentralus.dev.cognitive.microsoft.com/docs/services/57982f59b5964e36841e22dfbfe78fc1/operations/5a3044f608fa5e06b890f164)。 已更新 API 支持模型导出（不会将图像保存到“预测”的新预测操作），并在训练 API 中引入了批处理操作。
- UX 经过调整，现在可以查看使用了哪个域来训练迭代。
- 更新了 [C# SDK 和示例](https://github.com/Microsoft/Cognitive-CustomVision-Windows)。