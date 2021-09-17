---
title: 文本分析 API 的数据限制
titleSuffix: Azure Cognitive Services
description: Azure 认知服务中的文本分析 API 的数据限制。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 04/07/2021
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: b583caa4fdb2a1e72833d4e24c317282be041513
ms.sourcegitcommit: d11ff5114d1ff43cc3e763b8f8e189eb0bb411f1
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/25/2021
ms.locfileid: "122822128"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>文本分析 API 的数据和速率限制
<a name="data-limits"></a>

使用本文查找将数据发送到文本分析 API 的大小和速率限制。

## <a name="data-limits"></a>数据限制

> [!NOTE]
> * 定价不受数据或速率限制影响。 定价基于发送到 API 的文本记录的数量，并受文本分析资源的[定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)限制。
>   * 文本记录测量为 1000 个字符。 
> * 数据和速率限制基于发送到 API 的文档数。 如果需要分析的文档的大小超出限制，则可将文本拆分成较小的文本块，然后再将其发送到 API。 
>   * 文档是由文本字符构成的单个字符串。  

| 限制 | 值 |
|------------------------|---------------|
| 单个文档的最大大小 | 5,120 个字符，通过 [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) 进行度量。 也适用于运行状况文本分析。 |
| 单个文档的最大大小（`/analyze` 终结点）  | 12.5 万个字符，通过 [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements) 进行度量。 不适用于运行状况文本分析。 |
| 整个请求的最大大小 | 1MB。 也适用于运行状况文本分析。 |


如果文档超出了字符限制，则 API 的行为会有所不同，具体取决于所使用的终结点：

* `/analyze` 终结点：
  * 如果请求中有任何文档超出最大大小，则 API 将拒绝整个请求并返回 `400 bad request` 错误。
* 所有其他终结点：  
  * API 不会处理超过最大大小的文档，并将为其返回无效的文档错误。 如果 API 请求包含多个文档，则 API 将继续处理它们（只要它们在字符限制内）。

可以在单个请求中发送的最大文档数将取决于所使用的 API 版本和功能，如下表中所述。

#### <a name="version-3"></a>[第 3 版](#tab/version-3)

以下限制针对当前 v3 API。 超过以下限制将生成 HTTP 400 错误代码。


| Feature | 每个请求的最大文档数 | 
|----------|-----------|
| 语言检测 | 1000 |
| 情绪分析 | 10 |
| 观点挖掘 | 10 |
| 关键短语提取 | 10 |
| 命名实体识别 | 5 |
| 实体链接 | 5 |
| 运行状况文本分析  | 对于基于 Web 的 API 为 10，对于容器为 1000。 |
| 分析终结点 | 对于所有操作为 25。 |

#### <a name="version-2"></a>[第 2 版](#tab/version-2)

| Feature | 每个请求的最大文档数 | 
|----------|-----------|
| 语言检测 | 1000 |
| 情绪分析 | 1000 |
| 关键短语提取 | 1000 |
| 命名实体识别 | 1000 |
| 实体链接 | 1000 |

---

## <a name="rate-limits"></a>速率限制

速率限制将因[定价层](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)而异。 两个 API 版本的这些限制是相同的。 这些速率限制不适用于没有设置速率限制的运行状况文本分析容器。

| 层          | 每秒请求数 | 每分钟请求数 |
|---------------|---------------------|---------------------|
| S/多服务 | 1000                | 1000                |
| S0/F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

对每个文本分析功能的请求速率分别进行测量。 可以同时向每个功能发送定价层的最大数量的请求。 例如，如果你处于 `S` 层并一次发送了 1000 个请求，则你将无法在 59 秒内再发送其他请求。


S0-S4 层已弃用，建议切换到 S 层。

## <a name="see-also"></a>另请参阅

* [什么是文本分析 API](../overview.md)
* [定价详细信息](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
