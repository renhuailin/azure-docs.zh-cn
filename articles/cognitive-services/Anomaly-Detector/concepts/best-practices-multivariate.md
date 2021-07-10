---
title: 使用异常检测器多变量 API 的最佳做法
titleSuffix: Azure Cognitive Services
description: 使用异常检测器多变量 API 将异常检测应用于时序数据的最佳做法。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 异常检测, 机器学习, 算法
ms.openlocfilehash: 30778cf48efda57fc0d50964611d5616ce7a84d5
ms.sourcegitcommit: 17345cc21e7b14e3e31cbf920f191875bf3c5914
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 05/19/2021
ms.locfileid: "110062247"
---
# <a name="multivariate-time-series-anomaly-detector-best-practices"></a>多变量时序异常检测器最佳做法

本文将提供有关使用多变量异常检测器 API 时要遵循的建议做法的指导。

## <a name="training-data"></a>训练数据 

### <a name="data-schema"></a>数据架构
若要使用异常检测器多变量 API，需要先训练你自己的模型。 训练数据是一组满足以下要求的多个时间序列：

每个时间序列都应是一个 CSV 文件，该文件有 2 列（且仅有 2 列），“timestamp”和“value”（全部小写）作为标题行 。 “timestamp”值应符合 ISO 8601 标准；“value”可为整数，也可为带有任意小数位的小数。 例如：

|timestamp | 值|
|-------|-------|
|2019-04-01T00:00:00Z| 5|
|2019-04-01T00:01:00Z| 3.6|
|2019-04-01T00:02:00Z| 4|
|`...`| `...` |

每个 CSV 文件应根据要用于模型训练的不同变量进行命名。 例如，“temperature.csv”和“humidity.csv”。 所有 CSV 文件都应压缩到一个没有任何子文件夹的 zip 文件中。 zip 文件可采用任何所需名称。 zip 文件应上传到 Azure Blob 存储。 为 zip 文件生成 [Blob SAS（共享访问签名）URL](../../../storage/common/storage-sas-overview.md) 后，可使用该 URL 进行训练。 请查看此文档，了解如何从 Azure Blob 存储生成 SAS URL。

### <a name="data-quality"></a>数据质量
- 当模型从历史数据中学习正常模式时，训练数据应表示系统的整体正常状态。 如果训练数据中的异常过多，模型会很难学习这些类型的模式。 
-  模型还具有数百万个参数，它需要最少数量的数据点来学习一组最优参数。 一般来说，你需要为每个变量提供至少 15,000 个数据点，才能正确地训练模型。 数据越多，模型效果越好。
- 一般而言，训练数据的缺失值比率应低于 20%。 缺少数据过多可能会导致自动填充值（通常是直线段或常量值）被视为正常模式并被模型学习。 这可能会导致将实际数据点检测为异常。 

    然而，在某些情况下，高比率是可以接受的。 例如，如果一个组中有两个时间序列，则使用 `Outer` 模式来对齐时间戳。 一种具分钟粒度，另一种具有小时粒度。 这样每小时时序本质上至少有 59 / 60 = 98.33% 的缺失数据点。 在这种情况下，如果唯一可用的值不会有太大波动，通常可以使用该值填充时序。

## <a name="parameters"></a>parameters

### <a name="sliding-window"></a>滑动窗口

多变量异常检测将一段长度为 `slidingWindow` 的数据点作为输入，并决定下一个数据点是否为异常。 样本的长度值越大，为决策考虑的数据就越多。 为 `slidingWindow` 选择适当的值时，应牢记两点：输入数据的属性以及训练/推理时间与潜在性能改进之间的权衡。 `slidingWindow` 由介于 28 到 2880 之间的一个整数组成。 你可以根据数据是否为周期性的，以及数据的采样速率来决定使用多少个数据点作为输入。

如果数据是周期性的，你可以包括 1-3 个周期作为输入，并且在以高频率（小粒度）采样数据（如分钟级别或秒级别数据时，可以选择更多数据作为输入。 另一个问题是，较长的输入可能会导致较长的训练/推理时间，并且不能保证更多的输入点会带来性能的提升。 而如果数据点太少，则可能会使模型难以收敛到最优解决方案。 例如，如果输入数据仅有两个点，则很难检测异常。

### <a name="align-mode"></a>对齐模式

