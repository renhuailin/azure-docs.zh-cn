---
title: 异常检测器多变量 API 故障排除
titleSuffix: Azure Cognitive Services
description: 了解如何在使用异常检测器 API 时修正常见错误代码
services: cognitive-services
author: mrbullwinkle
manager: nitinme
ms.service: cognitive-services
ms.subservice: anomaly-detector
ms.topic: conceptual
ms.date: 04/01/2021
ms.author: mbullwin
keywords: 异常检测, 机器学习, 算法
ms.openlocfilehash: 46b67e3f465bfb481389049e4c45e5c2edbefbfc
ms.sourcegitcommit: 8b7d16fefcf3d024a72119b233733cb3e962d6d9
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 07/16/2021
ms.locfileid: "114292686"
---
# <a name="troubleshooting-the-multivariate-api"></a>多变量 API 故障排除

本文提供有关如何在使用多变量 API 时排查常见错误消息并进行修正的指导。

## <a name="multivariate-error-codes"></a>多变量错误代码

### <a name="common-errors"></a>常见错误

| 错误代码                 | HTTP 错误代码 | 错误消息                                  | 注释                                                      |
| -------------------------- | --------------- | ---------------------------------------------- | ------------------------------------------------------------ |
| `SubscriptionNotInHeaders` | 400             | 标头中未找到 apim-subscription-id | 请将 APIM 订阅 ID 添加到标头中。 示例标头：`{"apim-subscription-id": <Your Subscription ID>}` |
| `FileNotExist`             | 400             | 文件 \<source> 不存在。                  | 请检查 blob 共享访问签名 (SAS) 是否有效。 确保它未过期。 |
| `InvalidBlobURL`           | 400             |                                                | 你的 blob 共享访问签名 (SAS) 不是有效的 SAS。                            |
| `StorageWriteError`        | 403             |                                                | 此错误可能是由权限问题导致的。 服务不允许将数据写入由客户管理的密钥 (CMK) 加密的 blob。 删除 CMK 或再次授予服务访问权限。 有关详细信息，请参阅[此页](../../encryption/cognitive-services-encryption-keys-portal.md)。 |
| `StorageReadError`         | 403             |                                                | 与 `StorageWriteError` 相同。                                 |
| `UnexpectedError`          | 500             |                                                | 请与我们联系并提供详细的错误信息。 你可以采用[本文档](../../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fanomaly-detector%2fcontext%2fcontext)中提供的支持选项，或者发送电子邮件至 [AnomalyDetector@microsoft.com](mailto:AnomalyDetector@microsoft.com)           |


### <a name="train-a-multivariate-anomaly-detection-model"></a>定型多变量异常情况检测模型

