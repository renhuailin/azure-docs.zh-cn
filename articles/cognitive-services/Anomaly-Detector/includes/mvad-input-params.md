---
title: MVAD 输入参数
titleSuffix: Azure Cognitive Services
services: cognitive-services
author: quying
manager: tonyxin
ms.service: cognitive-services
ms.topic: include
ms.date: 7/1/2021
ms.author: yingqunpku
ms.openlocfilehash: 5b4cea9dea4bb8ee6d1f32ec0e21fd87eb55da95
ms.sourcegitcommit: 6bd31ec35ac44d79debfe98a3ef32fb3522e3934
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/02/2021
ms.locfileid: "113215213"
---
### <a name="input-parameters"></a>输入参数

#### <a name="required-parameters"></a>必需的参数

在训练和推理 API 请求中，需要以下三个参数：

* `source` - zip 文件的链接，位于具有共享访问签名 (SAS) 的 Azure Blob 存储中。
* `startTime` - 用于训练或推理的数据的开始时间。 如果它早于数据中的实际最早时间戳，那么实际最早时间戳将作为起点。
* `endTime` - 用于训练或推理的数据的结束时间，必须晚于或等于 `startTime`。 如果 `endTime` 晚于数据中的实际最晚时间戳，那么实际最晚时间戳将作为终点。 如果 `endTime` 等于 `startTime`，则表示推断出一个单一的数据点，这在流式处理场景中经常使用。

#### <a name="optional-parameters-for-training-api"></a>训练 API 的可选参数

训练 API 的其他参数是可选的：

* `slidingWindow` - 有多少个数据点用于确定异常情况。 这是一个介于 28 和 2880 之间的整数。 默认值为 300。 如果在模型训练过程中 `slidingWindow` 为 `k`，那么在推理过程中至少可以从源文件访问 `k` 个点来获取有效结果。

    MVAD 采用一段数据点来决定下一个数据点是否异常。 段的长度是 `slidingWindow`。
    在选择 `slidingWindow` 值时，有两点需要注意：
    1. 数据的属性：它是否是周期性的，以及它的采样率。 如果数据是周期性的，可以设置 1 - 3 个周期的长度作为 `slidingWindow`。 如果数据的频率较高（小粒度），如分钟级别或秒级别，可以设置一个相对较高的 `slidingWindow` 值。
    1. 训练/推理时间与潜在性能影响之间的权衡。 较大的 `slidingWindow` 可能会导致较长的训练/推断时间。 不能保证较大的 `slidingWindow` 会提升正确率。 较小的 `slidingWindow` 可能会导致模型难以收敛到最优解。 例如，如果 `slidingWindow` 只有两个点，则很难检测到异常。

* `alignMode` - 如何在时间戳上对齐多个变量（时序）。 此参数有两个选项：`Inner` 和 `Outer`，默认值是 `Outer`。

    当变量的时间戳序列之间存在不一致时，这个参数至关重要。 在进一步处理之前，模型需要将变量调整到相同的时间戳序列上。

    `Inner` 表示模型将仅对每个变量都具有值（即所有变量的交集）的时间戳报告结果。 `Outer` 表示模型将对任何变量都具有值（即所有变量的并集）的时间戳报告结果。

    下面的示例可以解释不同的 `alignModel` 值。

    Variable-1

    |timestamp | 值|
    ----------| -----|
    |2020-11-01| 1  
    |2020-11-02| 2  
    |2020-11-04| 4  
    |2020-11-05| 5

    Variable-2

    timestamp | 值  
    --------- | -
    2020-11-01| 1  
    2020-11-02| 2  
    2020-11-03| 3  
    2020-11-04| 4

    `Inner` 加入两个变量

    timestamp | Variable-1 | Variable-2
    ----------| - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-04| 4 | 4

    `Outer` 加入两个变量

    timestamp | Variable-1 | Variable-2
    --------- | - | -
    2020-11-01| 1 | 1
    2020-11-02| 2 | 2
    2020-11-03| `nan` | 3
    2020-11-04| 4 | 4
    2020-11-05| 5 | `nan`

* `fillNAMethod` - 如何在合并的表中填充 `nan`。 合并的表中可能缺少一些值，应正确处理这些值。 我们提供了几种方法来填充这些值。 选项包括 `Linear`、`Previous`、`Subsequent``Zero` 和 `Fixed`，默认值是 `Linear`。

    | 选项     | 方法                                                                                           |
    | ---------- | -------------------------------------------------------------------------------------------------|
    | `Linear`     | 通过线性内插填充 `nan` 值                                                           |
    | `Previous`   | 传播最后一个有效值以填充空白。 示例：`[1, 2, nan, 3, nan, 4]` -> `[1, 2, 2, 3, 3, 4]` |
    | `Subsequent` | 使用下一个有效值填充空白。 示例：`[1, 2, nan, 3, nan, 4]` -> `[1, 2, 3, 3, 4, 4]`       |
    | `Zero`       | 用 0 填充 `nan` 值。                                                                           |
    | `Fixed`      | 使用应在 `paddingValue` 中提供的指定有效值填充 `nan` 值。          |

* `paddingValue` - 当 `fillNAMethod` 为 `Fixed` 时，使用填充值来填充 `nan`，在这种情况下必须提供。 在其他情况下，它是可选的。

* `displayName` - 这是一个可选参数，用于标识模型。 例如，可以使用它来标记参数、数据源以及有关模型及其输入数据的其他任何元数据。 默认值为空字符串。
