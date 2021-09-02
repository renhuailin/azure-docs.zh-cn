---
title: 适用于使用异常检测器多变量 API 的预测性维护体系结构
titleSuffix: Azure Cognitive Services
description: 适用于使用异常检测器多变量 API 的参考体系结构，目的是为时序数据应用异常检测，从而进行预测性维护。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 异常检测, 机器学习, 算法
ms.openlocfilehash: 9de0a774ab2464d3b59ee8875453255e92ea5c45
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113297237"
---
# <a name="predictive-maintenance-solution-with-anomaly-detector-multivariate"></a>具有异常检测器多变量的预测性维护解决方案

许多不同的行业都需要预测性维护解决方案来降低风险，并通过处理其设备的数据来获取可行见解。 预测性维护通过执行在线监视来评估设备的状况。 目标是在设备降级或中断之前执行维护。

监视设备的运行状况可能会很困难，因为设备内部的每个组件都可以生成数十个信号，例如振动、方向和旋转。  当这些信号具有隐式关系且需要进行监视和分析时，情况可能更复杂。 为这些信号定义不同的规则并手动将其相互关联起来可能很费时。 利用异常检测器的多变量功能可以：

* 同时监视多个关联信号，并在模型中考虑它们之间的相互关系。
* 在捕获的每个异常中，不同信号的贡献排名可帮助解释异常和分析事件的根本原因。
* 多变量异常检测模型以无人监督方式构建。 你可以专门针对不同类型的设备训练模型。

下面，我们将根据异常检测器多变量为预测性维护解决方案提供参考体系结构。

## <a name="reference-architecture"></a>参考体系结构

[ ![从使用一台工业设备在边缘收集传感器数据开始，跟踪处理/分析管道，到异常检测器运行后生成事件警报的结束输出的体系结构关系图。](../media/multivariate-architecture/multivariate-architecture.png) ](../media/multivariate-architecture/multivariate-architecture.png#lightbox)

在上面的体系结构中，来自传感器数据的流事件将存储在 Azure Data Lake 中，然后由数据转换模块进行处理，以转换为时序格式。 同时，流事件将通过已训练的模型触发实时检测。 通常有一个管理多变量模型生命周期的模块，如以上体系结构中的“桥接服务”。

**模型训练**：在使用异常检测器多变量检测组件或设备的异常之前。 我们需要根据此实体生成的特定信号（时序）来训练模型。 *桥接服务* 将获取历史数据，并将训练作业提交到异常检测器，然后将模型 ID 保存在“模型元数据”存储中。

**模型验证**：根据训练数据量，特定模型的训练时间可能有所不同。 “桥接服务”可以定期查询模型状态和诊断信息。 验证模型质量后才能将其联机。 如果场景中有标签，则可以使用这些标签来验证模型质量。 否则，可使用诊断信息来评估模型质量，还可以使用训练模型对历史数据执行检测，并通过评估结果来回溯测试模型的有效性。

**模型推理**：对有效的模型执行在线检测，并将结果 ID 存储在“推理表”中。 训练过程和推理过程均以异步方式完成。 通常，检测任务可以在几秒内完成。 用于检测的信号应与用于训练的信号相同。 例如，如果我们使用振动、方向和旋转进行训练，则在检测中应将这三个信号作为输入包含在内。

**事件警报**：可通过结果 ID 查询检测结果。 每个结果都包含每个异常的严重性和贡献排名。 贡献排名可用于了解发生此异常的原因以及导致此事件的信号。 你可以为严重性设置不同的阈值，从而生成要发送给现场工程师来执行维护工作的警报和通知。

## <a name="next-steps"></a>后续步骤

- [快速入门](../quickstarts/client-libraries-multivariate.md)。
- [最佳做法](../concepts/best-practices-multivariate.md)：本文介绍建议与多变量 API 一起使用的模式。