| 错误代码               | HTTP 错误代码 | 错误消息                                                | 注释                                                      |
| ------------------------ | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `TooManyModels`          | 400             | 此订阅已达到最大模型数量。  | 每个 APIM 订阅 ID 允许有 300 个活动模型。 请在训练新模型之前删除不使用的模型 |
| `TooManyRunningModels`   | 400             | 此订阅已达到最大运行模型数量。 | 每个 APIM 订阅 ID 允许同时训练 5 个模型。 请在之前的模型完成其训练过程后再训练新模型。 |
| `InvalidJsonFormat`      | 400             | json 格式无效。                                         | 训练请求不是有效的 JSON。                        |
| `InvalidAlignMode`       | 400             | `'alignMode'` 字段必须是这些值之一：`'Inner'` 或 `'Outer'`。 | 请检查 `'alignMode'` 的值，其应是 `'Inner'` 或 `'Outer'`（区分大小写）。 |
| `InvalidFillNAMethod`    | 400             | `'fillNAMethod'` 字段必须是这些值之一：`'Previous'`、`'Subsequent'`、`'Linear'`、`'Zero'`、`'Fixed'`、`'NotFill'`，且当 `'alignMode'` 为 `'Outer'` 时，此值不能为 `'NotFill'`。 | 请检查 `'fillNAMethod'` 的值。 有关详细信息，请参阅[本部分](./best-practices-multivariate.md#optional-parameters-for-training-api)。 |
| `RequiredPaddingValue`   | 400             | 当 `'fillNAMethod'` 为 `'Fixed'` 时，请求中的 `'paddingValue'` 字段为必填字段。 | 当 `'fillNAMethod'` 为 `'Fixed'` 时，需要提供有效的填充值。 有关详细信息，请参阅[本部分](./best-practices-multivariate.md#optional-parameters-for-training-api)。 |
| `RequiredSource`         | 400             | 请求中的 `'source'` 字段为必填字段。             | 训练请求尚未指定 `'source'` 字段的值。 示例：`{"source": <Your Blob SAS>}`。 |
| `RequiredStartTime`      | 400             | 请求中的 `'startTime'` 字段为必填字段。          | 训练请求尚未指定 `'startTime'` 字段的值。 示例：`{"startTime": "2021-01-01T00:00:00Z"}`。 |
| `InvalidTimestampFormat` | 400             | 时间戳格式无效。 `<timestamp>` 不是有效的格式。 | 请求正文中的时间戳格式不正确。 可以尝试 `import pandas as pd; pd.to_datetime(timestamp)` 进行验证。 |
| `RequiredEndTime`        | 400             | 请求中的 `'endTime'` 字段为必填字段。            | 训练请求尚未指定 `'startTime'` 字段的值。 示例：`{"endTime": "2021-01-01T00:00:00Z"}`。 |
| `InvalidSlidingWindow`   | 400             | `'slidingWindow'` 字段必须是介于 28 和 2880 之间的整数。 | `'slidingWindow'` 必须是介于 28 和 2880 之间（含 28 和 2880）的整数。 |

### <a name="get-multivariate-model-with-model-id"></a>获取具有模型 ID 的多变量模型

| 错误代码      | HTTP 错误代码 | 错误消息             | 注释                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
| `ModelNotExist` | 404             | 此模型不存在。 | 具有相应模型 ID 的模型不存在。 请检查请求 URL 中的模型 ID。 |

### <a name="list-multivariate-models"></a>列出多变量模型

| 错误代码      | HTTP 错误代码 | 错误消息             | 注释                                                      |
| --------------- | --------------- | ------------------------- | ------------------------------------------------------------ |
|`InvalidRequestParameterError`| 400             | $skip 或 $top 的值无效... | 请检查这两个参数的值是否是数值。 $skip 和 $top 用于列出包含分页的模型。 由于 API 仅返回 10 个最近更新的模型，因此可以使用 $skip 和 $top 获取先前更新的模型。 | 

### <a name="anomaly-detection-with-a-trained-model"></a>使用已训练模型进行异常情况检测

| 错误代码        | HTTP 错误代码 | 错误消息                                                | 注释                                                      |
| ----------------- | --------------- | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `ModelNotExist`   | 404             | 此模型不存在。                                    | 用于推理的模型不存在。 请检查请求 URL 中的模型 ID。 |
| `ModelFailed`     | 400             | 模型无法训练。                                  | 无法成功训练模型。 请使用模型 ID 获取此模型，并获取详细信息。 |
| `ModelNotReady`   | 400             | 模型尚未就绪。                                  | 模型尚未就绪。 请等待一段时间，直到训练过程完成。 |
| `InvalidFileSize` | 413             | 文件 \<file> 超过文件大小限制（\<size limit> 个字节）。 | 推理数据的大小超出上限（当前为 2GB）。 请使用较少数据进行推理。 |

### <a name="get-detection-results"></a>获取检测结果

| 错误代码       | HTTP 错误代码 | 错误消息              | 注释                                                      |
| ---------------- | --------------- | -------------------------- | ------------------------------------------------------------ |
| `ResultNotExist` | 404             | 结果不存在。 | 每个请求的结果不存在。 推理尚未完成或结果已过期（7 天）。 |

### <a name="data-processing-errors"></a>数据处理错误

以下错误代码没有关联的 HTTP 错误代码。

| 错误代码             | 错误消息                                                | 注释                                                      |
| ---------------------  | ------------------------------------------------------------ | ------------------------------------------------------------ |
| `NoVariablesFound`     | 未找到变量。 请检查文件是否按照说明进行组织。 | 数据源中未找到 csv 文件。 这通常是由错误的文件组织方式导致。 请参阅示例数据了解所需的结构。 |
| `DuplicatedVariables`  | 多个变量具有相同的名称。             | 存在重复的变量名称。                         |
| `FileNotExist`         | 文件 \<filename> 不存在。                              | 此错误通常在推理期间发生。 变量在训练数据中出现，但在推理数据中缺失。 |
| `RedundantFile`        | 文件 \<filename> 是多余的。                                | 此错误通常在推理期间发生。 变量不在训练数据中，但出现在推理数据中。 |
| `FileSizeTooLarge`     | 文件 \<filename> 过大。                    | 单个 csv 文件 \<filename> 超过了限制的大小。 请使用较少数据进行训练。 |
| `ReadingFileError`     | 读取 \<filename> 时出错。 \<error messages>    | 无法读取文件 \<filename>。 可以参阅 \<error messages> 获取详细信息，也可以在本地环境中使用 `pd.read_csv(filename)` 进行验证。 |
| `FileColumnsNotExist`  | 文件 \<filename> 中的列时间戳或值不存在。  | 每个 csv 文件必须包含名为“时间戳”和“值”（区分大小写）的两列数据。  |
| `VariableParseError`   | 变量 \<variable> 分析 \<error message> 出错。             | 由于运行时错误，无法处理 \<variable>。 请参阅 \<error message> 获取详细信息，或者与我们联系并提供 \<error message>。 |
| `MergeDataFailed`      | 无法合并数据。 请检查数据格式。              | 数据合并失败。 这可能是由错误的数据格式、文件组织方式等原因导致。请参阅示例数据了解当前文件结构。 |
| `ColumnNotFound`       | 合并数据中找不到 \<column> 列。          | 合并后列缺失。 请验证数据。     |
| `NumColumnsMismatch`   | 合并数据的列数与变量数不匹配。 | 请验证数据。                                      |
| `TooManyData`          | 数据点太多。 每个变量最大为 1000000 个。       | 请减少输入数据的大小。                        |
| `NoData`               | 无有效数据                                   | 处理后没有要训练/推理的数据。 请检查开始时间和结束时间。 |
| `DataExceedsLimit`     | 时间戳介于 `startTime` 和 `endTime` 之间的数据的长度超出限制 (\<limit>)。 | 处理后的数据大小超出限制。 （目前已处理的数据无大小限制。） |
| `NotEnoughInput`       | 数据不充足。 数据的长度为 \<data length>，但最小长度应大于滑动窗口，即 \<sliding window size>。 | 用于推理的最小数据点数是滑动窗口的大小。 尝试提供更多数据进行推理。 |