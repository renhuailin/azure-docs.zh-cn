---
title: 如何对时序数据使用异常检测器 API
titleSuffix: Azure Cognitive Services
description: 了解如何以批或流数据的形式检测数据中的异常。
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 10/01/2019
ms.author: mbullwin
ms.openlocfilehash: ae8759bd10096737b400fe672c3555ff5fd41585
ms.sourcegitcommit: 6ea4d4d1cfc913aef3927bef9e10b8443450e663
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/05/2021
ms.locfileid: "113296624"
---
# <a name="how-to-use-the-anomaly-detector-univariate-api-on-your-time-series-data"></a>如何：对时序数据使用异常检测器单变量 API  

[异常检测器 API](https://westus2.dev.cognitive.microsoft.com/docs/services/AnomalyDetector/operations/post-timeseries-entire-detect) 提供两种异常情况检测方法。 可以在整个时序中以批处理方式检测异常，也可以在生成数据时通过检测最新数据点的异常状态来检测异常。 检测模型将返回异常结果以及每个数据点的预期值，还会返回异常情况检测边界的上限和下限。 可使用这些值来直观显示数据中正常值的范围和异常。

## <a name="anomaly-detection-modes"></a>异常情况检测模式

异常检测器 API 提供两种检测模式：批处理和流式处理。

> [!NOTE]
> 以下请求 URL 必须与订阅的相应终结点结合。 例如：`https://<your-custom-subdomain>.api.cognitive.microsoft.com/anomalydetector/v1.0/timeseries/entire/detect`


### <a name="batch-detection"></a>批量检测

若要检测给定的时间范围内一批数据点的异常情况，请将以下请求 URI 用于时序数据： 

`/timeseries/entire/detect`. 

通过立刻发送时序数据，API 将使用整个序列生成一个模型，并用它分析每个数据点。  

### <a name="streaming-detection"></a>流式处理检测

若要持续检测流数据的异常情况，请将以下请求 URI 用于最新的数据点： 

`/timeseries/last/detect`. 

通过在生成新数据点的同时发送这些数据点，可以实时监视数据。 将使用发送的数据点生成一个模型，并且 API 将确定时序中的最新点是否异常。

## <a name="adjusting-lower-and-upper-anomaly-detection-boundaries"></a>调整异常情况检测的上下边界

默认情况下，使用 `expectedValue`、`upperMargin` 和 `lowerMargin` 计算异常情况检测的上下边界。 如果需要不同的边界，建议对 `upperMargin` 或 `lowerMargin` 应用 `marginScale`。 边界的计算公式如下：

|边界  |计算  |
|---------|---------|
|`upperBoundary` | `expectedValue + (100 - marginScale) * upperMargin`        |
|`lowerBoundary` | `expectedValue - (100 - marginScale) * lowerMargin`        |

以下示例显示了不同敏感度的异常检测器 API 结果。

### <a name="example-with-sensitivity-at-99"></a>敏感度为 99 的示例

![默认敏感度](../media/sensitivity_99.png)

### <a name="example-with-sensitivity-at-95"></a>敏感度为 95 的示例

![敏感度 99](../media/sensitivity_95.png)

### <a name="example-with-sensitivity-at-85"></a>敏感度为 85 的示例

![敏感度 85](../media/sensitivity_85.png)

## <a name="next-steps"></a>后续步骤

* [什么是异常检测器 API？](../overview.md)
* [快速入门：使用异常检测器检测时序数据中的异常](../quickstarts/client-libraries.md)
