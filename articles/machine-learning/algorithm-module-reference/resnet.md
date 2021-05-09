---
title: ResNet
titleSuffix: Azure Machine Learning
description: 了解如何在 Azure 机器学习设计器中使用 ResNet 算法创建图像分类模型。
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 09/26/2020
ms.openlocfilehash: 685f919fa0b6b0452d79ed0f2d30fdc119a6540f
ms.sourcegitcommit: 12f15775e64e7a10a5daebcc52154370f3e6fa0e
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/26/2021
ms.locfileid: "108000865"
---
# <a name="resnet"></a>ResNet

本文介绍如何使用 Azure 机器学习设计器中的 ResNet 模块来通过 ResNet 算法创建图像分类模型。  

此分类算法是一种监督式学习方法，需要一个标记的数据集。 
> [!NOTE]
> 此模块不支持从工作室中的“数据标签”生成的带标签的数据集，仅支持从[转换为图像目录](convert-to-image-directory.md)模块生成的带标签的图像目录。 

可通过提供模型和带标记的图像目录作为[训练 Pytorch 模型](train-pytorch-model.md)模块的输入来训练模型。 然后，可使用训练后的模型来预测使用[为图像模型评分](score-image-model.md)的新输入示例的值。

### <a name="more-about-resnet"></a>关于 ResNet 的详细信息

请参阅[本文](https://pytorch.org/vision/stable/models.html#torchvision.models.resnext101_32x8d)，详细了解 ResNet。

## <a name="how-to-configure-resnet"></a>如何配置 ResNet

1.  在设计器中将 ResNet 模块添加到管道。  

2.  对于“模型名称”，指定特定 ResNet 结构的名称，然后可以从支持的 resnet 中进行选择：“resnet18”、“resnet34”、“resnet50”、“resnet101”、“resnet152”、“resnet152”、“resnext50\_32x4d”、“resnext101\_32x8d”、“wide_resnet50\_2”、“wide_resnet101\_2”。

3.  对于“预先训练”，指定是否使用在 ImageNet 上预先训练的模型。 如果已选择，则可以根据选定的预训练模型来微调模型；如果已取消选择，则可以从头开始训练。

4.  对于零初始化残留，指定是否将每个残留分支中的最后一个批处理标准层执行零初始化。 如果选择此选项，则残留分支以零开始，每个残留块的行为与标识类似。 这有助于根据 https://arxiv.org/abs/1706.02677 的大型批大小进行收敛。

5.  将 ResNet 模块、训练和验证图像数据集模块的输出连接到[训练 Pytorch 模型](train-pytorch-model.md)。 

6.  提交管道。

## <a name="results"></a>结果

管道运行完成后，若要使用模型进行评分，请将[训练 PyTorch 模型](train-pytorch-model.md)连接到[为图像模型评分](score-image-model.md)，以预测新输入示例的值。

## <a name="technical-notes"></a>技术说明  

###  <a name="module-parameters"></a>模块参数  

| 名称       | 范围 | 类型    | 默认           | 说明                              |
| ---------- | ----- | ------- | ----------------- | ---------------------------------------- |
| 模型名称 | 任意   | Mode    | resnext101\_32x8d | 特定 ResNet 结构的名称       |
| 经过预先训练 | 任意   | 布尔 | True              | 是否使用在 ImageNet 上预先训练的模型 |
| 零初始化残留 | 任意 | 布尔 | False | 是否对每个残留分支中的最后一个批处理标准层执行零初始化 |
|            |       |         |                   |                                          |

###  <a name="output"></a>输出  

| 名称            | 类型                    | 说明                              |
| --------------- | ----------------------- | ---------------------------------------- |
| 未训练的模型 | UntrainedModelDirectory | 可连接到训练 Pytorch 模型的未训练的 ResNet 模型。 |

## <a name="next-steps"></a>后续步骤

请参阅 Azure 机器学习的[可用模块集](module-reference.md)。 