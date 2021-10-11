---
title: 迁移到 Read v3.x 容器
titleSuffix: Azure Cognitive Services
description: 了解如何迁移到 v3 Read OCR 容器
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: overview
ms.date: 09/28/2021
ms.author: aahi
ms.openlocfilehash: 5998ccdfa48a700ac8789954246f6b7bcec60543
ms.sourcegitcommit: 87de14fe9fdee75ea64f30ebb516cf7edad0cf87
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 10/01/2021
ms.locfileid: "129360505"
---
# <a name="migrate-to-the-read-v3x-ocr-containers"></a>迁移到 Read v3.x OCR 容器

如果正在使用计算机视觉 Read OCR 容器的版本 2，请通过本文来了解如何将应用程序升级为使用该容器的版本 3.x。 


## <a name="configuration-changes"></a>配置更改

* `ReadEngineConfig:ResultExpirationPeriod` 不再受支持。 Read OCR 容器具有一个内置 Cron 作业，该作业会在 48 小时后删除与请求关联的结果和元数据。
* `Cache:Redis:Configuration` 不再受支持。 v3.x 容器中未使用缓存，因此无需进行设置。

## <a name="api-changes"></a>API 更改

Read v3.2 容器使用计算机视觉 API 版本 3，并具有以下终结点：

* `/vision/v3.2-preview.1/read/analyzeResults/{operationId}`
* `/vision/v3.2-preview.1/read/analyze`
* `/vision/v3.2-preview.1/read/syncAnalyze`

有关更新应用程序以使用基于云的 Read API 版本 3 的详细信息，请参阅[计算机视觉 v3 REST API 迁移指南](./upgrade-api-versions.md)。 此信息也适用于容器。 同步操作仅在容器中受支持。

## <a name="memory-requirements"></a>内存需求

这些要求和建议基于这样的基准：每秒一个请求，使用包含 29 行和总共 803 个字符的经过扫描的业务信函的 8 MB。 下表显示了每个 Read OCR 容器的最小和建议资源分配。

|容器  |最小值 | 建议  |
|---------|---------|------|
|Read 3.2-preview | 8 个内核，16 GB 内存         | 8 个内核，24 GB 内存 |

每个核心必须至少为 2.6 千兆赫 (GHz) 或更快。

内核和内存对应于 `--cpus` 和 `--memory` 设置，这些设置用作 docker run 命令的一部分。

## <a name="storage-implementations"></a>存储实现

>[!NOTE]
> 容器版本 3.x 不再支持 MongoDB， 而支持 Azure 存储和脱机文件系统。

| 实现 |    所需的运行时参数 |
|---------|---------|
|文件级别（默认值）    | 无需任何运行时参数。 将使用 `/share` 目录。 |
|Azure Blob    | `Storage:ObjectStore:AzureBlob:ConnectionString={AzureStorageConnectionString}` |

## <a name="queue-implementations"></a>队列实现

在容器 v3.x 中，目前不支持 RabbitMQ。 支持的后备实现包括：

| 实现 | 运行时参数 | 预期用途 |
|---------|---------|-------|
| 内存中（默认值） | 无需任何运行时参数。 | 开发和测试 |
| Azure 队列 | `Queue:Azure:ConnectionString={AzureStorageConnectionString}` | 生产 |
| RabbitMQ    | 不可用 | 生产 |

为了提高冗余性，Read v3.x 容器使用可见性计时器来确保在采用多容器设置运行时，即使发生崩溃也可成功处理请求。 

使用 `Queue:Azure:QueueVisibilityTimeoutInMilliseconds` 设置计时器，以设置另一个工作线程处理消息时，该消息不可见的时长。 为了避免重复处理页面，建议将超时期限设置为 120 秒。 默认值为 30 秒。

| 默认值 | 建议的值 |
|---------|---------|
| 30000 |    120000 |


## <a name="next-steps"></a>后续步骤

* 查看[配置容器](computer-vision-resource-container-config.md)了解配置设置
* 查看 [OCR 概述](overview-ocr.md)，了解有关识别印刷文本和手写文本的详细信息
* 如需详细了解该容器支持的方法，请参阅[读取 API](//westus.dev.cognitive.microsoft.com/docs/services/5adf991815e1060e6355ad44/operations/56f91f2e778daf14a499e1fa)。
* 参阅[常见问题解答 (FAQ)](FAQ.yml)，以解决与计算机视觉功能相关的问题。
* 使用更多[认知服务容器](../cognitive-services-container-support.md)