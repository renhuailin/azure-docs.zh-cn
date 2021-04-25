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
ms.openlocfilehash: 7894f2a2e588a41dcaf09f7c1e3e338f892f7d8a
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 04/13/2021
ms.locfileid: "107318802"
---
# <a name="troubleshooting-the-multivariate-api"></a>多变量 API 故障排除

本文提供有关如何在使用多变量 API 时排查常见 HTTP 错误消息并进行修正的指导。

### <a name="multivariate-error-codes"></a>多变量错误代码

| 方法                                       | HTTP 错误代码 | 错误消息                                                                                                            | 采取的操作                                                                                                                                                                                                         |
|----------------------------------------------|-----------------|--------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| 定型多变量异常情况检测模型 | 400             | `The 'source' field is required in the request.`                                                                         | 关键字“source”未正确指定。 格式应为 `"{\"source\": \|" <SAS   URL>\"}"`                                                                                          |
| 定型多变量异常情况检测模型 | 400             | `The source field must be a valid sas blob url`                                                                          | 源字段必须是有效的 blob 容器 sas url。                                                                                                                                                             |
| 定型多变量异常情况检测模型 | 400             | `The 'startTime' field is required in the request.`                                                                      | 在请求中添加 startTime                                                                                                                                                                                         |
| 定型多变量异常情况检测模型 | 400             | `The 'endTime' field is required in the request.`                                                                        | 在请求中添加 endTime。                                                                                                                                                                                          |
| 定型多变量异常情况检测模型 | 400             | `Invalid Timestamp format.`                                                                                              | 源 url 中压缩的 csv 文件的时间戳格式无效，或者“startTime”和“endTime”格式无效。                                                                                 |
| 定型多变量异常情况检测模型 | 400             | `The displayName length exceeds maximum allowed length 24.`                                                              | DisplayName 是用户用于区分不同模型的可选参数。 有效的 displayName 必须小于 24 个字符。                                                                    |
| 定型多变量异常情况检测模型 | 400             | `The 'slidingWindow' field must be an integer between 28 and 2880.`                                                      | 滑动窗口必须处于有效范围内。                                                                                                                                                                             |
| 定型多变量异常情况检测模型 | 401             | `Unable to download blobs on the Azure Blob storage account.`                                                            | URL 没有正确的权限。 未设置列表标志。   客户应重新创建 SAS URL，并确保勾选读取和列表标志（例如使用存储资源管理器） |
| 定型多变量异常情况检测模型 | 413             | `Unable to process the dataset. Number of variables exceed the limit (300).`                                             | Blob 容器中的数据超出了当前的 300 个变量限制。 客户必须指向以减少变量大小。                                                                                |
| 定型多变量异常情况检测模型 | 413             | `Valid Timestamps in the dataset exceeds the limit (1 million points), please change   startTime or endTime parameters.` | 可用于定型的最大点数为 1 百万。 客户可减少变量大小或更改 startTime 或 endTime                                                                                     |
| 定型多变量异常情况检测模型 | 413             | `Unable to process dataset. Size of dataset exceeds size limit (2GB).`                                                   | Blob 容器中的数据超出了当前的 4 MB 限制。 客户必须指向数据量更低的 blob。                                                                                          |
| 检测多变量异常情况                  | 404             | `The model does not exist.`                                                                                              | 模型 ID 无效。 客户需要在使用模型之前对其定型。                                                                                                                                            |
| 检测多变量异常情况                  | 400             | `The model is not ready yet.`                                                                                            | 模型尚未就绪。 客户需要调用 Get 多变量模型 API 才能检查模型状态。                                                                                                               |
| 检测多变量异常情况                  | 400             | `The 'source' field is required in the request.`                                                                         | 关键字“source”未正确指定。 格式应为 `"{\"source\": \|" <SAS   URL>\"}"`                                                                                          |
| 检测多变量异常情况                  | 400             | `The source field must be a valid sas blob url`                                                                          | 源字段必须是有效的 blob 容器 sas url。                                                                                                                                                             |
| 检测多变量异常情况                  | 400             | `The 'startTime' field is required in the request.`                                                                      | 在请求中添加 startTime                                                                                                                                                                                         |
| 检测多变量异常情况                  | 400             | `The 'endTime' field is required in the request.`                                                                        | 在请求中添加 endTime。                                                                                                                                                                                          |
| 检测多变量异常情况                  | 400             | `Invalid Timestamp format.`                                                                                              | 源 url 中压缩的 csv 文件的时间戳格式无效，或者“startTime”和“endTime”格式无效。                                                                                 |
| 检测多变量异常情况                  | 400             | `The corresponding file of the variable does not exist.`                                                                 | 已在定型中使用某个变量，但是当客户使用相应的模型进行检测时，找不到该变量。 客户需要添加此变量，然后提交检测请求。         |
| 检测多变量异常情况                  | 413             | `Unable to process the dataset. Number of variables exceed the limit (300).`                                             | Blob 容器中的数据超出了当前的 300 个变量限制。 客户必须指向以减少变量大小。                                                                                |
| 检测多变量异常情况                  | 413             | `The limit timestamps of one detection request is 2880, please change startTime or endTime parameters.`                | 在一个检测请求中检测到的最大时间戳为 2880，客户需要更改 startTime 或 endTime，然后提交检测请求。                                                           |
| 检测多变量异常情况                  | 413             | `Unable to process dataset. Size of dataset exceeds size limit (2GB).`                                                   | Blob 容器中的数据超出当前的 4 MB 限制。 客户必须指向数据量更低的 blob。                                                                                          |
| 获取多变量模型                       | 404             | `Model with 'id=<input model ID>' not found.`                                                                            | ID 不是有效的模型 ID。 使用 GET 模型查找所有有效的模型 ID。                                                                                                                               |
| 获取多变量模型                       | 404             | `Model with id=<input model ID>' not found.`                                                                            | ID 不是有效的模型 ID。 使用 GET 模型查找所有有效的模型 ID。                                                                                                                               |
| 获取多变量异常情况检测结果    | 404             | `Result   with 'id=<input result ID>' not found.`                                                                          | ID 不是有效的结果 ID。 重新提交检测请求。                                                                                                                                   |
| 删除多变量模型                    | 404             | `Location   for model with 'id=<input model ID>' not found.`                                                               | ID 不是有效的模型 ID。 使用 GET 模型查找所有有效的模型 ID。                                                                                                                               |