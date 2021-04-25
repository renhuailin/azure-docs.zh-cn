---
title: 什么是异常检测器多变量 API？
titleSuffix: Azure Cognitive Services
description: 新异常检测器公共预览版多变量 API 的概述。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 异常检测, 机器学习, 算法
ms.openlocfilehash: 63ad63cd57dce5f503e317e8c6330dca0325ba05
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318799"
---
# <a name="multivariate-time-series-anomaly-detection-public-preview"></a>多变量时序异常检测（公共预览版）

利用首次发布的 Azure 认知服务异常检测器，你可以使用简单易用的[单变量时序检测器 API](overview.md) 构建监视解决方案的指标。 通过允许单独分析时序，异常检测器单变量提供了简易性和可扩展性。

利用新的 **多变量异常情况检测** API，开发人员能够轻松集成高级 AI，从而检测指标组中的异常，并且无需机器学习知识或带标记的数据。 现在，我们会将多达 300 个不同信号之间的依赖关系和相互关联自动视为关键因素。 这一新功能可帮助你主动防止复杂系统发生故障，例如，软件应用程序、服务器、工厂计算机、航天器，或者甚至是业务。

假设自动引擎中有 20 个传感器，可生成 20 个不同的信号，例如，振动、温度、燃料压力等。这些信号的单独读数可能无法全面反应系统级别的问题，但加以综合则可以反应引擎的运行状况。 当这些信号的交互作用偏离出正常范围时，多变量异常检测功能就可以像经验丰富的专家一样理解异常情况。 底层 AI 模型使用你的数据进行训练和自定义，从而了解业务的独特需求。 现在，利用异常检测器中的新 API，开发人员可以轻松地将多变量时序异常检测功能集成到预测性维护解决方案以及复杂的企业软件或商业智能工具的 AIOps 监视解决方案中。

## <a name="when-to-use-multivariate-versus-univariate"></a>何时使用 **多变量** 与 **单变量**

如果目标是基于历史数据，在每个单独的时序上检测不符合正常模式的异常情况，请使用单变量异常检测 API。 示例：你想要仅根据收入数据来检测每日收入异常，或者想要仅根据 CPU 数据来检测 CPU 峰值。
- `POST /anomalydetector/v1.0/timeseries/last/detect`
- `POST /anomalydetector/v1.0/timeseries/batch/detect`
- `POST /anomalydetector/v1.0/timeseries/changepoint/detect`

![以蓝色曲线表示的单个变量波动值的时序线图，其中的异常值以橙色圆圈标识](./media/anomaly_detection2.png)

如果你的目标是从一组时序数据中检测到系统级别的异常，请使用下面的多变量异常检测 API。 特别是，当任何单个时序无法全面反应问题时，则必须从整体上查看所有信号（一组时序），从而确定系统级别的问题。 示例：你有一项昂贵的实物资产，例如，飞机、石油钻塔上的设备或卫星。 其中每项资产都有数十个或数百个不同类型的传感器。 那么，你必须查看传感器产生的所有时序信号才能确定是否存在系统级别的问题。

- `POST /anomalydetector/v1.1-preview/multivariate/models`
- `GET /anomalydetector/v1.1-preview/multivariate/models[?$skip][&$top]`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `POST/anomalydetector/v1.1-preview/multivariate/models/{modelId}/detect`
- `GET /anomalydetector/v1.1-preview/multivariate/results/{resultId}`
- `DELETE /anomalydetector/v1.1-preview/multivariate/models/{modelId}`
- `GET /anomalydetector/v1.1-preview/multivariate/models/{modelId}/export`

![以下变量的多个时序线图：振动、温度、压力、速度、旋转速度，其中的异常部分以橙色突出显示](./media/multivariate-graph.png)

## <a name="region-support"></a>区域支持

目前，异常检测器多变量的公共预览版在三个区域提供：美国西部 2 区、美国东部 2 区和西欧。

## <a name="algorithms"></a>算法

- [通过图注意力网络执行多变量时序异常情况检测](https://arxiv.org/abs/2009.02040)

## <a name="join-the-anomaly-detector-community"></a>加入“异常检测器”社区

- 加入 [Microsoft Teams 中的异常检测器顾问组](https://aka.ms/AdAdvisorsJoin)

## <a name="next-steps"></a>后续步骤

- [快速入门](./quickstarts/client-libraries-multivariate.md)。
- [最佳做法](./concepts/best-practices-multivariate.md)：本文介绍建议与多变量 API 一起使用的模式。
