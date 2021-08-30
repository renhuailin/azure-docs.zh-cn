---
title: 在文本分析 v3 中指定模型版本
titleSuffix: Azure Cognitive Services
description: 了解如何指定用于数据的文本分析 API 模型。
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: article
ms.date: 06/17/2021
ms.author: aahi
ms.openlocfilehash: d1804505a73be2db8d7088caf74063381fa0ba33
ms.sourcegitcommit: 0046757af1da267fc2f0e88617c633524883795f
ms.translationtype: HT
ms.contentlocale: zh-CN
ms.lasthandoff: 08/13/2021
ms.locfileid: "121735542"
---
# <a name="model-versioning-in-the-text-analytics-api"></a>文本分析 API 中的模型版本控制

通过版本 3 的文本分析 API，你可以选择用于你的数据的模型版本。 在 API 请求中使用可选的 `model-version` 参数选择一个模型版本。 例如：`<resource-url>/text/analytics/v3.0/sentiment?model-version=2020-04-01`。 如果未指定此参数，API 会默认为最新的稳定版。 

## <a name="available-versions"></a>可用版本

使用下表来查找每个托管终结点所支持的模型版本。


| 终结点                        | 支持的版本                                     | 最新版本 |
|---------------------------------|--------------------------------------------------------|----------------|
| `/sentiment`                    | `2019-10-01`, `2020-04-01`                             | `2020-04-01`   |
| `/languages`                    | `2019-10-01`, `2020-07-01`, `2020-09-01`, `2021-01-05` | `2021-01-05`   |
| `/entities/linking`             | `2019-10-01`, `2020-02-01`                             | `2020-02-01`   |
| `/entities/recognition/general` | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2021-01-15`,`2021-06-01`  | `2021-06-01`   |
| `/entities/recognition/pii`     | `2019-10-01`, `2020-02-01`, `2020-04-01`,`2020-07-01`, `2021-01-15`  | `2021-01-15`   |
| `/entities/health`              | `2021-05-15`                           | `2021-05-15`   |
| `/keyphrases`                   | `2019-10-01`, `2020-07-01`, `2021-06-01`  | `2021-06-01`   |


可以在[新增功能](../whats-new.md)中找到有关这些模型的更新的详细信息。

## <a name="extractive-summarization"></a>抽取式摘要

从 `version 3.1-preview.1` 开始，可以使用异步 `analyze` 终结点提供抽取式摘要。 

当前模型版本为 `2021-08-01`

## <a name="text-analytics-for-health"></a>运行状况文本分析

[运行状况文本分析](../how-tos/text-analytics-for-health.md)容器使用与上述 API 终结点不同的模型版本控制。  请注意，每个容器映像仅有一个可用的模型版本。

| 终结点                        | 容器映像标记                     | 模型版本 |
|---------------------------------|-----------------------------------------|---------------|
| `/entities/health`              | `3.0.016230002-onprem-amd64` 或更高版本            | `2021-05-15`  |
| `/entities/health`              | `3.0.015370001-onprem-amd64`            | `2021-03-01`  |
| `/entities/health`              | `1.1.013530001-amd64-preview`           | `2020-09-03`  |
| `/entities/health`              | `1.1.013150001-amd64-preview`           | `2020-07-24`  |
| `/domains/health`               | `1.1.012640001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012420001-amd64-preview`           | `2020-05-08`  |
| `/domains/health`               | `1.1.012070001-amd64-preview`           | `2020-04-16`  |


## <a name="next-steps"></a>后续步骤

* [文本分析概述](../overview.md)
* [情绪分析](../how-tos/text-analytics-how-to-sentiment-analysis.md)
* [实体识别](../how-tos/text-analytics-how-to-entity-linking.md)