参数 `alignMode` 用于指示如何基于时间戳对齐多个时序。 这是因为许多时序都缺少值，在进一步处理前，我们需要将它们基于相同的时间戳对齐。 此参数有两个选项：`inner join` 和 `outer join`。 `inner join` 意味着，我们将基于每个时序都具有一个值的时间戳报告检测结果，而 `outer join` 意味着，我们将基于具有值的任何时序的时间戳报告检测结果。  `alignMode` 还会影响模型的输入序列，因此，请为你的方案选择一个适合的 `alignMode`，因为结果可能会明显不同。

这里我们展示一个示例来解释不同的 `alignModel` 值。

#### <a name="series1"></a>Series1

|timestamp | 值|
----------| -----|
|`2020-11-01`| 1  
|`2020-11-02`| 2  
|`2020-11-04`| 4  
|`2020-11-05`| 5

#### <a name="series2"></a>Series2

timestamp | 值  
--------- | -
`2020-11-01`| 1  
`2020-11-02`| 2  
`2020-11-03`| 3  
`2020-11-04`| 4

#### <a name="inner-join-two-series"></a>内部联接两个序列
  
timestamp | Series1 | Series2
----------| - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-04`| 4 | 4

#### <a name="outer-join-two-series"></a>外部联接两个序列

timestamp | series1 | series2
--------- | - | -
`2020-11-01`| 1 | 1
`2020-11-02`| 2 | 2
`2020-11-03`| NA | 3
`2020-11-04`| 4 | 4
`2020-11-05`| 5 | NA

### <a name="fill-not-available-na"></a>填充不可用 (NA)

通过外部联接将变量按时间戳对齐后，某些变量中可能会有一些 `Not Available` (`NA`) 值。 可以指定方法来填充此 NA 值。 `fillNAMethod` 的选项为 `Linear`、`Previous`、`Subsequent`、`Zero` 和 `Fixed`。

| 选项     | 方法                                                                                           |
| ---------- | -------------------------------------------------------------------------------------------------|
| 线性     | 通过线性内插填充 NA 值                                                           |
| 上一篇   | 传播最后一个有效值以填充空白。 示例：`[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
| 后续 | 使用下一个有效值填充空白。 示例：`[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
| 零个       | 用 0 填充 NA 值。                                                                           |
| 固定      | 使用应在 `paddingValue` 中提供的指定有效值填充 NA 值。          |

## <a name="model-analysis"></a>模型分析

### <a name="training-latency"></a>训练延迟

多变量异常检测训练可能非常耗时。 当你有大量用于训练的时间戳时，这一点更加突出。 因此，我们允许异步执行部分训练过程。 通常，用户通过训练模型 API 提交训练任务。 然后，通过 `Get Multivariate Model API` 获取模型状态。 这里我们演示如何提取训练完成前的剩余时间。 在“获取多变量模型 API”响应中，有一个名为 `diagnosticsInfo` 的项。 在此项中，有一个 `modelState` 元素。 要计算剩余时间，我们需要使用 `epochIds` 和 `latenciesInSeconds`。 一个时期表示训练数据的一个完整周期。 我们将会每 10 个时期输出一次状态信息。 我们总共将训练 100 个时期，延迟指示一个时期所花费的时间。 利用此信息，我们可以知道训练模型的剩余时间。

### <a name="model-performance"></a>模型性能

多变量异常检测，作为一种无人监督模型。 评估模型性能的最佳方式是手动检查异常结果。 在“获取多变量模型”响应中，我们提供了一些基本信息供我们分析模型性能。 在“获取多变量模型 API”返回的 `modelState` 元素中，我们可以使用 `trainLosses` 和 `validationLosses` 来评估模型是否已按预期进行训练。 在大多数情况下，这两种损失会逐步降低。 `variableStates` 中提供了另一条供我们用来分析模型性能的信息。 变量状态列表按 `filledNARatio` 值降序排序。 值越大表示性能越差，通常我们需要尽可能降低此 `NA ratio` 的值。 在时间戳方面，`NA` 可能是由缺少值或未对齐变量所导致的。

## <a name="next-steps"></a>后续步骤

- [快速入门](../quickstarts/client-libraries-multivariate.md)。
- [了解支持异常检测器多变量的基础算法](https://arxiv.org/abs/2009.02040)
