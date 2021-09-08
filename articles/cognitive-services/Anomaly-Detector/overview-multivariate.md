---
title: 什么是异常检测器多变量 API？
titleSuffix: Azure Cognitive Services
description: 新异常检测器预览版多变量 API 的概述。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 异常检测, 机器学习, 算法
ms.openlocfilehash: 85c169afb8c6158a3e5c32ac4d62cbeeabb4c2bb
ms.sourcegitcommit: 03f0db2e8d91219cf88852c1e500ae86552d8249
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/27/2021
ms.locfileid: "123035590"
---
# <a name="multivariate-time-series-anomaly-detection-preview"></a>多变量时序异常检测（预览版）

利用新的 **多变量异常情况检测** API，开发人员能够轻松集成高级 AI，从而检测指标组中的异常，并且无需机器学习知识或带标记的数据。 现在，我们会将多达 300 个不同信号之间的依赖关系和相互关联自动视为关键因素。 这一新功能可帮助你主动防止复杂系统发生故障，例如，软件应用程序、服务器、工厂计算机、航天器，或者甚至是业务。

![以下变量的多个时序线图：旋转、滤光器、压力、轴承，其中的异常部分以橙色突出显示](./media/multivariate-graph.png)

假设自动引擎中有 20 个传感器，可生成 20 个不同的信号，例如，旋转、燃料压力、轴承等。这些信号的单独读数可能无法全面反应系统级别的问题，但加以综合则可以反应引擎的运行状况。 当这些信号的交互作用偏离出正常范围时，多变量异常检测功能就可以像经验丰富的专家一样理解异常情况。 底层 AI 模型使用你的数据进行训练和自定义，从而了解业务的独特需求。 现在，利用异常检测器中的新 API，开发人员可以轻松地将多变量时序异常检测功能集成到预测性维护解决方案以及复杂的企业软件或商业智能工具的 AIOps 监视解决方案中。

## <a name="when-to-use-multivariate-versus-univariate"></a>何时使用 **多变量** 与 **单变量**

如果目标是基于历史数据，在每个单独的时序上检测不符合正常模式的异常情况，请使用单变量异常检测 API。 例如，你想要仅根据收入数据来检测每日收入异常，或者想要仅根据 CPU 数据来检测 CPU 峰值。

如果你的目标是从一组时序数据中检测到系统级别的异常，请使用多变量异常情况检测 API。 特别是，当任何单个时序无法全面反应问题时，则必须从整体上查看所有信号（一组时序），从而确定系统级别的问题。 例如，你有一项昂贵的实物资产，比如飞机、石油钻塔上的设备或卫星。 其中每项资产都有数十个或数百个不同类型的传感器。 那么，你必须查看传感器产生的所有时序信号才能确定是否存在系统级别的问题。

## <a name="notebook"></a>笔记本

若要了解如何调用异常检测器 API（多变量），请试用此 [Notebook](https://github.com/Azure-Samples/AnomalyDetector/blob/master/ipython-notebook/Multivariate%20API%20Demo%20Notebook.ipynb)。 此 Jupyter Notebook 演示如何发送 API 请求和直观显示结果。

若要运行 Notebook，应获取有效的异常检测器 API 订阅密钥和 API 终结点。 在笔记本中，将有效的异常检测器 API 订阅密钥添加到 `subscription_key` 变量，并将 `endpoint` 变量更改为你的终结点。

## <a name="region-support"></a>区域支持

异常检测器多变量预览版目前在 10 个 Azure 区域可用：东南亚、澳大利亚东部、加拿大中部、北欧、西欧、美国东部、美国东部 2、美国中南部、美国西部 2 和英国南部。

## <a name="algorithms"></a>算法

如需了解使用的算法，请参阅以下技术文档：

* 博客：[介绍多变量异常情况检测](https://techcommunity.microsoft.com/t5/azure-ai/introducing-multivariate-anomaly-detection/ba-p/2260679)
* 论文：[通过图注意力网络执行多变量时序异常情况检测](https://arxiv.org/abs/2009.02040)


> [!VIDEO https://www.youtube.com/embed/FwuI02edclQ]


## <a name="join-the-anomaly-detector-community"></a>加入“异常检测器”社区

- 加入 [Microsoft Teams 中的异常检测器顾问组](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>后续步骤

- [教程](./tutorials/learn-multivariate-anomaly-detection.md)：本文是一个介绍如何使用多变量 API 的端到端教程。
- [快速入门](./quickstarts/client-libraries-multivariate.md)。
- [最佳做法](./concepts/best-practices-multivariate.md)：本文介绍与多变量 API 一起使用的建议模式。
